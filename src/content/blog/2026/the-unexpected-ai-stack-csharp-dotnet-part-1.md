---
title: "The Unexpected AI Stack: C# + .NET (Part 1)"
description: "Exploring an unconventional stack for building AI- and agent-enabled applications: C# and .NET"
pubDate: "2026 August 10"
socialImage: "/public/img/ai-sleeper-stack/netcore-csharp-aspire.png"
slug: "2026/08/the-unexpected-ai-stack-csharp-dotnet-part-1"
tags: "llms,ai,mcp"
---

----

## Summary

- C# and .NET as a stack are probably flying under the radar for many teams building new apps in the agentic era
- For teams already using legacy C# and .NET, it may not be clear how to best leverage the modern .NET stack tooling to speed up agentic development
- Aspire tooling provides agents a programmable, isolated runtime orchestration layer that improves agent autonomy and facilitates agentic patterns like worktrees and parallel development.
- CSharpRepl lets agents interact with an instance of *the running application* and manipulate the runtime state of the application including wrapping and replacing existing functions.
- Combined, this set of tooling gives agents more autonomy to iterate and build stable, correct, production-ready code that is proven in *runtime configurations* efficiently.

----

## Why C#?

In an age when agents can write code in any programming language, it's fair to ask: why have agents write code in C#?

Last year, I was a technical lead on the effort at Motion ($500m valuation, series C, post YCombinator startup, ~40 engineers) [to move the entire backend off of TypeScript + Node and shift to a combination of C# and F# for all future backends](https://engineering.usemotion.com/moving-off-of-typescript-e7bb1f3ad091).

The reasons are numerous and [I've written about this extensively before](https://typescript-is-like-csharp.chrlschn.dev/pages/intro-and-motivation.html), but it's worth summarizing this again in mid 2026 framed in the context of building in an agent-first reality:

- Build- *and* run-time type checks provide a foundational layer of safety and correctness to check agent outputs
- Clean, easy to understand build-time error traces; compare this to typical TypeScript type error stacks...
- Roslyn static analyzers that allow for custom static analysis for agent guardrails
- Roslyn source generators for terse runtime code to remove boilerplate and reduce context for agents on the read path
- Extensively and richly documented with broad first-party libraries means that LLMs have very good coverage in their training data. While Python and JavaScript may have *more representation*, the lack of a standard library and BCL means that there are many variants of how to accomplish the same task.
- EF Core (mature, powerful .NET ORM) provides build-time checks for database schemas and decreases the likelihood of runtime errors from schema changes.
- Mature, stable, *consistent*, easy to use tooling and tool chain.  Just `dotnet`.  Again, easy for agents to work with, minimizing the need for skills or explanatory text for the agents.

Aside from that, if developers are no longer writing code, why *not* choose a platform that has:

- Built-in primitives for in-process asynchronous coordination (`System.Threading.Channels`, Orleans actor model)
- Higher performance and throughput where it matters (boundary serialization for both JSON and [gRPC/Protobuf](https://github.com/LesnyRumcajs/grpc_bench/discussions/559), etc.)
- Better story around ecosystem security and dependency management (NPM vs Nuget)
- A whole professional organization continuously patching and fixing the platform and libraries

Given the benefits of adding both build and runtime type safety as well as a mature, well-documented, and broad first party platform, C# is a surprisingly strong choice for building real software with AI.

The objective of this two-part series is to shed some light on how C# and the .NET ecosystem is an ideal foundation for building production software with agents.

- **Part 1** (you are here) will introduce two under-the-radar components of the .NET stack that make it surprisingly amenable to building software with agents: Aspire and `CSharpRepl`.
- [Part 2]((https://chrlschn.dev/blog/2026/08/the-unexpected-ai-stack-csharp-dotnet-part-2/)) will dive into a hands-on implementation from the ground up with an open-source template for teams to build on top of.
- **Part 3** will implement the next layer of the application including a simple streaming interface to the Copilot SDK agent.
- **Part 4** will extend the application with Testcontainers to demonstrate how to simplify test execution for agents with stateless containers as well as telemetry for runtime visibility.

***The full repo***: <https://github.com/zeeq-ai/zeeq-tmpl>

----

## Encapsulating the runtime with Aspire

If you're using Docker Compose or [Tilt](https://tilt.dev/) for dev runtime orchestration, you may have occasionally wished that it was just a bit more *programmable*.  That's exactly the gap that [Aspire](https://aspire.dev/) fills: a programmable orchestration layer that makes it easy to build an isolated runtime stack that agents can control while building software.

The best analogy is to consider the difference between Terraform and Pulumi or CDK.  Terraform is a declarative tool for building infrastructure, while Pulumi and CDK are *programmable* tools that allows teams to build infrastructure with code.  Aspire is the same thing for orchestrating your runtime stack.

> Early iterations of Aspire were focused more on building distributed, microservices systems.  While Aspire is still great for that, it is even better as an agent-friendly runtime orchestration layer.

It has several key features that make it a foundational component of an agent-friendly software stack:

- It has a [built-in internal network loop](https://aspire.dev/fundamentals/networking-overview/) that isolates individual runtime stacks.  This is important for agent development and worktrees because this allows agents to run the full stack in isolation.
- It has a [built-in, queryable OpenTelemetry target](https://aspire.dev/fundamentals/telemetry/) that gives agents the richness to access not just logs, but logical flows through traces and spans with rich metadata attributes on logs, traces/spans, and metrics.
- The `aspire` CLI supports searching and filtering console logs as well as OTEL structured logs and traces by resource.
- It is deeply programmable and supports C#, TypeScript, and Python for building runtime orchestration logic.  And of course, it can run any code and has [built-in extensions for handling most common runtime stack components](https://aspire.dev/integrations/gallery/?) like Vite powered JS frontends, databases, messaging, caching, etc.

### Built in telemetry and observability

A key piece of the story is Aspire's easy-to-use and built-in telemetry and observability backend.

This screenshot of the Zeeq Aspire dashboard shows what a typical local runtime looks like:

![Zeeq's Aspire dashboard for local dev](/public/img/ai-sleeper-stack/zeeq-aspire.webp)

It doesn't look too different from Tilt or Docker Desktop, but peek the telemetry:

![Example of the telemetry in the dashboard](/public/img/ai-sleeper-stack/telemetry-example.webp)
*Rich built in telemetry lets agents get visibility into everyday types of issues like the underlying database queries being issued.*

Most importantly, the telemetry sink also surfaces `gen.ai` attributes that give visibility into standard agent interactions: materialized prompts, tool calls, etc.

![Example of the telemetry in the dashboard](/public/img/ai-sleeper-stack/gen-ai-telemetry.webp)
*Aspire materializes `gen.ai` spans to make it easier to see final prompts, tool calls, and agent interactions.  The agents can search these spans directly.*

You will certainly get much, much richer telemetry via specialized tools like Langfuse (not mutually exclusive since it becomes just another OTEL sink), but Aspire's built-in, searchable telemetry sink let's agents autonomously iterate with visibility into the runtime state.

```bash
aspire otel spans zeeq-server --search "BEGIN CHANGES" \
  --non-interactive -nologo \
  --dashboard-url http://localhost:15010
```

Searches through the list of spans that the agent can then then read for a full trace of the execution flow (assuming your code has been well-instrumented!):

![Example of querying spans from Aspire](/public/img/ai-sleeper-stack/query-traces.png)
*The CLI allows agents to search through the telemetry spans before doing a full read to trace through an instrumented execution flow*

### Agent interaction with runtime resources

The `aspire` CLI becomes a primary interaction point with agents for runtime resources including:

- Reading logs via `aspire logs`
- Query the state of running resources via `aspire describe <resource>`
- Controlling resource lifecycle via `aspire resource <resource> start|stop|rebuild`

```bash
# Search logs for a given resource
aspire logs zeeq-server --search "principal" \
  --non-interactive --nologo

# Allow agent to further filter via jq
aspire logs zeeq-server --search "principal" \
  --non-interactive --nologo --format json

# Check application status with all environment variables
aspire describe zeeq-server --format json

# Check application status and query for specific properties
aspire describe zeeq-server --apphost ./host --format json | \
  jq -c '.resources[] | {
    name,
    displayName,
    state,
    healthStatus,
    commands: (.commands // {} | keys)
  }'

# Rebuild and restart a compiled resource like a .NET or Rust backend
aspire resource zeeq-server rebuild

# Restart a Node frontend resource
aspire resource zeeq-web restart
```

### Port isolation for worktrees

The inner network loop becomes a key feature if you're using worktrees and you want to [run the full application stack in isolation to allow for agents to work in parallel](https://devblogs.microsoft.com/aspire/aspire-isolated-mode-parallel-development/).

```bash
# Run with randomized ports and isolated user secrets
aspire run --isolated
```

----

## Wiring CSharpRepl into the running application

[CSharpRepl (CSR)](https://fuqua.io/CSharpRepl/) is a command line REPL that allows interactive C# scripting.

In the past, this has been generally useful for quickly trying out C# code without having to create a project or compile and run.  However, the real unlock with CSR is that it can be *wired into a running instance of the application*.

### Direct access to runtime dependencies

Imagine the following dependency graph:

![Dependency graph for an API handler](/public/img/ai-sleeper-stack/di-graph.png)
*A typical dependency graph for a web API handler endpoint.*

If the agent can only test the web API handler entrypoint via `curl`, then it cannot easily diagnose issues with `Service α` since it must always pass through three layers of dependencies before it can test an input scenario with `Service α`.  While some of this can be covered with unit and integration tests, in some cases -- especially with AI-enabled apps -- there's no substitute for being able to test the runtime application directly like a human might with a debugger.

This is precisely what CSR allows.  Will Fuqua (author of CSharpRepl) [has a writeup that shows the gist of this](https://fuqua.io/blog/2026/06/injecting-a-csharp-repl-into-a-running-net-process/):

```bash
# Get the installation location of the CSharpRepl tool hooks
csharprepl connect init

# This yields two environment variables (example) that can be wired up via Aspire
export DOTNET_STARTUP_HOOKS=".../tools/csharprepl/.../connector/CSharpRepl.InjectedHook.dll"
export ASPNETCORE_HOSTINGSTARTUPASSEMBLIES="CSharpRepl.InjectedHook"

# Then list the candidate processes for CSR to connect to
csharprepl connect list

# And connect to the PID
csharprepl connect 6580
```

Once connected, the agent can then *directly manipulate `Service α`* and any other runtime dependencies without having to go through the API entrypoint.  This allows agents to:

- Diagnose issues and iterate on fixes much more quickly since it can wrap the running functions,
- Test different input scenarios,
- Replace the current implementation and run a live test reaching directly into the DI container,
- Bypass the authentication and authorization layers at the boundary to simulate different users without having complicated agent authorization paths or weakened surface areas to create an affordance to let agents access the runtime.

Think of it like a blood test versus directly examining an organ or tumor.  The blood test is still useful, but being able to directly probe the organ or tumor will give a more complete picture of underlying "operational runtime behavior" and make it easier for diagnosing issues.

### Wrapping and replacing functions at runtime

But it goes beyond that:

- `#replace` allows agents to intercept and *replace* a running function with another implementation
- `#wrap` allows agents to intercept and *wrap* a running function with additional logic (like logging, telemetry, etc.)

![Conceptual overview of how agents can use CSharpRepl to wrap and replace ](/public/img/ai-sleeper-stack/service-wrap-replace.png)
*How wrap and replace can be used by agents to diagnose runtime issues, insert additional telemetry, and test new implementations rapidly.*

Together, this toolset gives agents a **huge** unlock because the agents can reach deep into the runtime configuration of the application to test different scenarios.  Three aspects that I find this boosts:

1. Agents can better diagnose production issues by directly interacting with a local running instance and simulate the failure conditions or attempt to recreate the failure conditions from a production trace.
2. Agents can build more stable runtime systems and regression test backends without the need to use slower frontend browser/client manipulation.
3. Agents can experiment and iterate faster while building more stable runtime/production code.

----

## Closing thoughts

While it is true that a lot of the innovation in the AI space originates from the Python ecosystem, for most teams *building business applications using AI*, the C# and .NET stack is a highly underrated and perhaps surprising option, especially for folks that haven't dabbled in C# or .NET since the *.NET Framework* era.

Modern .NET and C# are well-suited for teams that want to build real products and real software on a stable base in a programming language and stack that offers agents strong build-time guardrails as well as throughput and performance at scale where it matters (consider the API boundary serialization of JSON and gRPC/Protobuf, for example -- both areas where C# and .NET excel).

In [Part 2](https://chrlschn.dev/blog/2026/08/the-unexpected-ai-stack-csharp-dotnet-part-4/), we'll build a practical template for a C# + .NET agent-enabled application that you can use as a starting point for your own projects (wiring EF core for build time checked database queries, minimal web API endpoints, test containers for integration testing, etc.).  We'll also go into real-world examples of how to let agents operate the stack effectively when building autonomously including a skill that guides agents on getting the most out of CSharpRepl.

> If you are curious to see a real-world setup, check out the [Zeeq.ai](https://zeeq.ai) repo: [https://github.com/zeeq-ai/zeeq-app](https://github.com/zeeq-ai/zeeq-app)

----

Still human written (mistakes and all!); [see the file history in the repo](https://github.com/CharlieDigital/chrlschn/blob/main/src/content/blog/2026/the-unexpected-ai-stack-csharp-dotnet-part-1.md).
