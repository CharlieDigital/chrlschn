---
title: "The Unexpected AI Stack: C# + .NET (Part 1)"
description: "Exploring an unconventional stack for building AI- and agent-enabled applications: C# and .NET"
pubDate: "2026 August 10"
socialImage: "/public/img/ai-sleeper-stack/netcore-csharp-aspire.png"
slug: "2026/08/the-unexpected-ai-stack-csharp-dotnet"
tags: "llms,ai,mcp"
---

----

## Summary

(TBD)

----

## Why C#?

In an age when agents can write code in any programming language, it's fair to ask: why have agents write code in C#?

Last year, I was a key part of the effort at Motion ($500m valuation, series C, post YCombinator startup) [to move the entire backend off of TypeScript + Node and shift to a combination of C# and F# for all future backends](https://engineering.usemotion.com/moving-off-of-typescript-e7bb1f3ad091).

The reasons are numerous and [I've written about this extensively before](https://typescript-is-like-csharp.chrlschn.dev/pages/intro-and-motivation.html), but it's worth summarizing this again in mid 2026 framed in the context of building in an agent first reality:

- Build- *and* run-time type checks provide one layer of safety and correctness for agents to work with
- Clean, easy to understand error stacks (story is even better with upcoming C# 15 and runtime async); compare this to typical TypeScript type error stacks...
- Roslyn static analyzers that allow for custom static analysis to build guardrails for agents
- Roslyn source generators for terse runtime code to remove boilerplate and reducing context for agents
- Extensively and richly documented with broad first-party libraries means that LLMs have very good coverage in their training data. While Python and JavaScript may have more coverage, the lack of a standard library and BCL means that there are many variants of how to accomplish the same task.
- EF Core (mature, powerful .NET ORM) provides build-time checks for database schemas and decreases the likelihood of runtime errors from schema changes.
- Mature, stable, *consistent*, easy to use tooling and tool chain.  Just `dotnet`.  Again, easy for agents to work with.

Aside from that, if developers are no longer writing code, why *not* choose a platform that has:

- Better built-in primitives for in-process asynchronous coordination (`System.Threading.Channels`, Orleans actor model)
- Higher performance and throughput where it matters (boundary serialization for both JSON and gRPC/Protobuf, etc.)
- Better story around ecosystem security and dependency management
- A whole professional organization continuously patching and fixing the platform and libraries

Given the benefits of adding both build and runtime type safety as well as a mature, well-documented, and broad platform, C# is a surprisingly strong choice for building real software with AI.

In this series, I want to shed some light on a few particular aspects that engineering teams might be sleeping on.

- **Part 1** (you are here) will introduce two  under-the-radar components of the .NET stack that make it surprisingly amenable to building software with agents: `CSharpRepl` and Aspire.
- **Part 2** will dive into a handles on implementation from the ground up with an open-source template for you to build your own solutions on top of.

## Encapsulating the Runtime with Aspire

If you're using Docker Compose or [Tilt](https://tilt.dev/) for dev runtime orchestration, you may have occasionally wished that it was just a bit more *programmable*.  That's exactly the gap that [Aspire](https://aspire.dev/) fills: a programmable orchestration layer that makes it easy to build an isolated, runtime stack that agents can control while building software.

The best analogy is to consider the difference between Terraform and Pulumi.  Terraform is a declarative tool for building infrastructure, while Pulumi is a *programmable* tool that allows teams to build infrastructure with code.  Aspire is the same thing for orchestrating your runtime stack.

It has several key features that I think make it a foundational component of an agent-friendly software stack:

- It has a [built-in internal network loop](https://aspire.dev/fundamentals/networking-overview/) that isolates individual runtime instances.  This is important for agent development and worktrees because this allows agents to run the full stack in isolation.
- It has a [built-in, queryable OpenTelemetry target](https://aspire.dev/fundamentals/telemetry/) that gives agents the richness to access not just logs, but logical flows through traces and spans with rich metadata attributes on logs, traces/spans, and metrics.
- The `aspire` CLI supports searching and filtering console logs as well as OTEL structured logs and traces by resource.
- It is deeply programmable and supports C#, TypeScript, and Python for building runtime orchestration logic.  And of course, it can run any code and has [built-in extensions for handling most common runtime stack components](https://aspire.dev/integrations/gallery/?) like JS frontends, databases, messaging, caching, etc.

### Aspire telemetry and observability

This screenshot of the Zeeq Aspire dashboard shows what a typical local runtime looks like:

![Zeeq's Aspire dashboard for local dev](/public/img/ai-sleeper-stack/zeeq-aspire.webp)

It doesn't look too different from Tilt or Docker Desktop, but peek the telemetry:

![Example of the telemetry in the dashboard](/public/img/ai-sleeper-stack/telemetry-example.webp)

Most importantly, the telemetry sink also surfaces `gen.ai` attributes that give visibility into standard agent interactions: materialized prompts, tool calls, etc.

![Example of the telemetry in the dashboard](/public/img/ai-sleeper-stack/gen-ai-telemetry.webp)

You will certainly get much, much richer telemetry via specialized tools like Langfuse (not mutually exclusive since it becomes just another OTEL sink), but Aspire's built-in, searchable telemetry sink let's agents autonomously iterate with visibility into the runtime state.

```bash
aspire otel spans zeeq --search "BEGIN CHANGES" \
  --non-interactive -nologo \
  --dashboard-url http://localhost:15010
```

Produces the list of spans that the agent can then query and trace the full execution path of the action.

![Example of querying spans from Aspire](/public/img/ai-sleeper-stack/query-traces.png)

### Agent interaction with runtime resources
