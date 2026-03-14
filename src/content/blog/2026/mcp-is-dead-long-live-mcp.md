---
title: "MCP is Dead; Long Live MCP!"
description: "Understanding the social media zeitgeist around CLIs and the premature death of MCP"
pubDate: "2026 March 14"
socialImage: "/public/img/complexity/rube-goldberg.gif"
slug: "2026/03/mcp-is-dead-long-live-mcp"
tags: "llms,ai.mcp"
---

----

## Summary

- There is currently a social media zeitgeist dialed-in on CLIs...just as their was a moment for MCP but a few months ago
- While it is true that there are token savings to be had by using a CLI, many folks have not considered how agents using customer CLIs run into the same context problem as MCP, except now without structure
- There is a confusion of MCP over `stdio` versus MCP over HTTP; the latter is a very different use case
- Many folks are also only familiar with MCP tools, but overlook MCP prompts and resources
- Also misunderstood is why MCP auth is important and the role and importance of telemetry in understanding org-wide too use
- For enterprise and org-level use cases, MCP is the present and future; teams that want to find the path from vibe coding to agentic engineering need to understand how to leverage MCP.s

----

## The Influencer Driven Hype Cycle

Just 6 months back, MCP was all that anyone could talk about and it seemed that everyone was in a frenzy to ship MCP-related offerings and tools.  At [Motion](https://www.usemotion.com/), where I work, we were in the thick of it with seemingly every single vendor trying to get us to buy or use their MCP product.

> It's just an API; why do I need a wrapper around that when I can just call the API directly.

This was my common response to these vendors as they tried to pitch usage of their MCP offerings (often at a premium!).  My guard was up as I looked on from the sidelines at the hype surrounding MCP with skepticism; we entirely skipped the MCP hype cycle because it wasn't clear what the added value was.

But in just 6 short months, it seems as if the industry discourse around MCP has completely shifted.  What happened?

First, most folks realized that in most use cases, MCP is just overhead to calling APIs directly because MCPs as wrappers for APIs do not make sense.  Instead of using MCP, we simply wrote small tool wrappers around REST API endpoints.

Second, it is instructive to step back and understand just how much of this field is now driven by individuals marketing their companies and marketing themselves.  So much of the AI landscape requires creating a sense of FOMO and hype and has been that way for years (while those of us adopting and adapting these tools on a daily basis often look on scratching our head).  These influencers constantly need *some* content to rant about to stay relevant and push themselves, their products, or their services so we see constant re-orientation around the topic of the moment.

In that sense, I view many of these influencers (and even people that should know better like Garry Tan and Andrew Ng) as no different than influencers peddling Ivermectin or the gluten-free diets or vaccine conspiracies; it's simple ignorance.

The influencer-driven discourse has now shifted towards hating on MCP and praising CLIs as the current zeitgeist.  And for good reason because in many cases, it is true that the CLI makes much more sense as an interface for agentic tool use...except when it's not.

## Understanding the Misunderstanding

Before we dive any deeper, I would like to make clear that I think MCP is indeed the wrong choice for a class of use cases, but as the title might suggest,

There are two topics at the core of the debate of CLI vs MCP and the foundation of much of the discussion:

1. Token savings and efficiency with CLIs.
2. The bloat and uselessness of MCP and its complexity.

### Do Token Savings Exist?

Yes, they do.  There are two modalities in token savings to be had.

#### CLI Tools in the Training Dataset

First is that for CLI utilities *in the models' training dataset* like `jq`, `curl`, `git`, `grep`, `aws`, `s3`, `gcloud`, etc. benefit tremendously from the LLM already having encountered innumerable examples of how to use these tools.  Because of this, the agent does not need additional instruction, schemas, or context on how to use these tools; it can simply one-shot the tool in many cases.  This can be a significant savings over MCP because in MCP, the tools must be declared up front in the `tools/list` response.

However, this is *not* true of a custom, bespoke CLI tool.  The LLM has no way of knowing which CLI to use and how it should use it...unless each tool is listed with a description *somewhere* either in `AGENTS|CLAUDE.md` or a `README.md`.  You *must* provide the LLM some instruction on how and when to use a bespoke CLI tool.

It is possible to point it to a directory called `/cli-tools` and rely on descriptive naming to have the agent pick and choose the tool, but anyone that works with agents day in and day out already knows that agents are often not very good at this without more explicit instructions.

Aside from that, even with a tool like `curl`, any token savings are lost the moment it has to understand a bespoke OpenAPI schema to correctly call the API since now, the entire OpenAPI schema may need to be loaded into context or extensive examples given to the agent in context to instruct it on how to use it.

#### Progressive Context Consumption
