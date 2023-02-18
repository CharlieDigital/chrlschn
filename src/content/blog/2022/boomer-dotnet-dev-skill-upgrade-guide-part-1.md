---
title: "The Boomer .NET Dev Skill Upgrade Guide — Part 1"
description: "The first part of my guide for how .NET developers need to re-orient in the modern dev landscape."
pubDate: "2022 May 01"
socialImage: "/public/img/dotnet-skills-1/dnskills-1.gif"
slug: "2022/05/boomer-dotnet-dev-skill-upgrade-guide-part-1"
tags: ".net"
---

----

![](/public/img/dotnet-skills-1/dnskills-1.gif)
*Walking into a Bay Area startup on day 1 as a 40 year old individual contributor.*

*This two part musing reflects the conversations I’ve had with some good friends and former teammates of mine as well as reflections on some of my own observations over the last few years as I prepare to join one of the few Bay Area VC-backed startups using .NET on the backend.*

----


Stating that the development landscape has changed quite significantly in the last few years would be an understatement. If you’re a boomer developer like me, it can feel daunting walking into uncharted territory if you’ve been doing .NET Framework development for the last decade without keeping a pulse on the rest of the market and continuously upgrading your skills.

I personally know a few devs who have reached out and now find themselves seeking direction in an engineering landscape that has shifted quite dramatically since they first started with the .NET Framework.

For myself, I started the shift out of .NET Framework around 2018 when I led the transition from an on-premise product to .NET Core running on Azure Functions with a Vue front-end and CosmosDB database. I have found the journey rewarding on some fronts (serverless!) and a bit frustrating on others (I really, *really* dislike React — more on this in a bit).

I’ve had the opportunity to observe how software is being developed in an AR/VR startup that reached a $100m Series C funding round to a cybersecurity startup that overextended on Kubernetes to a YCombinator startup that just reached $3m ARR and will likely land a multi-million dollar Series B.

Along the way, I’ve had the opportunity to interview with many other YC and VC backed startups (sometimes successfully, other times coming up short quite miserably).

Here I present to you the learnings of a boomer .NET developer in two parts.

----

## Embrace .NET 6

It’s not worth spending too much time expanding on this because it’s a given: **you’ll need to embrace .NET 6**. And I mean this in the fullest from the minimal APIs to the command line tooling; throw away your insecurities, prejudices, and step outside of your comfort zone.

I’ll touch on this a bit more later in part 2 — when we talk about Linux, Docker, and VS Code — but it is absolutely imperative that you start with the right mindset because the landscape of .NET and .NET development has changed so dramatically, that you cannot progress as a .NET developer if you can’t let go.

The now and future of .NET is multi-platform — not Windows only and often *not even Windows first*.

## Learn TypeScript and Either Python or Go

You do not have an option. You have to learn TypeScript and the TypeScript toolchain. While the Blazor ecosystem and adoption is growing, your options are going to be the broadest as a full- or part-time full-stack developer — enterprise or startup — if you can work with TypeScript on both the front- and back-end.

In almost every VC-backed startup I interviewed with and every one that I worked at, TypeScript on Node was the core of the backend or the company was transitioning JavaScript to TypeScript (with a few outliers using Swift, Python, and Reason on the backend). TypeScript is universally used on the front-end.

In some cases, some workloads are simply going to make more sense with TypeScript. For example, some workloads like E2E testing, CLI/tooling development, local scripting are just easier and more ergonomic in TypeScript than C#.

I cannot recommend [Adam Freeman’s Essential TypeScript 4](https://www.amazon.com/Essential-TypeScript-4-Beginner-Pro/dp/148427010X) more heartily. It’s not just a great technical book for TypeScript, but its first two chapters break down JavaScript concisely and each chapter thereafter starts with an outline of the shortcomings of JavaScript at scale and how TypeScript addresses those gaps. This is one of *the best* language specific technical books I’ve ever read and completely worth it to pick it up over just perusing the docs.

The good news is that TypeScript — in many ways — is a somewhat gentle transition if you are comfortable with C#; it’s very easy to be a competent TypeScript developer if you are already a competent C# developer. Check out my small repository highlighting the congruence between TypeScript and C#: [CharlieDigital/js-ts-csharp](https://github.com/CharlieDigital/js-ts-csharp)

You can also see how similar the two languages are in my earlier article [Structural Control Flow with Object-Oriented Programming](https://medium.com/codex/structural-control-flow-with-object-oriented-programming-part-2-7d18526146de) where I demonstrate patterns that wouldn’t feel out of place in C#.

While TypeScript alone is probably sufficient, one of Python or Go should be added to your repertoire to broaden your options. If you’d like to get into ML/AI, it’s a given that Python is a necessity these days.

## React Sucks, but You Have to Learn It

Let’s get this out of the way: I *hate* React with a passion. It sucks. It’s terrible, verbose, ugly, and easy to do poorly. In so many ways, it reminds me of classic ASP or PHP with the intermingling of layout and logic. On top of that, the React runtime is exceedingly simple; if you want to understand more, [this write-up will help you understand the core nature of React and how it forces developers to work harder](/blog/2023/02/using-usememo-and-usecallback-react-langoliers). React is the exact opposite of a good tech choice to build on *because it’s hard to do right and easy to do wrong*. I’ve seen it done wrong so many times by different teams that all I can conclude is that it has to be the nature of React.

But you have no choice: you have to know the basics of working with React. It has become the default because it dominates the market despite its many flaws.

Here’s a full-stack repo showing how to work with a .NET 6 backend and React with Valtio frontend (sorry, I can’t quite bring myself to embrace the stench of Redux): [CharlieDigital/dn6-mongo-react-valtio](https://github.com/CharlieDigital/dn6-mongo-react-valtio).

On the other hand, Vue is much easier to get right and do well. Part of it is that the ecosystem ships with sensible and easy to use defaults. Part of it is that the defaults use easier paradigms (e.g. proxy-based state). Part of that is because its ecosystem is simply more cohesive while also having the benefit of taking the best parts of React and Angular.

You may be able to get away with only Vue — which is much more sane. Most companies hiring for full stack devs will be fine with hiring for Vue experience, even if their stack is React. But beyond trivial examples, they are fundamentally very different approaches to building front-ends and managing state.

## Get Hands-on with Modern Front-end Tooling

While much of the existing front-end workflows are using webpack, there is an increasing shift towards [Vite](https://vitejs.dev/) and it will continue to grow due to how streamlined it makes the development experience.

Thankfully, I find Vite significantly easier to work with than webpack. Similar to Vue — also from the mind of Evan You — Vite ships with sensible defaults which make it easy to work with the toolset. Whether you’re working with React, Vue, or Svelte, Vite is the way to go.

While I do still occasionally pine for the *“good ol’ days”* and simplicity of working with vanilla JS, hot reload with Vite is just *so good*.

## Wrap Your Mind Around NoSQL Paradigms

![](/public/img/dotnet-skills-1/dnskills-2.gif)
*DB Rankings showing the steady rise of MongoDB and Postgres.*

[While relational databases such as Postgres, MySQL, SQL Server, and Oracle aren’t going anywhere](https://db-engines.com/en/ranking), the startup world — and increasingly enterprise — is dominated by NoSQL.

The reasons vary from cost and the advantages of consumption based pricing for startups to the lower object-relational friction when working with JSON serialized objects for less experienced developers.

Solutions such as Firestore and CosmosDB promise functionally unlimited capacity without the overhead of managing infrastructure and planning complex sharding strategies — as long as you know what you’re doing.

Rather than spending time learning n number of databases, your time is best spent around understanding the different paradigms that are most common and how to think about building solutions using said paradigms and play around with a handful of them:

* **Document-oriented databases** such as MongoDB, Firestore, CosmosDB, and AWS DocumentDB are great places to start. Document-oriented databases solve the common problem of object-relational mismatch, but these databases present unique data modelling challenges as well. While its often easy to get started with these databases, where teams usually run into issues is that the ease of use usually results in bad practices that won’t scale (or are too costly to scale). The good news is that each of these have free or functionally free tiers that you can get started with.
* **Key-value and wide column store databases** such as DynamoDB, Cassandra, and Redis. These databases are commonly used when huge, flat datasets are expected.
* **Graph databases** such as Neo4j and AWS Neptune. While graph is still a niche, having familiarity with the use cases can help unlock opportunities due to the niche nature of the databases. There have been several opportunities that opened up based on my knowledge of and extensive experience with Neo4j.
* **Analytical data analysis tools** such as Apache Spark, AWS Kinesis, and Azure Stream Analytics are also key tools to understand as the scale and scope of data as well as the expected gap between ingest and insight gets smaller and smaller.

For hands on work, I strongly recommend getting comfortable with:

* **MongoDB**. This still seems the go-to for many startups because of the low object-relational friction that can be hard for young teams to overcome. It is also seen as “safe” because it prevents cloud vendor lock in versus Firestore or CosmosDB, for example.
* **Postgres**. On the relational side, this is the database that I find startups gravitating towards. Get familiar with either [Prisma](https://www.prisma.io/) or [TypeORM](https://typeorm.io/) as these are the most common ORMs for relational data access with TypeScript.

----

In part 1, we’ve looked at how the technology landscape has changed and some of the key technologies that .NET developers need to soak up. While there is a growing market for Blazor (WASM is still maturing) and still a market for ASP.NET MVC, becoming comfortable with the tech stack dominating the landscape is key to unlocking more opportunities.

My suggestion for anyone preparing to make this transition is to build small projects in GitHub and just have fun while venturing outside of your comfort zone. Deploying serverless apps into Azure, AWS, or Google Cloud is functionally free for all intents and purposes (I pay under $1/month total for my various projects running on Azure, AWS, and Google Cloud) so there’s no excuse for not diving in.

In [part 2](/blog/2022/05/boomer-dotnet-dev-skill-upgrade-guide-part-2) (subscribe or follow to get updates!), we’ll take a closer look at the development environment and tooling and my suggestions if you want to have access to the most opportunities in this shifting market.