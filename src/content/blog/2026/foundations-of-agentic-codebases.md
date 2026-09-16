---
title: "Foundations of Agent Friendly Codebases"
description: "A look at 10 foundational pillars that enable agents to operate more competently and more efficiently in any codebase."
pubDate: "2026 September 13"
socialImage: "/public/img/10-pillars/10-pillars-og.png"
slug: "2026/09/foundations-of-agent-friendly-codebases"
tags: "architecture,ai"
---

----

## Summary

- Platform foundation decisions can help improve agent "competency" and efficiency by providing agents the tools to operate autonomously while ensuring the generated code is coerced into desirable patterns and less likely to fail at runtime.
- These same pillars help human operators as well, but the effect is amplified with agents
- The 10 pillars:
  - end-to-end types (at runtime, too!),
  - leveraging static analysis to surface issues as early as possible,
  - context-enriched logging and telemetry,
  - test isolation and concurrency,
  - runtime mutability of the application,
  - programmable runtime orchestration,
  - modularity and plugability,
  - terseness of expressions that highlight semantic intent,
  - well-documented foundations,
  - and strategic use of comments as infrastructure-free memory.

----

## Setting the stage for agents

Not all codebases are created equal when it comes to providing agents the scaffolding to work "competently" within the codebase.

Taking a step back, agents are not unlike the human members of your team in the sense that any platform configuration that allows a human engineer to move faster through the use of structural enforcement, increasing the likelihood of finding the right code with less misses, and reading less code to understand the application will intuitively also help agents operate in the codebase.

All the same things that make codebases more navigable, maintainable, and understandable for human engineers will also benefit agents.  Failure to provide this type of structure makes it more likely to result in slop, runtime errors, agents going off track, and building the wrong things.

To that end (from experience) here are 10 pillars that help agents operate more competently and more efficiently in any codebase.

## 1. End-to-end types (runtime, too!)

Types are a feedback mechanism for both humans and agents on the domain space and basic "rules" within the codebase.

Types can provide signals to agents in several ways:

- by describing what is allowed and what is not
- by signaling invalid code early via LSPs
- by signaling invalid code at a dedicated build step

Runtime types or schema validations add additional layer: precise feedback on incorrect states at the boundary at runtime.  Without some form of runtime type or shape validation, it can be difficult to trace down boundary origin errors (e.g. JSON inputs and deserialization) that may not otherwise materialize until deeper into a call stack.  Runtime types at the boundary short circuit this and also provide more diagnostic signals to agents.

Pick runtime typed languages like C#, Rust, Go or incorporate schema validations like Pydantic, Zod, or Valibot.

**Bonus**: use domain modeling and leverage your programming language's visibility modifier as a strong signal to the agents on how types are intended to be used.  I find that current generation agents are very good a honoring `private` vs `public` and will use those as hints on how they should or should not use a type.

## 2. Static analysis

Types are just one form of static analysis.  Most platforms include other forms of static analysis like linters and powerful analyzers like C#'s Roslyn Analyzers that allow authoring of rich, powerful static checks that enforce rules by surfacing signals as soon as the code is generated.

Any form of static analysis that provides additional signals on correctness decreases the risk of runtime errors due to invalid states by providing feedback to coding agents as early as possible.

## 3. Context enriched logging and telemetry

Like human teammates, agents will benefit from access to additional runtime context in well-placed logs and structured OpenTelemetry spans.

Enriching these signals with:

- Local, runtime context like input arguments and local variables
- File, member, and precise line location
- Encoding of the call path and hierarchy

will provide agents a faster route to identifying runtime issues.

Getting OpenTelemetry right is a big unlock as the usage of events, links, and tags on spans can give agents a "map" of how an operation flows through the codebase.

This is especially useful for diagnosing runtime issues.  Making this available to the agent in local dev and from runtime environments empowers agents to rapidly identify root cause, diagnose, and implement fixes.  Aspire's built-in OTEL collector with CLI querying makes this a no-brainer.

**Bonus**: use middleware to enrich your logs and telemetry consistently and automatically on the request.

## 4. Test isolation, concurrency, and composition

Tests (unit, integration, and end-to-end) are one of the mechanisms agents can leverage to verify their work.

Therefore, making tests faster and more isolated helps run more tests, both in terms of test speed as well as throughput (concurrency).

For your platform and runtime:

- Understand how the test harness parallelizes test cases and orient your codebase to maximize concurrency
- Teach your agent how to filter test cases and instruct it to run isolated tests first because this provides a faster feedback loop. Then progressively increase the test surface area when validating code after writing or updating tests.
- Use isolation techniques like [Testcontainers](https://testcontainers.com/) and transactions to speed up integration testing by isolating the test data.
- Write your code to depend less on integration tests and more on unit tests using a functional core and an imperative shell that moves all side effects to the edges.

The latter is one of the hardest things to do well for both humans and agents.  But doing so has big gains because it allows tests to run significantly faster.

**Bonus**: clearly tag your tests using your test library's tagging or annotation features so agents can easily filter subsets of related tests and have an efficient execution strategy (run less tests each cycle; only run the full suite as a final check).

## 5. Runtime mutability of the application

Doing logs and telemetry well give agents a boost when tracing, but providing an option of *mutating the runtime state* of the application is like turning on the afterburners.

When an application has to be restarted, the application loses the runtime state.  For languages that require a build step like Rust, Java, C#, and Go, this adds to the cost of each cycle and re-establishing state.  One approach might be to introduce a runtime scripting language (like some game engines).  Another is to figure out how to enable mutations of the running application *without restarting it*.

In C#, for example, [the CSharpRepl package](https://fuqua.io/CSharpRepl/) allows [hooking into the running application and directly manipulating the running state](https://fuqua.io/blog/2026/06/injecting-a-csharp-repl-into-a-running-net-process/) by:

- Wrapping existing functions with new ones to add logging or other wise change behavior before or after invoking the function
- Replacing functions at runtime to test hypothesis or stabilize a call path to isolate runtime test scenarios
- Directly access and manipulate runtime components without requiring a rebuild so agents can probe complex interactions that are hard to replicate in integration testing like cache behavior and boundary-level behaviors (e.g. interaction with real runtime components, not mocks)

Runtime mutability gives agents superpowers for both diagnostic tasks as well as exploratory work.

## 6. Programmable runtime orchestration

For any non-trivial application, the runtime will require many pieces to come together.  Redis, Postgres, the backend, the frontend, etc.

Tools like Docker Compose and Tilt provide a runtime orchestration layer that makes it easier for agents to both understand the runtime composition as well as operate the runtime components.  [Aspire](https://aspire.dev/) is a particularly powerful runtime orchestration tool because of its programmable nature (think Pulumi or AWS CDK).  As a bonus, Aspire's inner network loop helps isolate running stacks and prevents port conflicts, allowing agents to run multiple instances of the stack for worktrees.  Aspire also includes powerful CLI tooling to query the built in log and telemetry collector, allowing agents ease of visibility into the runtime.

The ability to run multiple instances of the full stack through Aspire's port isolation means that even local agents (assuming hardware capability), can work end-to-end on different tasks simultaneously.

In general, this programmability provides agents flexible ways to wire up and run components with one command (e.g. `docker compose up`, `tilt up`, `aspire run`).  Aspire also lets agents inspect runtime state because it includes CLI tools that let agents search through logs and OTEL traces.

## 7. Modularity and plugability

When paired with runtime mutability, modular, pluggable application components let agents swap out components at runtime and replace with fakes or experimental implementations.

It also assists agents in two other ways:

- Ease of test setup with localized fakes
- Isolate behaviors from contracts which can reduce code duplication as well as decrease failure scenarios by leveraging encapsulation

At a broader, platform level, modularity of the application itself helps teams move faster by partitioning runtime components into separate contexts.  This better matches how agents work best: when given control over a fully isolated context.  This means that building a pluggable "micro-frontend" architecture, for example, will pay dividends for agents by allow more parallelism.

## 8. Terseness of expressions

Having terse, expressive code that conveys the behavioral intent with less text is obviously helpful for agents because each string that is pulled into context has a cost and also influences how the underlying LLM understands the codebase.

Techniques that can reduce verbosity *while increasing semantic and behavioral signals* are powerful tools to help make code more terse while improving an agents understanding the intent of the code.  Shortening variable names to a single character?  That's usually a bad move since it reduces the semantic intent of the code.  Instead, focus on techniques that reduce verbosity while enriching intent.

As a concrete example, this test case:

```csharp
// Terse and expressive; clear what the test is setting up
var (seed, organizationGraphs) = await EntityGraph
    .AddGeneratedSeed(_context)
    .AddOrganizations(
        organization =>
        {
            organization.Slug = "graph-org-a";
            organization.IsDefaultMembership = true; // 👈 See how this stands out?
        },
        organization =>
        {
            organization.Slug = "graph-org-b";
            organization.IsDefaultMembership = false; // 👈 See how this stands out?
        }
    )
    .BuildAsync();
```

Here, you can clearly comprehend the scenario under test: one organization is `IsDefaultMembership = true` and the other is `IsDefaultMembership = false` so we must be testing membership behavior with all else being equal.

You can observe that the terse example above is *significantly less verbose* ***yet much more expressive*** than:

```csharp
static string NewId(string prefix) => $"{prefix}_{Guid.CreateVersion7():N}";

var now = DateTimeOffset.UtcNow;

var owner = new User
{
    Id = NewId("user"),
    DisplayName = "Test User 1",
    Email = "test-owner@example.test",
    EmailVerified = true,
    CreatedAtUtc = now,
    UpdatedAtUtc = now,
};

var seedOrganization = new Organization
{
    Id = NewId("org"),
    DisplayName = "Test Organization",
    Slug = $"test-organization-{Guid.CreateVersion7():N}"[..28],
    CreatedByUserId = owner.Id,
    CreatedAtUtc = now,
    UpdatedAtUtc = now,
    ActivatedAtUtc = now,
};

var seedRootTeam = new Team
{
    Id = NewId("team"),
    OrganizationId = seedOrganization.Id,
    DisplayName = "Root Team",
    IsRootTeam = true,
    CreatedByUserId = owner.Id,
    CreatedAtUtc = now,
    UpdatedAtUtc = now,
};

var seedOrganizationMembership = new OrganizationMembership
{
    Id = NewId("mem"),
    // Truncated...
};

var seedTeamMembership = new TeamMembership
{
    OrganizationId = seedOrganization.Id,
    // Truncated...
};

static (Organization Organization, Team RootTeam,
    OrganizationMembership OrganizationMembership,
    TeamMembership RootTeamMembership) CreateOrganization(
        User owner,
        string slug,
        bool isDefaultMembership
    )
{
    var now = DateTimeOffset.UtcNow;
    var organization = new Organization
    {
        Id = NewId("org"),
        // Truncated...
    };

    var rootTeam = new Team
    {
        Id = NewId("team"),
        // Truncated...
    };

    var organizationMembership = new OrganizationMembership
    {
        Id = NewId("mem"),
        // Truncated...
    };

    var rootTeamMembership = new TeamMembership
    {
        OrganizationId = organization.Id,
        // Truncated...
    };

    return (organization, rootTeam, organizationMembership, rootTeamMembership);
}

var organizationGraphs = new[]
{
    CreateOrganization(owner, "graph-org-a", isDefaultMembership: true),
    CreateOrganization(owner, "graph-org-b", isDefaultMembership: false),
};

_context.AddRange(
    owner,
    seedOrganization,
    seedRootTeam,
    seedOrganizationMembership,
    seedTeamMembership
);

foreach (var graph in organizationGraphs)
{
    _context.AddRange(
        graph.Organization,
        graph.RootTeam,
        graph.OrganizationMembership,
        graph.RootTeamMembership
    );
}
```

Not only is the first form more context *efficient*, it also doesn't pollute the context with unnecessary noise.  In this case, it also makes use of the type system to enforce correct setup of tests.

The rule holds: whatever improvements you make in the code that would make it easier for humans also makes it easier for agents.  Take the time to write framework level code when you notice this kind of verbosity and loss of terseness.

## 9. Use well-documented foundations

The more well-documented the foundational parts of a stack are, the less guidance an agent will need.  The more stable the platform has been historically, the less the agents will make mistakes based on its training data.  The more documentation a novel codebase and toolset has, the more likely it is that an agent will get it right and follow best practices.

As is the case with human teams, picking platforms, languages, packages, and tools that have historically been stable, well-documented (not just well-represented in the training corpus with potentially many variants) helps agents write "correct" code.  Stacks with high representation but also *high variance* will generally require more guidance to get the agent to write code that is idiomatic to a given team.  This will then require more context (skills, references) to force the agents into the desired behavior.

## 10. Strategic use of comments as durable, infrastructure-free memory

Code comments are a practice that I have always embraced as a way to help future travelers (usually myself) quickly orient in the codebase and understand technical decisions, tradeoffs made, and *business context* for some piece of code.

While your local coding agent has local memory and it is possible to adopt shared memory infrastructure, using comments is not only simpler and infrastructure free for a whole team, but the agent will self-update it as well.  The comments, when well-guided, will also instruct code review agents on the intent.

Because agents feed source to LLMs, comments in code are an infrastructure-free approach to embed long-term memory directly into the codebase; no third party, external tool required!

Comments at the start of files are particularly useful because unless the agent is slicing a file by a specific line that it has found, it will tend to read files a few lines from the top first.

```bash
# Common way agents inspect files
sed -n '1,200p' path/to/file
```

So it can be *very useful* to store a memory directly "in the line of sight".

What comments should focus on:

- Business context that *isn't* apparent from reading the code
- Technical tradeoffs and decisions made that may have upstream dependencies (so the agent doesn't try to "fix" some code)
- How a piece of code participates in a larger business process and the related artifacts
- Links or pointers to useful references in source or on the web where agents can read more context
- Rules that may not be obvious at a call site without reading deeper into the call stack and thereby allow agents to increase iteration velocity
- Related artifacts that were created at the same time that should be updated together or consulted

Effective use of comments is a low-lift, easy hack that pays big dividends for both agents, human operators, and code reviewers.

----

## Closing thoughts

It's hard to believe that *we're not even a year in* with respect to agentic coding (versus "AI-assisted coding").

Like human peers, agents can operate more competently, more effectively, and more efficiently when the platform has been scaffolded to provide both context and capabilities to the operator.

These 10 foundational pillars help both humans and agents work more effectively in a codebase, but in the hands of a coding agent, the effect is multiplied.  Getting these 10 right can allow agents to cycle faster, produce more "correct" code, reduce mistakes that surface at runtime, and work more autonomously without increasing operational risk.

While these facets are irrespective of tech stack, I have a writeup of how to incorporate these in C# and .NET: [The Unexpected AI Stack: C# + .NET (Part 1)](https://chrlschn.dev/blog/2026/08/the-unexpected-ai-stack-csharp-dotnet-part-1/).

What other foundational pillars or practices have you found useful in making codebases more agent-friendly?

----

Still human written (mistakes and all!); [see the file history in the repo](https://github.com/CharlieDigital/chrlschn/commits/main/src/content/blog/2026/foundations-of-agentic-codebases.md).
