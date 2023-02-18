---
title: "The Boomer .NET Dev Skill Upgrade Guide — Part 2"
description: "The second part of my guide for how .NET developers need to re-orient in the modern dev landscape."
pubDate: "2022 May 08"
socialImage: "/public/img/dotnet-skills-1/dnskills-3.webp"
slug: "2022/05/boomer-dotnet-dev-skill-upgrade-guide-part-2"
tags: ".net"
---

----

![](/public/img/dotnet-skills-1/dnskills-3.webp)

*This two part musing reflects the conversations I’ve had with some good friends and former teammates of mine as well as reflections on some of my own observations over the last few years as I prepare to join one of the few Bay Area VC-backed startups using .NET on the backend.*

Side note: one of the bits of feedback I received on [the first part](/blog/2022/05/boomer-dotnet-dev-skill-upgrade-guide-part-1) is that the use of “boomer” only serves to perpetuate ageism in the software industry. While I am sensitive to this, I see it as a mindset and not a physical trait. The phrase “boomer” refers to someone who has a strong attachment to the status quo, or a feeling of being behind the times, or would even rather regress to “the good old days”. Very often, *it’s a result of working in a particular environment, team, or management style rather than a trait of the individual*.

----

In [Part 1](/blog/2022/05/boomer-dotnet-dev-skill-upgrade-guide-part-1), I talked about just how much the landscape of the tech stack has changed since the hay days of .NET Framework. In Part 2, I want to focus on the development environment and tooling.

There’s a lot to unpack, but the key is that modern .NET is not a Windows only stack and often it isn’t even *Windows first*. Much of this is due to the rise of containerization and serverless workloads in its various modes.

Adapting to this shift means that developers need to be versed in Unix-like operating systems.

Here I present to you the learnings of a boomer .NET developer, Part 2:

----

## macOS and VS Code

Prior to the start of 2021, I — like most .NET devs — worked within the comfy confines of Visual Studio Professional on Windows and all was right with the world. But at the the start of 2021, I committed to a switch to VS Code which, little did I know, would set me on a path to macOS.

This wasn’t a partial switch, this was a full and hard commit. Henceforth, I would write every line of code in VS Code.

Part of it was that I wanted to see if it was possible. Part of it was that I was already doing most of my front-end Vue SPA work in VS Code which has great tooling for JavaScript, TypeScript, and front-end development in general.

I found myself working much more intimately with the command line interface and the `dotnet` CLI. The good news is that the CLI has come a long way and it’s easy to work quickly in .NET from the CLI these days.

![](/public/img/dotnet-skills-1/dnskills-4.gif)
*Starting and testing a simple .NET minimal web API from the command line. Gone are the days of the clunky msbuild interface.*

There were some initial struggles with learning the quirks of OmniSharp and C# in VS Code. *But the key outcome of this period was that I was no longer dependent on VS for anything*; I could do pretty much everything I needed to from the command line and VS Code (your mileage may vary).

<blockquote>
Gone are the days of the clunky msbuild interface
</blockquote>

Don’t get me wrong: one really cannot compare VS Code to a fully decked out IDE like Visual Studio, but the question is how much of that full feature set gets used on a day-to-day basis.

That decoupling from Visual Studio and Windows was a critical leap as the primary development platform for every startup I interviewed with was macOS, not Windows. Not having to be bound by Visual Studio meant that I was no longer bound by Windows; I fully committed to switching over to macOS.

[I’ve written about my experiences with working on Apple M1 hardware](https://chrlschn.medium.com/dev-diaries-net-development-on-a-macbook-pro-m1-75359c25b697) and if your workload allows, I absolutely recommend it for the performance, battery life, and absolutely silent operation. As much as I prefer Windows as a daily use OS, the Apple hardware is just that much better.

But more importantly, it prepared me to work with .NET running in Linux containers.

*(Side note: JetBrains Rider is also a great IDE if you don’t want to deal with the quirks of OmniSharp)*

## Working Knowledge of Linux

The reason this is critical is that the way the world deploys and scales software has shifted *mightily* towards containerized workloads. For those workloads, Linux is significantly more cost and resource effective than Windows.

From a development perspective, it’s less important to think of it as an OS rather than as a low-cost, lightweight runtime container for your application.

Because macOS is itself a Unix-based OS, shell scripting between macOS and Linux is more or less portable, making it a bit easier to work in macOS than working with WSL on Windows.

The simple act of shell scripting and working with the command line in macOS will prime you for basic Linux interaction when setting up and working with Linux containers. Otherwise, [Microsoft’s .NET Runtime Linux containers](https://hub.docker.com/_/microsoft-dotnet/) are already configured for .NET and generally involves minimal Linux knowledge.

And that’s it 🤣. Really.

## Get Comfortable with the Command Line (vi, git, npm, yarn, and more!)

That brings us back to the command line and it is absolutely critical to get comfortable with it.

Part of it is that all of the tools for building modern front-ends are command line based. Whether you’re using `npm` or `yarn`, you’ll end up being quite intimate with the command line.

Part of it is that many of the same commands used during development are needed for packaging and deployment in continuous integration whether it’s GitHub Actions or GitLab CI or Azure Pipelines.

Part of it is that the most productive way of interacting with the cloud services is typically via the CLI.

Part of it is that you’ll occasionally have to troubleshoot an errant container and your only mode of interaction is via the command line. That container has no GUI, so you’ll need to be comfortable with any one of the available console text editors like `vi` or `vim`. If you need to check or update a config file in the container, you’ll need basic operational `vi` (not every distribution comes with `vim`, but I have not encountered one *without* `vi`).

Mostly you can get by with knowing how to:

* Open a file
* Make an edit
* Find and replace
* Save the file and exit
* Exit without saving

Get familiar with some of the key commands like `cat`, `grep`, `rm`, `sed`, and a handful of others.

That’s it! For the most part, the only reason to interact with Linux is to troubleshoot and occasionally modify a file so getting comfortable with a few key command line tools is really critical. [With the maturation of serverless containers as a service](https://chrlschn.medium.com/thoughtworks-misses-the-mark-re-serverless-vs-kubernetes-9901d1b0f0c6) that can pull code directly from GitHub and deploy on your behalf, you can now deploy containerized workloads on Linux *without even interacting with Linux in many cases*.

## Docker, Docker, Docker

<blockquote>
“Docker is used by millions of developers around the world to build, run and share containerized applications. Currently, 55% of professional developers are using Docker at work” — TechRepublic
</blockquote>

The recent changes to Docker’s licensing [have some folks seeking alternatives](https://blog.logrocket.com/top-docker-alternatives-2022/), but Docker still remains one of the most widely utilized and most accessible tools for working with containers.

So perhaps the right heading should be *“containers, containers, containers”* as it is the present reality of deploying applications in the cloud. Even serverless functions are but code shoved into specialized containers and launched on your behalf.

My entrypoint to working with Docker was actually [Dapr](https://dapr.io/) which I had started to play around with it in late June of 2021 as I was evaluating it for an app I was working on. The Dapr team publishes a reference eShopOnDapr application: [dotnet-architecture/eShopOnDapr](https://github.com/dotnet-architecture/eShopOnDapr)

I found the sample eShopOnDapr reference application filled with too much “wand waving” and decided that I wanted to break down every layer of how to set up a full Dapr application from scratch and ended up writing a series of blog posts over the course of a week (the GKE one came much later):

* [Dapr and Azure Functions: **Part 1 — Hello World**](https://charliedigital.com/2021/07/01/dapr-and-azure-functions-part-1-hello-world/)
* [Dapr and Azure Functions: **Part 2 — Azure Functions Containerization in Docker**](https://charliedigital.com/2021/07/02/dapr-and-azure-functions-part-2-containerization-docker/)
* [Dapr and Azure Functions: **Part 3 — Containerizing with Dapr**](https://charliedigital.com/2021/07/02/dapr-and-azure-functions-part-3-containerizing-with-dapr/)
* [Dapr and Azure Functions: **Part 4— Deploy via Kubernetes**](https://charliedigital.com/2021/07/06/dapr-and-azure-functions-part-4-deploy-via-kubernetes/)
* [Dapr and Azure Functions: **Part 5a — Deploying to AWS with ECR and EKS Fargate**](https://charliedigital.com/2021/07/07/dapr-and-azure-functions-part-5a-deploying-to-aws-with-ecr-and-eks-fargate/)
* [Dapr and Azure Functions: **Part 5b — Deploying to Azure with ACR and AKS**](https://charliedigital.com/2021/07/08/dapr-and-azure-functions-part-5b-deploying-to-azure-with-acr-and-aks/)
* [Dapr and Azure Functions: **Part 5c — Deploying to Google with GKE**](https://charliedigital.com/2021/10/30/dapr-and-azure-functions-part-5c-deploying-to-google-with-gke/)

Building this up from scratch helped me get a handle on the fundamentals of:

* Docker
* Docker Compose (really useful)
* Kubernetes (less useful and probably an egregious example of YAGNI for most teams and use cases)
* Working with the container registries and Kubernetes offerings on each of the cloud providers

I recommend this exercise as taking a Dapr solution from scratch all the ways to Kubernetes deployment will provide you with a really good baseline for understanding several key technologies as well as the fundamentals of networking with Docker Compose and Kubernetes. The entire exercise cost under $2 (just be sure to clean up your resources!).

*Side note: **containers != Kubernetes**. I strongly recommend that you get to know Kubernetes, but generally avoid it unless it is necessary for your application architecture (e.g. you need to run a server handling web sockets). Every cloud provider now has mechanisms to run container workloads without the overhead of Kubernetes. [Azure Container Apps abstracts the underlying orchestration and still allows a Dapr sidecar architecture](https://docs.microsoft.com/en-us/azure/container-apps/microservices-dapr?tabs=bash).*

You’ll also get really hands on with the CLIs of each of the cloud providers and get a feel for the platforms (I’ve come to really like GCP).

In the end, the key takeaway for me is just how awesome working with containers can be for packaging, shipping, and running applications. The current offerings of serverless containers — [Google Cloud Run](https://cloud.google.com/run), [AWS App Runner](https://aws.amazon.com/apprunner/), and [Azure Container Apps](https://azure.microsoft.com/en-us/services/container-apps/) — make it so streamlined to deploy code to the cloud and mitigates many of the shortcomings and restrictions of working with serverless functions while offering many of the benefits like automatic scaling and scale to zero.

## Serverless and Cloud

There is no question by now that cloud is the present and future; it’s simply a matter of time and every developer needs to have hands on experience working with the cloud.

I personally think that one of the biggest shifts in the last two years is the rise of containers-as-a-service (as opposed to Kubernetes-as-a-service) as this provides the ability to run serverless workloads that scale to zero without having to do much more than listening for incoming requests on a known port. Write code in whatever programming language, whatever application server — as long as you can package it in a container, you can run it serverless in the cloud!

Deploy your React or Vue front-end as a set of static assets to any of the blob storage services and boom! Instant web app!

The good news is that all three platforms offer plentiful credits to start with. But to be honest, even running applications full time on these platforms costs less than $1/month if you choose the right technologies with consumption based pricing. CosmosDB is a great example with [a serverless offering](https://docs.microsoft.com/en-us/azure/cosmos-db/serverless) as well as a free tier.

I run a Google Cloud Run app ([the live instance](https://storage.googleapis.com/dn6-mongo-web/web/dist/index.html) of my sample [dn6-mongo-react-valtio](https://github.com/CharlieDigital/dn6-mongo-react-valtio) repo) with a free tier MongoDB backend that handles at least a request every 2 hours (scheduled reset) for $0.02/month!

![](/public/img/dotnet-skills-1/dnskills-5.webp)
*No excuses! It’s functionally free to learn.*

Having worked with all three cloud vendors now, I do think that Google Cloud is perhaps the easiest to work with and recommend it as an entry point (AWS being the most complicated and least ergonomic, IMO). In particular, Google Cloud Run makes it really easy to take existing skills with .NET and build applications for serverless containers without much fuss — literally just slap in a `Dockerfile` to your existing web API and you have a super scalable serverless web API.

----

I like to think of this as ***The End of the Beginning***:

<blockquote>
Avon the snail and Edward the ant are back for another funny adventure. And adventure, he has heard, is the key to a happy life. So with his new friend Edward the ant, Avon sets out on a journey to find the excitement his life has been missing.
</blockquote>

![](/public/img/dotnet-skills-1/dnskills-6.webp)
*A really great book if you have young kids!*

For me, it has certainly been an adventure moving on from .NET Framework and even .NET Core to .NET 6, Node, Docker, serverless, and cloud.

In truth, I feel that I’ve barely scratched the surface with these two articles and there are certainly many topics I’ve left out (*Next.js, Nuxt.js, Nest.js, SSR, SSG, WebAssembly, GitHub Actions and CI/CD in general, mobile, Electron, Cypress, Playwright, Jest, WebSockets, gRPC, GraphQL, and more*), but I’ve tried to focus on the real core set of learnings from my own journey.

I hope that these two articles have provided you perspective and the outlines of a roadmap for you to begin your own adventures and journeys into the future of .NET and application development!

If you liked these two articles, subscribe for more brain dumps on .NET, serverless, and software engineering!