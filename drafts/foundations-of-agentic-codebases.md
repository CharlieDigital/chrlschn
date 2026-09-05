---
title: "Foundations of Agent Friendly Codebases"
description: "A look at 10 foundational pillars that enable agents to operate more competently and more efficiently in any codebase."
pubDate: "2026 August 20"
socialImage: "/public/img/mcp/death-to-mcp-long-live-mcp-revisited.png"
slug: "2026/08/foundations-of-agent-friendly-codebases"
tags: "architecture,ai"
---

----

## Summary

- Platform foundation decisions can help improve agent "competency" and efficiency by providing agents the tools to operate autonomously while ensuring the generated code is "correct" and less likely to fail at runtime.
- These same pillars help human operators as well, but the effect is amplified with agents
- The 10 pillars: programmable runtime orchestration, end-to-end types (at runtime, too!), leveraging static analysis to surface issues as early as possible, context-enriched logging and telemetry, test isolation and concurrency, runtime mutability of the application, modularity and plugability, terseness of expressions that highlight semantic intent, well-documented foundations, and strategic use of comments as infrastructure-free memory.

----

## Setting the stage for agents

Not all codebases are created equal when it comes to providing agents the scaffolding to work "competently" within the codebase.

Taking a step back, agents are not unlike the human members of your team in the sense that any platform configuration that allows a human engineer to move faster, find the right code with less misses, and read less code to understand the application will intuitively also help agents operate in the codebase.

To that end, here are 10 pillars that help agents operate more competently and more efficiently in any codebase.

## 1. Programmable runtime orchestration

For any non-trivial application, the runtime will require many pieces to come together.  Redis, Postgres, the backend, the frontend, etc.

Tools like Docker Compose and Tilt provide a runtime orchestration layer that makes it easier for agents to both understand the runtime composition as well as operate the runtime components.  Aspire is a particularly powerful runtime orchestration tool because of its programmable nature (think Pulumi or AWS CDK).  As a bonus, Aspire's inner network loop helps isolate running stacks and prevents port conflicts, allowing agents to run multiple instances of the stack for worktrees.

This programmability provides agents flexible ways to wire up and run components with one command (e.g. `docker compose up`, `tilt up`, `aspire run`).

## 2. End-to-end types (runtime, too!)

Types are a feedback mechanism for both humans and agents on the domain space and basic "rules" within the codebase.

Types can provide signals to agents in several ways:

- by describing what is allowed and what is not
- by signaling invalid code early via LSPs
- by signaling invalid code at a dedicated build step

Runtime types or schema validations add additional layer: precise feedback on incorrect states at the boundary.  Without some form of runtime type or shape validation, it can be difficult to trace down boundary origin errors (e.g. JSON inputs and deserialization) that may not otherwise materialize until deeper into a call stack.  Runtime types at the boundary short circuit this and also provide more diagnostic signals to agents.

## 3. Static analysis

Types are just one form of static analysis.  Most platforms include other types of static analysis like linters and powerful analyzers like C#'s Roslyn Analyzers that allow authoring of rich, powerful static checks that enforce rules by surfacing signals as soon as the code is generated.

Any type of static analysis that provides additional signals on correctness decreases the risk of runtime errors due to invalid states.

## 4. Context enriched logging and telemetry

Like human teammates, agents will benefit from access to additional runtime context in well-placed logs and structured OpenTelemetry spans.

Enriching these signals with:

- Local, runtime context like input arguments and local variables
- File, member, and precise line location
- Encoding of the call path and hierarchy

will provide agents a faster route to identifying runtime issues.

Getting OpenTelemetry right is a big unlock as the usage of events, links, and tags on spans can give agents a "map" of how an operation flows through the codebase.

This is especially useful for diagnosing runtime issues.

## 5. Test isolation, concurrency, and composition

Tests are one of the mechanisms agents can leverage to verify their work.

Therefore, making tests faster and more isolated helps run more tests, both in terms of test speed as well as throughput (concurrency).

For your platform and and runtime:

- Understand how the test harness parallelizes test cases and orient your codebase to maximize concurrency
- Teach your agent how to filter test cases and instruct it to run isolated tests first because this provides a faster feedback loop. Then progressively increase the test surface area when validating code after writing or updating tests.
- Use isolation techniques like Testcontainers and transactions to speed up integration testing by isolating the test data.
- Write your code to depend less on integration tests and more on unit tests using a functional core and an imperative shell that moves all side effects to the edges.

The latter is one of the hardest things to do well for both humans and agents.  But doing so has big gains because it allows tests to run significantly faster.

## 6. Runtime mutability of the application

Doing logs and telemetry well give agents a boost when tracing, but providing an option of *mutating the runtime state* of the application is like turning on the afterburners.

When an application has to be restarted, the application loses the runtime state.  For languages that require a build step like Rust, Java, C#, and Go, this adds to the cost of each cycle.  One approach might be to introduce a runtime scripting language (like some game engines).  Another is to figure out how to enable mutations of the running application *without restarting it*.

In C#, for example, the CSharpRepl package allows hooking into the running application and directly manipulating the running state by:

- Wrapping existing functions with new ones to add logging or other wise change behavior before or after invoking the function
- Replacing functions at runtime to test hypothesis or stabilize a call path to isolate runtime test scenarios
- Directly access and manipulate runtime components without requiring a rebuild so agents can probe complex interactions that are hard to replicate in integration testing like cache behavior and boundary-level behaviors (e.g. interaction with real runtime components, not mocks)

Runtime mutability gives agents superpowers for both diagnostic tasks as well as exploratory work.

## 7. Modularity and plugability

When paired with runtime mutability, modular, pluggable application components let agents swap out components at runtime and replace with fakes or experimental implementations.

It also assists agents in two other ways:

- Ease of test setup with localized fakes
- Isolate behaviors from contracts which can reduce code duplication as well as decrease failure scenarios by leveraging encapsulation

At a broader, platform level, modularity of the application itself helps teams move faster by partitioning runtime components into separate contexts.  This better matches how agents work best: when given control over a fully isolated context.  This means that building a pluggable "micro-frontend" architecture, for example, will pay dividends for agents by allow more parallelism.

## 8. Terseness of expressions

Having terse, expressive code that conveys the behavioral intent with less text is obviously helpful for agents because each string that is pulled into context has a cost and also influences how the underlying LLM understands the codebase.

Techniques that can reduce verbosity *while increasing semantic and behavioral signals* are powerful tools to help make code more terse while improving an agents understanding the intent of the code.  Shortening variable names to a single character?  That's usually a bad move since it reduces the semantic intent of the code.  Instead, focus on techniques that reduce verbosity while enriching intent.

## 9. Use well-documented foundations

The more well-documented the foundational parts of a stack are, the less guidance an agent will need.  The more stable the platform has been historically, the less the agents will make mistakes based on its training data.  The more documentation a novel codebase and toolset has, the more likely it is that an agent will get it right and follow best practices.

As is the case with human teams, picking platforms, languages, packages, and tools that have historically been stable, well-documented (not just well-represented in the training corpus with potentially many variants) helps agents write "correct" code.  Stacks with high representation but also *high variance* will generally require more guidance to get the agent to write code that is idiomatic to a given team.

## 10. Strategic use of comments

Code comments are a practice that I have always embraced as a way to help future travelers (usually myself) quickly orient in the codebase and understand technical decisions, tradeoffs made, and business context for some piece of code.

Agents feed source to LLMs and thus comments in code are an infrastructure-free approach to embed long-term memory directly into the codebase; no third party, external tool required!

What comments should focus on:

- Business context that *isn't* apparent from reading the code
- Technical tradeoffs and decisions made that may have upstream dependencies (so the agent doesn't try to "fix" some code)
- How a piece of code participates in a larger business process and the related artifacts
- Links to useful references in source or on the web where agents can read more context
- Rules that may not be obvious at a call site without reading deeper into the call stack and thereby allow agents to increase iteration velocity

Effective use of comments is a low-lift, easy hack that pays big dividends for both agents and human operators and code reviewers.

----

## Closing thoughts

It's hard to believe that we're not even a year in with respect to agentic coding (versus "AI-assisted coding").

Like human peers, agents can operate more competently, more effectively, and more efficiently when the platform has been scaffolded to provide both context and capabilities to the operator.

These 10 foundational pillars help both humans and agents work more effectively in a codebase, but in the hands of a coding agent, the effect is multiplied.  Getting these 10 right can allow agents to cycle faster, produce more "correct" code, reduce mistakes that surface at runtime, and work more autonomously without increasing operational risk.
