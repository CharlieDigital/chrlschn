---
title: "MCP is Dead; Long Live MCP! - Revisited"
description: "6 months later, where does MCP stand today?"
pubDate: "2026 August 8"
socialImage: "/public/img/mcp/death-to-mcp-long-live-mcp-revisited.png"
slug: "2026/08/mcp-is-dead-long-live-mcp"
tags: "llms,ai,mcp"
---

---

## Revisiting the Call

[Back in March](https://chrlschn.dev/blog/2026/03/mcp-is-dead-long-live-mcp/), the fervor around the death of MCP was at a fever pitch with every AI influencer in tech social media circles proclaiming the death of MCP and the reign of the CLI.

What I wrote then:

> Especially in `stdio` mode, MCP felt excessive and useless.  Indeed, in most use cases, MCP over `stdio` is probably not needed and adds complexity over writing a simple CLI.
>
> But MCP over streamable HTTP?  ***This is an absolute game changer*** and will be a key linchpin in organizational and enterprise adoption shifting from *vibe-coding* to *agentic engineering*.

This seemed obvious after having spent a few weeks building [Motion's internal agent context and telemetry system](https://zeeq.ai) ([now open sourced](https://github.com/zeeq-ai/zeeq-app)).

Here we are, ~6 months later, and the protocol (and industry) has aligned towards stateless MCP over streamable HTTP.

[Cloudflare's recent blog post](https://blog.cloudflare.com/mcp-v2/) focuses on the switch from the stateful HTTP mode to stateless HTTP mode (stateless HTTP was already possible even back in March, to be clear).  *But what seems likely now is that much of the expansion in the "backbone" of agentic systems will be built on top of MCP.*

![Cloudflare's MCP 2.0 blog post](/public/img/mcp/cloudflare.png)

Simon Willison's [recent blog post](https://simonwillison.net/2026/Jul/31/stateless-mcp/) also highlights many of the same points (which seemed obvious even back in March (stateless mode is not new!)).

![Simon Willison's blog post](/public/img/mcp/willison.png)

---

## Why MCP Seemed "Obvious"

As I wrote in the original post, MCP over HTTP has several advantages that a local CLI cannot provide around security, ease of rollout and deployment, and ability to scale.  For enterprise operating environments, CLIs were never going to be the answer for all use cases because they pose an operational and security risk.  (They are fine for use cases that do not require auth, fine for solo cowboy vibe-coders, but clearly not the right fit for an entire class of organizational and enterprise use cases.)

The obvious answer to *"why MCP"* is that *"it's just HTTP"* and (for better or for worse) much of our infrastructure, expertise, and tooling for the largest network for exchange of text-based communication and information is built around HTTP and its mature, well-established supporting standards like OAuth and OIDC.

- All of the ways you would secure HTTP endpoints (TLS, OAuth, OIDC, etc.) apply to securing an MCP endpoint.
- All of the ways you would rollout observability and telemetry for HTTP endpoints (OTEL, logging) apply to MCP endpoints.
- All of the server apps (Nest.js, Express, .NET Web APIs, etc.) you would use to deliver API endpoints and server capabilities over HTTP apply to delivering MCP capabilities
- All of the ways you would scale and load balance an HTTP endpoint...well *you get the picture* ***because MCP is just a thin payload and interaction model on top of HTTP***

For agents to be able to do more useful things, they need to be able to connect to other systems and we already have a very well defined set of tools and protocols for doing that; MCP just provides the structure and a few agent-specific idioms on top (elicitation, prompts, resources, etc.).

Cloudflare's blog post should then come as no surprise since they are, at the core, one of the biggest providers of HTTP infrastructure and services delivered over HTTP.

![ChatGPT and Codex itself uses MCP for much of its internal implementation](/public/img/mcp/codex-mcp.png)
*ChatGPT and Codex itself both use MCP for much of their internal implementation.*

---

## Should You Adopt MCP?

Yes.

I encourage everyone to build *something* with MCP over stateless, streamable HTTP so you understand when and why to pick MCP over local CLI (please do not misunderstand: in no way does MCP replace local CLI; *they just serve different use cases* and people were declaring MCP dead without understanding why enterprises might want to have a centralized interface that could plug easily into existing infrastructure and tooling).

|Type|When|
|---|---|
|**Local CLI**|Local capabilities, local tooling, manipulating local environments; well known tools like `jq`, `curl`, `grep` that are already in the LLM's training corpus many times over; tools that do not require auth.  Don't bother with MCP when a local CLI will work just fine.|
|**MCP over HTTP**|Tools that require state (stored in a DB, Redis, however you might store state with HTTP web APIs), tools that benefit from long-running backends, tools that require authentication and organizational access control, tools where telemetry and observability surface key insights.|

Stateless MCP over HTTP is built more or less just as you would build any other HTTP API endpoint (just with slightly different payloads and interaction flows).

---

## What's Still Missing?

All that said, there are still gaps in MCP across different *client* implementations.

Most notable [is ChatGPT/Codex's lack of support for MCP Prompts](https://github.com/openai/codex/issues/5059), which promises to fix one of the biggest issues right now with the nonsensical way we deploy agent skills via manually synchronizing raw text files.

For the unfamiliar, MCP Prompts is roughly equivalent to local skills and in most harnesses, is activated via a "slash" command just like skills.

![From the MCP specification page](/public/img/mcp/prompts-resource-tools.png)

Today, most teams synchronize skill files by "installing" them by downloading them manually in some way.  They then have to be kept updated, synchronized, etc.  Imagine manually downloading HTML and JS files to run a web app.  That would be silly!

If we think of an MCP endpoint as nothing more than a remote API that is exposed to the agent and skills as nothing more than text content + scripts (that ***is*** all that they are), well, *that sounds a lot like modern web apps* that deliver text (HTML) and scripts (JS) over HTTP!

Just like modern web apps, we can imagine that the text scaffolding and scripts get downloaded, cached, and executed in a nice little sandbox.  It feels simply obvious that this is pretty much exactly what teams and agents need, right?  And it seems obvious that we already have a nice set of tools and protocols for doing this over HTTP.

We'll get there.

---

## Closing Thoughts

The AI space is one that is still heavily susceptible to hype cycles because there are a lot of folks out there who are primarily pumping out content to get eyeballs.  Content creators that are building one offs and throwaways ([parallel Claudes building a C compiler](https://www.anthropic.com/engineering/building-c-compiler)?, [agent swarms building a web browser](https://fortune.com/2026/01/23/cursor-built-web-browser-with-swarm-ai-agents-powered-openai/)?) add noise that requires filtering to find the practical, useful signal within the cacophony.

The way to cut through a lot of the noise is to simply focus on first principles and deeply understand the underlying technical details.

MCP is assuredly ***not dead*** because anyone that spent a bit of time building with the streamable HTTP mode could see: *it's just HTTP* and for as inefficient and imperfect as HTTP is, it is still the single most successful protocol for the exchange of text-based content and information that humans have invented (thus far).

---

All content was human written; [see the file history in the repo](https://github.com/CharlieDigital/chrlschn/commits/main/src/content/blog/2026/mcp-is-dead-long-live-mcp-revisited.md).
