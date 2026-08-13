---
title: "The Unexpected AI Stack: C# + .NET (Part 5)"
description: "Wiring telemetry and logging into the application and building the spec planning feature using the coding agent"
pubDate: "2026 August 16"
socialImage: "/public/img/ai-sleeper-stack/netcore-csharp-aspire.png"
slug: "2026/08/the-unexpected-ai-stack-csharp-dotnet-part-5"
tags: "llms,ai,mcp"
---

----

## Summary

- TBD

----

- [Part 1](https://chrlschn.dev/blog/2026/08/the-unexpected-ai-stack-csharp-dotnet-part-1/) will introduce two under-the-radar components of the .NET stack that make it surprisingly amenable to building software with agents: Aspire and `CSharpRepl`.
- [Part 2](https://chrlschn.dev/blog/2026/08/the-unexpected-ai-stack-csharp-dotnet-part-1) will dive into a hands-on implementation from the ground up to scaffold an open-source template for teams to build on top of.
- [Part 3](https://chrlschn.dev/blog/2026/08/the-unexpected-ai-stack-csharp-dotnet-part-3/)  will implement the next layer of the application including a simple streaming interface to the Copilot SDK agent.
- **Part 4** (you are here) will extend the application with Testcontainers to demonstrate how to simplify test execution for agents with stateless containers.
- **Part 5** will configure the application with logging, telemetry, and observability before diving into building the actual application using your coding agent.

***The full repo***: <https://github.com/zeeq-ai/zeeq-tmpl/tree/feat-part5-Part-5-code-changes>

----

## What we're building

In this final part of the series, the objective is to extend the application with Testcontainers for stateless integration testing as well as telemetry and observability that provides agents additional context when understanding the flow of the application and troubleshooting.

The last leg of the application is to build a specification writing surface that allows the agent to give continuous feedback on the specification as it runs research on the underlying code as the specification is being written.

![Wireframe of the application build out for this stage](/public/img/ai-sleeper-stack/planner-wireframe.png)
*A planning surface for writing specifications that the agent will automatically process and start building a plan from.*

----

## Adding logging, telemetry, and observability

A key foundation of building software systems agentically is to provide agents a well-designed logging and telemetry setup that allows agents to pin-point issues and understand the runtime state by examining logs and telemetry spans.

To this effect, we'll wire up Serilog for logging and OpenTelemetry to emit spans that the agent can search for in Aspire using the CLI.

### Adding the logging and telemetry packages

```bash
cd src/server
# Serilog packages for logging
dotnet add package Serilog
dotnet add package Serilog.AspNetCore
dotnet add package Serilog.Sinks.Console
dotnet add package Serilog.Sinks.OpenTelemetry
# OpenTelemetry packages for telemetry
dotnet add package OpenTelemetry
dotnet add package OpenTelemetry.Exporter.OpenTelemetryProtocol
dotnet add package OpenTelemetry.Extensions.Hosting
dotnet add package OpenTelemetry.Instrumentation.AspNetCore
dotnet add package OpenTelemetry.Instrumentation.Http
dotnet add package OpenTelemetry.Instrumentation.EntityFrameworkCore --prerelease
# Package level support for telemetry
dotnet add package Npgsql.OpenTelemetry
```

### Wiring the runtime for logging

The logger will be set up to ship to the console and to OpenTelemetry.  The agent can see the logs by directly querying for console logs or seeing them in the context of an OpenTelemetry span.

```csharp
// src/server/Program.cs
// Setup logging
var attributes = new Dictionary<string, object> { ["service"] = "zeeq" };

Log.Logger = new LoggerConfiguration()
    .WriteTo.Console(
        outputTemplate: "[{Timestamp:HH:mm:ss.fff} {Level:u3}] {Message:lj} ({Here}){NewLine}{Exception}"
    )
    .WriteTo.OpenTelemetry(options =>
    {
        options.ResourceAttributes = attributes;
    })
    .MinimumLevel.Override("Microsoft", LogEventLevel.Warning)
    .MinimumLevel.Override("Microsoft.Hosting.Lifetime", LogEventLevel.Information)
    .CreateLogger();

builder.Services.AddSerilog();
```

A key part of exposing more context to the agents when it reads the logs is to actually provide ***the exact member, source file, and line number for each log message***.

We can achieve this using .NET's `CallerMemberNameAttribute`, `CallerFilePathAttribute`, and `CallerLineNumberAttribute` markers to capture the call site of the log message and add it to the log context.

```csharp
// src/server/LoggerExtensions.cs
using System.Runtime.CompilerServices;
using ILogger = Serilog.ILogger;

namespace Zeeq.Tmpl;

/// <summary>
/// Extension method to capture the call site with member name, file, and line number.
/// </summary>
public static class LoggerExtensions
{
    public static ILogger Here(
        this ILogger logger,
        [CallerMemberName] string memberName = "", // 👈 Note the empty defaults; these are populated at call time
        [CallerFilePath] string sourceFilePath = "",
        [CallerLineNumber] int sourceLineNumber = 0
    )
    {
        var srcFile = Path.GetFileName(sourceFilePath);
        var here = $" {srcFile}:{memberName}@{sourceLineNumber}";

        return logger
            .ForContext("Here", here)
            .ForContext("MemberName", memberName)
            .ForContext("FilePath", sourceFilePath)
            .ForContext("LineNumber", sourceLineNumber);
    }
}
```

### Wiring the runtime for OpenTelemetry

Next is to wire up OpenTelemetry into the runtime:

```csharp
// src/server/Program.cs
builder
    .Services.AddOpenTelemetry()
    .ConfigureResource(res => res.AddService("zeeq").AddAttributes(attributes))
    .WithTracing(builder =>
    {
        builder
            .AddAspNetCoreInstrumentation(config =>
            {
                config.RecordException = true;
            })
            .AddHttpClientInstrumentation()
            .AddEntityFrameworkCoreInstrumentation();
    })
    .WithMetrics(builder =>
        builder
            .AddMeter("*")
            .AddAspNetCoreInstrumentation()
            .AddHttpClientInstrumentation()
            .AddNpgsqlInstrumentation()
    )
    .WithLogging()
    .UseOtlpExporter();
```

### Instrumenting the flow of the application

The OpenTelemetry SDK for .NET provides two ways of implementation instrumentation using either `System.Diagnostics` or the [Tracer Shim API](https://opentelemetry.io/docs/languages/dotnet/shim/).

For this application, we'll choose the more idiomatic `System.Diagnostics` approach with several convenience methods that capture the telemetry call site and makes it easier to attach metadata to the span.

```csharp
// src/server/ZeeqTelemetry.cs
using System.Diagnostics;
using System.Diagnostics.Metrics;
using System.Runtime.CompilerServices;

namespace Zeeq.Tmpl;

/// <summary>
/// Telemetry entry point with convenience methods.
/// </summary>
public static class ZeeqTelemetry
{
    public const string ActivitySourceName = "zeeq";

    public static readonly ActivitySource Tracer = new(ActivitySourceName);

    public static readonly Meter Metrics = new(ActivitySourceName);

    public static void SetTags(params (string Key, object? Value)[] tags)
    {
        foreach (var (Key, Value) in tags)
        {
            Activity.Current?.SetTag(Key, Value);
        }
    }

    public static Activity? AddEvent(
        (string Key, object? Value)[] tags,
        string? eventName = null,
        [CallerMemberName] string name = "",
        [CallerFilePath] string filePath = "",
        [CallerLineNumber] int lineNumber = 0
    )
    {
        var activity = Activity.Current;

        var effectiveName = eventName ?? $"{name}@{Path.GetFileName(filePath)}:{lineNumber}";

        activity?.AddEvent(
            new ActivityEvent(
                effectiveName,
                tags:
                [
                    .. tags.Select(tag => new KeyValuePair<string, object?>(tag.Key, tag.Value)),
                    new KeyValuePair<string, object?>("code.member", name),
                    new KeyValuePair<string, object?>("code.file_path", filePath),
                    new KeyValuePair<string, object?>("code.line_number", lineNumber),
                ]
            )
        );

        return activity;
    }

    public static Activity? Trace(
        (string Key, object? Value)[] tags,
        string? traceName = null,
        [CallerMemberName] string memberName = "",
        [CallerFilePath] string filePath = "",
        [CallerLineNumber] int lineNumber = 0
    ) =>
        ZeeqTelemetry.Tracer.StartActivity(
            $"{traceName ?? $"{Path.GetFileName(filePath)}#{memberName}"}",
            kind: ActivityKind.Internal,
            parentContext: Activity.Current?.Context ?? default,
            tags:
            [
                .. tags.Select(tag => new KeyValuePair<string, object?>(tag.Key, tag.Value)),
                new KeyValuePair<string, object?>("code.member", memberName),
                new KeyValuePair<string, object?>("code.file_path", filePath),
                new KeyValuePair<string, object?>("code.line_number", lineNumber),
            ]
        );
}
```

Now we can instrument the health endpoint as a test.

```csharp
// src/server/Endpoints/Health.cs

// Note: updated to `async` to include the DB call as an example
public class HealthHandler(ZeeqContext dbContext) : IEndpointHandler
{
    private static readonly Serilog.ILogger Log = Serilog.Log.ForContext<HealthHandler>();

    public async Task<string> HandleAsync()
    {
        // 👇 Start a trace here
        using var activity = ZeeqTelemetry.Trace(
            tags: [("endpoint", "health")],
            traceName: "HealthCheck"
        );

        // 👇 This log will be linked to the span
        Log.Here().Information("Health check requested!");

        // 👇 Database access will produce a span with the query!
        await dbContext.Database.ExecuteSqlRawAsync("SELECT 1");

        return $"Healthy @ {DateTime.UtcNow}";
    }
}
```

At this point, you may still be wondering "why bother with this at all"?  Again, the key is to help provide agents visibility into what's happening as the code is executing.  To do this well requires that we provide high-signal telemetry that the agents can search and traverse through.

What this looks like in the trace when we `curl http://localhost:5138/health`:

![What the OTEL trace looks like](/public/img/ai-sleeper-stack/telemetry-output.webp)
*The telemetry trace now has a custom span that carries the information about the exact call site as well as the logs in the scope of the trace.*

To query for this in Aspire via the CLI:

```bash
# OTEL query requires specifying the dashboard
aspire otel spans app-backend --search "Health" --non-interactive --nologo --dashboard-url http://localhost:15050

# The matching spans
09:25:15.206 OK     0.25s app-backend: GET /health ebb3f57
09:25:15.250 OK      0.2s app-backend: HealthCheck e622d94

# In JSON format to expose the metadata
aspire otel spans app-backend --search "Health" --non-interactive --nologo --dashboard-url http://localhost:15050 --format json

# Query by trace
aspire otel traces app-backend --search "Health"  --non-interactive --nologo --dashboard-url http://localhost:15050

# Produces the table of matching traces
┌──────────────┬──────────────────────────────────┬───────┬──────────┬────────┐
│ Timestamp    │ Name                             │ Spans │ Duration │ Status │
├──────────────┼──────────────────────────────────┼───────┼──────────┼────────┤
│ 09:25:15.206 │ app-backend: GET /health 65562f4 │ 3     │ 0.25s    │ OK     │
└──────────────┴──────────────────────────────────┴───────┴──────────┴────────┘
Showing 1 of 1 traces
```

The structured log also carries this information enriched from using `.Here()`:

![What the structured log looks like](/public/img/ai-sleeper-stack/structured-log.webp)
*The structured log now has a custom span that carries the information about the exact call site so agents can quickly track down code.*

Likewise, we can query for this log:

```bash
# Query for our log
aspire logs app-backend --search "Health"

# Produces the following (⭐️ note the source and line number!)
[app-backend] [09:25:15.250 INF] Health check requested! ( Health.cs:HandleAsync@26)
[app-backend] [09:25:15.455 INF] HTTP GET /health responded 200 in 230.3616 ms ()
```

***Now the full power of this stack starts to come into focus: by providing agents deep visibility into the runtime execution of the code plus the ability to manipulate the code at runtime without recompiling or restarting the application, this stack gives agents supreme flexibility to iterate on the codebase autonomously.***

### Teach the agent how to build a skill

As we did in part 3 and part 4, we'll teach the agent to manifest a skill to help it use the Aspire stack effectively as it traces the application and generates code.

As before, I recommend issuing the instructions one part at a time to watch the agent learn and iterate.

```md
<!-- Initialize the doc with a few help commands -->
We are working on writing a skill in .agents/skills/aspire-tracing/SKILL.md using Aspire
Our goal is to keep this skill short, terse, to the point with key examples broken down into sections.
Key commands: `aspire otel spans -h`, `aspire otel traces -h`, `aspire logs -h`, `aspire resource app-backend -h`, `aspire -h`
This document will be organized into sections with notes and examples
Write a quick introductory section and then a table of the key basic commands.
When done, I'll prompt the next experiment

<!-- Teach it how to use the logs -->
Use `aspire logs app-backend --search "Health" --format json` to query for logs in the app-backend service that contain the word "Health".
Note the output format in JSON and how to filter/search through it
Note the log message carries the source location of the log message
Document findings and usage patterns
When done, I'll prompt the next experiment

<!-- Teach it to use the otel commands which require a dashboard URL -->
Use `aspire otel spans app-backend --search "Health" --non-interactive --nologo --dashboard-url http://localhost:15050 --format json` to query for spans
Use `aspire otel traces app-backend --search "Health"  --non-interactive --nologo --dashboard-url http://localhost:15050` to query for traces (try with and without `--format json`)
Note that `--dashboard-url` is required and can be resolved from `aspire ps` if needed; try with and without
Note the spans carry the source locations as well
Document findings and usage patterns
When done, I'll prompt the next experiment

<!-- Teach it how to write telemetry -->
Read: ZeeqTelemetry.cs, Health.cs, Program.cs to understand how to instrument the application
Distinguish between logging and telemetry and how to use them together
Delineate the use case for logging and telemetry
Understand the custom extension methods used to enrich telemetry spans with local context
Observe how the log instance is initialized per class and how the `Here()` extension method is used to enrich the log context with the call site
Document findings and usage patterns
When done, I'll prompt the next experiment

<!-- Reconciliation and terseness pass -->
Reconcile later findings with initial learnings
Keep the most useful commands for diagnosing issues and tracing code
Omit noise and non-essential, low-utility commands and learnings
Consolidate and tighten up the logging instructions
Focus the skill to be concise, terse, direct, and dense
```

Like before, add an explicit entry into `AGENTS.md` to make it more likely to be used.

```md
// AGENTS.md and CLAUDE.md
## Skills

- Use the skill `unit-integration-testing` when writing and running .NET C# tests
- Use the skill `csharprepl` to directly manipulate the running C# application, wrap methods, replace methods, and inspect the runtime state of the application; experiment rapidly without rebuilding the runtime
- Use the skill `aspire-tracing` when working with Aspire to access telemetry and source location as well as best practices for instrumenting the application with OpenTelemetry and logs.
```

----

## Regroup and recap of the current state

***A quick regroup:*** to this point, we've been heavily focused on hand rolling the scaffolding for the application.  Part of this is to demonstrate how to design a foundation for enabling fluid agentic engineering in a codebase.  Part of this is that no agents will (without a lot of coaxing and hand-holding) produce this type of foundation on its own.  This exercise serves both the human operator and also helps agents understand how to build a sound foundation for agentic coding.

The key ingredients that have been the core focus of this series so far:

- Runtime scaffolding to carry types from the database to the front-end of the application ([Part 2](https://chrlschn.dev/blog/2026/08/the-unexpected-ai-stack-csharp-dotnet-part-2))
- Aspire to orchestrate the local development runtime while providing agents the tooling to manipulate the runtime state, query for logs and traces, and wire up CSharpRepl ([Part 2](https://chrlschn.dev/blog/2026/08/the-unexpected-ai-stack-csharp-dotnet-part-2), [Part 3](https://chrlschn.dev/blog/2026/08/the-unexpected-ai-stack-csharp-dotnet-part-3))
- Adding CSharpRepl to allow agents to directly modify the running application without needing to recompile or restart the application; a powerful tool to both diagnose issues and iterate rapidly on the codebase ([Part 3](https://chrlschn.dev/blog/2026/08/the-unexpected-ai-stack-csharp-dotnet-part-3))
- A test harness using Testcontainers to allow agents to run tests in a stateless containerized environment ([Part 4](https://chrlschn.dev/blog/2026/08/the-unexpected-ai-stack-csharp-dotnet-part-4))
- A logging and telemetry setup that allows agents to trace the flow of the application to the exact source location of the code that provides agents with the context to understand the flow of the application and diagnose issues

This foundational work has mostly been done by hand to help understand the simplicity and significance of each piece and also how to bring this together.  With this deep understanding and the underlying scaffolding in place, we're ready to move onto the next phases where we start to build out the rest of the features relying on agents.

----

## Implementing the feature

From this state on, we'll be relying on prompts to build the rest of the features because we've given the agent a solid set of tools to use for working with the codebase iteratively and made the key platform level architecture choices.

I'll focus on the prompt iteration at this point.

### Wiring up the API backend

### Wiring up the API frontend

### Test driving it

----

## Closing thoughts

TBD


> If you are curious to see a real-world setup, check out the [Zeeq.ai](https://zeeq.ai) repo: [https://github.com/zeeq-ai/zeeq-app](https://github.com/zeeq-ai/zeeq-app)

----

Still human written (mistakes and all!); [see the file history in the repo](https://github.com/CharlieDigital/chrlschn/blob/main/src/content/blog/2026/the-unexpected-ai-stack-csharp-dotnet-part-5.md).
