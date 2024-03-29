---
title: "End-to-End Type Safety with .NET7, TypeScript, and OpenAPI -- with Hot Rebuild"
description: "Supercharge your dev workflow with end-to-end type safety and hot rebuild!"
pubDate: "2023 Oct 10"
socialImage: "/public/img/dn7-openapi/service-mapping.png"
slug: "2023/10/end-to-end-type-safety-with-dotnet7-webapis-typescript-openapi"
tags: ".net,typescript,openapi"
---

----

![Watch build](/public/img/dn7-openapi/watch-build.gif)

If you're building TypeScript frontend solutions with a .NET backend or you have a setup where you need to have the same models in some TypeScript code (e.g. same model definitions in a TypeScript serverless function), then you've come to the right place!

I've previously written about [how to set up OpenAPI TypeScript client generation with .NET 6](https://chrlschn.medium.com/net-6-web-apis-with-openapi-typescript-client-generation-a743e7f8e4f5).  The key benefit is that we can generate a full set of strongly typed clients and models for our frontend based on our backend data models.  But one problem with this approach is that if we want to have that sweet `dotnet watch` hot reload on the backend API, we can't generate the updated client unless we:

1. Trigger rebuild ("rude edit")
2. Stop the watcher and restart it

Just rebuilding it won't work properly as it would attempt to write the binaries to the same target.

Ideally, we can keep the API service running since we're only updating the generated TypeScript client bindings.  If we can keep the backend service running and the client binding generation causes the frontend to reload, it won't error if our service is stopped.

For this to work, we'll need a bit of simple -- but perhaps not-so-obvious -- configurations.

> 💡 Full repo here: https://github.com/CharlieDigital/dn7-openapi-codegen

---

## Setting Up the Frontend

On the frontend, we are setting up a basic directory structure just for the purposes of this sample.

When setting this up for your own use, you would initialize your application frontend first (e.g. React, Vue, Svelte, or Angular project) and then decide where you want the generated API bindings to go.

In our case, our bindings will go into `/app/src/_api`.

Once this is set up, run the following:

```bash
# Add the codegen tooling
cd app
yarn add openapi-typescript-codegen
```

Then set up the codegen script in `package.json`

```json
{
  "name": "app",
  "version": "1.0.0",
  "main": "index.js",
  "license": "MIT",
  "dependencies": {
    "openapi-typescript-codegen": "^0.25.0"
  },
  "scripts": {
    "gen": "npx openapi --input ./src/_api/schema-api.json --output ./src/_api/ --client axios --useUnionTypes"
  }
}
```

---

## Setting Up the Backend

The backend setup is where it gets interesting.

We want to add two post-build actions:

1. Generate the OpenAPI schema
2. Generate the TypeScript client bindings

Before we do that, we need to initialize the .NET tool manifest and install the Swagger CLI tooling:

```bash
# Initialize the tool manifest
cd backend
dotnet new tool-manifest

# Install Swagger CLI tooling
dotnet tool install swashbuckle.aspnetcore.cli
```

Now that we have the tooling, we need to update the `.csproj` file with a post-build action:

```xml
<!-- This sesction is a set of post-build commands -->
<Target
  Name="Swagger"
  AfterTargets="Build"
  Condition="$(Configuration)=='Gen' Or $(GEN)=='true'">
  <Message Text="Generating OpenAPI schema file." Importance="high" />
  <!-- Restore the tool if needed -->
  <Exec Command="dotnet tool restore" />

  <!-- (1) Generate the external API. -->
  <Exec
    Command="dotnet swagger tofile --output ../app/src/_api/schema-api.json $(OutputPath)$(AssemblyName).dll v1"
    EnvironmentVariables="ASPNETCORE_ENVIRONMENT=Development"
    WorkingDirectory="$(ProjectDir)" />

  <!-- (2) Generate TS bindings for the web app -->
  <Exec Command="yarn gen" WorkingDirectory="../app" />
</Target>
```

The script performs two main actions:

1. Generate the OpenAPI schema `.json` file using the Swagger CLI and output it to our frontend app folder
2. Use the generated OpenAPI schema file to generate the TypeScript client and bindings in our frontend app using `openapi-typescript-codegen`

There are two special things we've added to this build script:

1. A condition to only run this script if the `Configuration = 'Gen'`
2. Or if the `GEN` environment variable is `true`

These two conditions serve different purposes.  The first is to allow us to have two output directories since each build configuration maps to a different output directory.

Why is this important?  If we want to run `dotnet watch` for hot reload of our API, that will prevent us from generating the schema and clients unless we stop the application first.  Ideally, we can keep hot reload *and* be able to update the client.

The second condition allows us to include the build in CI/CD if we want to generate it as part of our `Release` build.

Finally, in the main `PropertyGroup`, we'll add a declaration to get the XML comments which will be pulled into the client schema:

```xml
<PropertyGroup>
  <TargetFramework>net7.0</TargetFramework>
  <Nullable>enable</Nullable>
  <ImplicitUsings>enable</ImplicitUsings>
  <!-- This gives us comments in our schema -->
  <GenerateDocumentationFile>true</GenerateDocumentationFile>
</PropertyGroup>
```

In our code, we need to set up the Swagger pipeline to pull in the comments:

```csharp
// [Program.cs]
// Set up the OpenAPI spec generation on build.
builder.Services.AddSwaggerGen(spec => {
  var filePath = Path.Combine(AppContext.BaseDirectory, "backend.xml");

  spec.IncludeXmlComments(filePath);
  spec.DescribeAllParametersInCamelCase();
  spec.SupportNonNullableReferenceTypes();
});
```

Now we're ready to roll.

---

## Using This Configuration

To make it easier to use this configuration, we'll add two scripts to the `backend` for convenience:

```bash
# _watch.sh
dotnet watch --non-interactive

# _gen.sh
dotnet watch build --non-interactive -- --configuration Gen
```

*(It's easy to write the equivalent batch or PowerShell scripts and you can even script them to run together.)*

The first is simply a script to initiate hot reload on our API with the `--non-interactive` flag.

The second does the same but instead of hot reloading our code, it generates a new schema instead!  The `--configuration Gen` is key: this directs the output to a different build directory:

```
/backend
  /bin
    /Debug/net7.0 <---- Where the runtime build goes
    /Gen/net7.0   <---- Where the code generation build goes
/app
  /src
    /_api
      schema-api.json <---- Generated by Swagger CLI on build
      index.ts        <---- Generated by openapi-typescript-codegen (yarn gen)
      /core                |
      /models              |
      /services       <----
```

By using a separate configuration for our `watch build`, we can now rebuild while hot reload is active since we're targeting a different output target.

Now run each in a separate terminal window and you have hot reload for your API as well as your TypeScript client/model generation!

*(A special note: `dotnet watch build` is broken with the preview version of .NET 8 that I have installed (`8.0.100-preview.7.23376.3`) so the `global.json` file is necessary to point to a stable release version where `dotnet watch build` does work correctly.  To see which versions you have locally, run `dotnet --list-sdks`.)*

---

## Output

If all goes well, you'll have the following output generated for your services:

![Service bindings](/public/img/dn7-openapi/service-mapping.png)

And your models:

![Model bindings](/public/img/dn7-openapi/type-mapping.png)

Note how nicely everything is translated into the TypeScript bindings -- comments included.

In usage, you can see that we get strong typing through the stack, full intellisense support, and even the comments from the backend!

![Screen capture](/public/img/dn7-openapi/typescript-svc.gif)

If you add Entity Framework to this stack, it's easy to achieve end-to-end type safety all the ways from your database to your frontend clients.

Having all of this with hot reload on top of it makes developing web APIs and frontends with .NET a very productive DX!