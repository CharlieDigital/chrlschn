---
title: "Programmatically Invoke Cloud Run Jobs with Runtime Overrides"
description: "Love Google Cloud Run but need to be able to programmatically invoke long running jobs?"
pubDate: "2023 Sep 24"
socialImage: "/public/img/cloud-run-invoke/cloud-run-overrides.png"
slug: "2023/09/programmatically-invoke-cloud-run-jobs-with-overrides"
tags: "cloud,docker,gcp"
---

----

If you're like me, then you're a big fan of Google Cloud Run.  Why? You can run pretty meaty workloads for free; a perfect choice for hobbyists, weekend hackers, indie developers, and small shops.  With just a small `Dockerfile`, you're ready to go.

<iframe style="aspect-ratio: 16/9; width: 100%;" src="https://www.youtube.com/embed/GlnEm7JyvyY?si=grqGvUkGGXIrmd-0" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

But Cloud Run is not without its limitations. Specifically, Cloud Run Services have a 60 minute timeout limit and isn’t suited to run *asynchronous processing jobs*. It’s possible to do the latter using Pub/Sub push, but that has a *fixed* acknowledgement deadline of a mere 10 seconds so if you need anything more than 10 seconds, it’s not going to work.

This is where ***Cloud Run Jobs*** come into the picture.

Not only does it meet our needs, its free tier grant is even more generous than Cloud Run Services!

![Cloud Run Pricing](/public/img/cloud-run-invoke/cloud-run-free.png)

Hot dang! If we can move some of our heavier, longer running workloads into Jobs, *we get more free credits*!

As a digression, part of the fun of these cloud architectures is being able to figure out ways to leverage these services to run your workloads for free.  This is such a case where an alternate solution is to run a Compute Engine instance or run GKE Autopilot.  But if we want to run it for free, we need a fully serverless way of doing this.

Unlike Cloud Run Services, Cloud Run Jobs can run up to 24 hours and run asynchronously without external input.  Skimming the docs, it’s not obvious if it’s possible to dynamically pass parameters to a job run and kick it off programmatically.  [In fact, the documentation shows that this is possible using the console or the command line CLI](https://cloud.google.com/run/docs/execute/jobs#override-job-configuration), but there are no client SDKs to achieve the same.

![Console overrides](/public/img/cloud-run-invoke/cloud-run-console-override.png)

Why do you need overrides?  A simple use case is that you have a long running job that you want to trigger programmatically and pass it some parameters like the ID of a set of records to process or a path to a Storage bucket to process.  The overrides allow us to inject entry point arguments and environment variable overrides that are specific to the job invocation.

With this, now we can pass in a reference to some data in our database, for example, and process that specific data.

Let's see how we implement and deploy programmatically invoked Cloud Run Jobs which can accept parameter overrides at the point of invocation.

---

## Overview

The overall structure will look like this:

![Overview](/public/img/cloud-run-invoke/cloud-run-overrides.png)

We'll create a simple, public-facing web service which will allow us to invoke an API endpoint to trigger the job with parameters.

> 💡 If you'd like to see the code first, [check out the Github Repo](https://github.com/CharlieDigital/gcr-invoke-job-overrides).

---

## Create the Job

The first task is to create the job definition.

Cloud Run Jobs are based off of a *definition* of the job that instructs the runtime about which container image to run, the environment variables to load, the number of instances, and so on.

In this app, we have a simple job that will just print out the environment variables and the entry point args to verify that we've received the overrides.

We're using a simple .NET 7 console app:

```csharp
// C#
var variables = Environment.GetEnvironmentVariables();

foreach (var key in variables.Keys) {
  Console.WriteLine($"{key} = {variables[key]}");
}

foreach (var arg in args) {
  Console.WriteLine($"  ARG: {arg}");
}
```

To run this in Cloud Run, we'll need a docker file for the job:

```bash
FROM --platform=$BUILDPLATFORM mcr.microsoft.com/dotnet/sdk:7.0 AS build-env
WORKDIR /app

ARG TARGETARCH
ARG BUILDPLATFORM

# Copy everything
COPY ./job/. ./
# Restore as distinct layers
RUN dotnet restore -a $TARGETARCH
# Build and publish a release
RUN dotnet publish -c Release -o published -a $TARGETARCH --no-restore

# Build runtime image
FROM mcr.microsoft.com/dotnet/runtime:7.0
WORKDIR /app
COPY --from=build-env /app/published .
ENTRYPOINT ["dotnet", "job.dll"]
```

The `build-deploy-job.sh` script will package and deploy the image into the artifact registry:

```bash
set -e

# PROJECT_ID=123 REPOSITORY=myrepo REGISTRY_REGION=us-central1 ./build-deploy-job.sh

docker buildx build \
  --push \
  --platform linux/amd64 \
  -t {REGISTRY_REGION}-docker.pkg.dev/{PROJECT_ID}/{REPOSITORY}/test-invoke-job \
  -f Dockerfile.job .
```

Note that I'm building from an M1 Mac so we'll need to create a cross-platform build.

Once we have the image, we can create the job definition:

```bash
gcloud run jobs create test-job \
  --image {REGISTRY_REGION}-docker.pkg.dev/{PROJECT_ID}/{REPOSITORY}/test-invoke-job:latest
```

---

## Create the API Invoker

Ideally, we want to invoke the job from some other code.  In my case, I have an API which processes a set of documents.  When I kick off the job, I want to pass in a *scope* -- think of it like a `tenantId` -- which defines which set of documents to process.

I spent quite a bit of time figuring out how to invoke this.  [A discussion on Stackoverflow](https://stackoverflow.com/questions/73561965/how-to-pass-parameters-to-google-cloud-run-job) was the first clue that this was possible.

As of this writing, the client libraries do not support this functionality.

In fact, [the documentation only shows how to do this from the console and command line](https://cloud.google.com/run/docs/execute/jobs#override-job-configuration).  However, [looking at the REST API](https://cloud.google.com/run/docs/execute/jobs#api), [it does allow sending parameters when triggering a job](https://cloud.google.com/run/docs/reference/rest/v1/namespaces.jobs/run):

```bash
curl -H "Content-Type: application/json" \
  -H "Authorization: Bearer ACCESS_TOKEN" \
  -X POST \
  -d '...' \
  https://REGION-run.googleapis.com/apis/run.googleapis.com/v1/namespaces/PROJECT-ID/jobs/JOB-NAME:run
```

To get this to work, we'll need two things:

1. Get the access token.
2. Build the request with the overrides.

### Getting an Access Token

From the container, we can [access the container metadata](https://cloud.google.com/run/docs/container-contract#metadata-server) to retrieve the token for the current user.

It's a simple `GET` request and the URL looks like so:

```
http://metadata.google.internal/computeMetadata/v1/instance/service-accounts/default/token
```

The C# code for this is simple:

```csharp
// C#
async Task<string> ResolveToken() {
  using var client = new HttpClient();

  // See: https://cloud.google.com/run/docs/container-contract#metadata-server
  client.DefaultRequestHeaders.Add("Metadata-Flavor", "Google");

  var response = await client.GetAsync(
    "http://metadata.google.internal/computeMetadata/v1/instance/service-accounts/default/token"
  );

  var token = await response.Content.ReadAsStringAsync();

  return token;
};
```

This will return the access token that we'll need to make the call to run the job.

### Build and Execute Request with Overrides

Once we have the access token, we can make the REST API call to run the job with overrides.

```csharp
// C#
var token = await ResolveToken();

var accessToken = JsonSerializer.Deserialize<Token>(token)!;

// See: https://cloud.google.com/run/docs/reference/rest/v1/namespaces.jobs/run
using var client = new HttpClient();

client.DefaultRequestHeaders.Add(
  HeaderNames.Authorization,
  $"Bearer {accessToken.access_token}"
);

var projectId = "YOUR_PROJECT_ID_HERE";
var job = "YOUR_JOB_NAME_HERE";
var region = "YOUR_RUNTIME_REGION_HERE"

await client.PostAsJsonAsync(
  $"https://{region}-run.googleapis.com/apis/run.googleapis.com/v1/namespaces/{projectId}/jobs/{job}:run",
  new RunRequest(
    Overrides: new Overrides(
      ContainerOverrides: new ContainerOverride[] {
        new(
          Name: "",
          // Entry point argument overrides 🎉
          Args: new string[] {
            "arg-1",
            "arg-2"
          },
          // Environment variable overrides 🙌
          Env: new EnvVar[] {
            new("ENV_1", "HELLO"),
            new("ENV_2", "WORLD")
          },
          ClearArgs: false
        )
      },
      TaskCount: 1,
      TimeoutSeconds: 10
    )
  ),
  options: new () {
    PropertyNamingPolicy = JsonNamingPolicy.CamelCase
  }
);
```

The `Dockerfile` for this is equally simple:

```bash
FROM --platform=$BUILDPLATFORM mcr.microsoft.com/dotnet/sdk:7.0 AS build-env
WORKDIR /app

ARG TARGETARCH
ARG BUILDPLATFORM

# Copy everything
COPY ./api/. ./
# Restore as distinct layers
RUN dotnet restore -a $TARGETARCH
# Build and publish a release
RUN dotnet publish -c Release -o published -a $TARGETARCH --no-restore

# Build runtime image
FROM mcr.microsoft.com/dotnet/aspnet:7.0
WORKDIR /app
COPY --from=build-env /app/published .
ENTRYPOINT ["dotnet", "api.dll"]
```

And to deploy:

```shell
set -e

# PROJECT_ID=123 REPOSITORY=myrepo REGISTRY_REGION=us-central1 RUNTIME_REGION=us-east4 ./build-deploy-api.sh

docker buildx build \
  --push \
  --platform linux/amd64 \
  -t {REGISTRY_REGION}.pkg.dev/{PROJECT_ID}/{REPOSITORY}/test-invoke-job-api-svc \
  -f Dockerfile.api .

# Deploy image
gcloud run deploy test-invoke-job-api-svc \
  --image={REGISTRY_REGION}-docker.pkg.dev/{PROJECT_ID}/{REPOSITORY}/test-invoke-job-api-svc:latest \
  --allow-unauthenticated \
  --min-instances=0 \
  --max-instances=1 \
  --region={RUNTIME_REGION} \
  --cpu-boost \
  --memory=256Mi
```

To confirm, we can review the log output from the job:

![Log](/public/img/cloud-run-invoke/cloud-run-logs.png)

Nice!

Now we have a way of kicking off long running jobs in Cloud Run programmatically while passing in parameters.

---

## Conclusion

With this simple setup, we are now able to not only programmatically kick off long running compute jobs and pass in parameters, but we also get a whole separate stack of compute credits!

![](/public/img/noice.gif)

If you made it this far, add this blog to your RSS reader of choice or tag me on Mastodon [@chrlschn](https://mastodon.social/@chrlschn) or X [@chrlschn](https://twitter.com/chrlschn).
