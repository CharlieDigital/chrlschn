---
title: "LARPing with Agents (and Why It Works)"
description: "Understanding how assigning anthropomorphic traits to agents can improve their effectiveness and interaction."
pubDate: "2026 October 25"
socialImage: "/public/img/10-pillars/10-pillars-og.png"
slug: "2026/10/larping-with-agents-and-why-it-works"
tags: "ai,llm,agents"
---

----

## Summary

- Multi-agent orchestration has traditionally been challenging to get right, first hand experience with earlier models and harnesses demonstrated it simply consuming excess tokens for little payoff.
- As models and harnesses have advanced, it has become a more viable, more accessible technique that can allow agents to operate more autonomously without sacrificing quality.
- While nearly all harnesses and models now support "native" sub-agents, not all multi-agent orchestrators are created equally and using them effectively is still a matter of configuration and tuning to your workload in most cases
- Getting the most out of multi-agent orchestration requires understanding "why" it works and how to leverage agent teams to autonomously deliver high quality software
- This blog post covers the theory of "why" and the practical configuration of "how" using Paseo (<https://paseo.sh>; Apache 2.0) to achieve this with very low lift but high payoff.

----

## Roleplaying with Agent Teams

Four weeks back, while evaluating harnesses for orchestrating agents across multiple hosts, I stumbled upon the open source "meta harness" [Paseo](https://paseo.sh).

Up until this point, I have personally be a strict TUI user, generally staying shy of desktop apps.  But in this particular case, I was seeking a client that would be more accessible to wider team and Paseo fit the bill and handled multi-host orchestration spanning into GitHub Codespaces with aplomb.

Finding success with Paseo for multi-host orchestration, I decided that I would use it as my main harness for at least a week before recommending it.

In doing so, I stumbled upon the first bit of curiosity: the agent profiles (which turned out to be the gateway to building a powerful, low-friction, highly effective agentic team).  It did not "click" at first since the profiles seemingly had little effect on the agentic behavior.  Indeed, the agent profiles feel like they come up quite short at first blush.

The misleading part stems from the default agent profile dialog:

![Default dialog](/public/img/larping/agent-dialog.png)

No scoping to workspaces, no skills, no tool restrictions, generally very little control over the configuration.  You must be thinking: "this guy is pulling my leg!"

### Understanding the Prompt Flow

The example in the dialog is simplistic and indeed, when configured as guided by the example, the results are likely to be...underwhelming.   But playing with it bit-by-bit, I started to understand that I needed *invert* the way I was looking at this feature.

The trick is to actually understand how the agent profiles *flow*.  Paseo ships with a set of skills and MCP tools that manage agent orchestration.  Or particular interest ist he `list_profiles` tool:

```markdown
**`list_profiles`** — named launch bundles configured by the human. Before choosing how to launch a delegated agent, call this tool and read every profile's `notes`. Pick a named profile the user requested, or the profile whose notes best match the work.
```

In practice, a **lead** or **orchestrator** will use this tool to list out the profiles by first reading the configuration and then matching the work to the `note` for the profile.  Understanding this, we can *simply use this mechanism to project inject instructions into the lead*.

Here is an example of a profile for a "Distinguished Member of Technical Staff":

```markdown
Use for design reviews, tie breakers, and input for challenging architectural decisions to get a second or final opinion.  Ask for creative ideas and ways to improve the design; explore alternate approaches that produce faster, cleaner, more efficient code.  Ask to research and determine if there are other, better angles to approach the problem space.  DO NOT ask to write code; ask for ideas, input, creativity, new angles, alternate paths.  Explicitly instruct: DO NOT CODE; only research, ideate, expand solution space, provide feedback.
```

Take note of the verbiage: these are *not* instructions for the sub-agent; these are instructions for the *lead agent* when instantiating the sub-agent.  When the lead reads the profiles, this configuration causes it to specifically prompt the sub-agent teammate.  The setup is in fact, *inverted*.

### Getting Consistent Behavior

This is, in fact, all that is needed to get agentic behavior working with shocking efficacy in Paseo.  Go ahead and configure a full team of profiles:

![Agent profiles configured in Paseo](/public/img/larping/profiles.png)

It turns out that additional tool configuration, skill configuration, and directory scoping are simply not necessary.  When a lead agent sees the profiles, the profile notes act like prompt-injected context and the agent knows exactly how to build and coordinate a team.

But I noticed quite a bit of inconsistency in how the team operated and how well sub-agents were being utilized.  To fix this required tweaking Paseo's shared system prompt for agents:

```markdown
<operating_environment>
You are operating in a Paseo agent harness; `paseo-*` skills and `paseo` CLI are available (as needed to explore the workspace, other agents collaborating)
</operating_environment>

- Use and prefer `paseo` built-in browser tools
- Use `paseo-*` skills to coordinate with the team
- When starting a new agent, always let them know their identity, specific role, ownership of responsibilities, and how they can contribute; set clear expectations at initialization.
- Use XML *when initializing new agents*: <role>(sub-agent role, identity; them)</role>, <lead_role>("self", you; identity + role)</lead_role>, <objective>(sub-agent's goal)</objective>, <instruction_details>(existing context, inputs, info, details of task, tips, references, etc.)</instruction_details>, <scope>(what do vs what not do)</scope>, <team>(other sub-agents, team info, responsibilities, roles; as needed)</team>, <gates>(any specific exit gates, checks; only as relevant (optional))</gates><comms>(specific instructions for comms, what to report, when to report, format of report, etc.; optional as needed)</comms>
- Avoid rework and retread: provide a solid instruction_details, current objective, responsibilities, expectations, best practices, who owns what, scope of work, specific commands if needed, reference files (source, notes, links/URLs, docs, etc.) if relevant.  Do not let your team guess what you already know; provide the key, learned insights at initialization.
- Use clear, direct, terse language; be specific where needed
- Use a "risk-based" approach when assembling team to work on a task; assign work that has higher risk to more senior engineers
- Create workspaces (worktrees) only when work benefits from isolated branches (parallel, overlapping work).  Coordinate edits otherwise.  Worktrees add some complexity.
```

The key is actually the 4th clause (reformatted here for clarity):

```markdown
Use XML *when initializing new agents*:
<role>(sub-agent role, identity; them)</role>,

<lead_role>("self", you; identity + role)</lead_role>,

<objective>(sub-agent's goal)</objective>,

<instruction_details>
(existing context, inputs, info, details of task, tips, references, etc.)
</instruction_details>,

<scope>(what do vs what not do)</scope>,

<team>
(other sub-agents, team info, responsibilities, roles; as needed)
</team>,

<gates>
(any specific exit gates, checks; only as relevant (optional))
</gates>

<comms>
(specific instructions for comms, what to report, when to report,
format of report, etc.; optional as needed)
</comms>
```

What this does is that it shapes the instruction that the lead provides to the sub-agent when it initializes it.  Now the lead has two key things it needs: 1) my instructions on what to ask of each agent profile and 2) how it should ask for it.

### Real World Examples

For the doubtful, it is instructive to see exactly how a lead uses this when it decides to create a new sub-agent:

![Example of an agent prompt to a sub-agent](/public/img/larping/agent-prompt.png)

You can see the structure, but more importantly, you can see the *detail*; far richer and specific than anything a human might write.  The system prompt guides the lead to structure:

- The **roles**, responsibilities, and ownership boundaries of both parties
- The **objective** as well as the **detailed instructions** with context about the task that has already been resolved either upstream or in other agents
- The **scope** of work, bounding what the sub-agent does
- Visibility into the **team** so the sub-agent knows who to communicate with and who owns what
- Specific completion **gates** that define "done" for the sub-agent
- Rules for **communication** including when and how to report back


A few days back, I shared my [Paseo (Apache 2.0, no affiliation)](https://paseo.sh) setup in a Hacker News discussion:

![Comment from Hacker News](/public/img/larping/youre-deep-in-it.png)

One of the more interesting comments called this out as "larping" (Live Action Role Playing) with agents.

## Observations

## Why This Works

## How to Set It Up

## Extending Beyond Agents

Key benefits

- Spread token use across platforms and models
- Divergent thinking and problem solving utilizing multiple agent perspectives
- Context control, especially when used with memory systems and auto-archiving
- Focused agent behaviors and responsibilities

"bi-directional prompt injection" -> the profiles inject instructions into the lead -> the lead injects instructions into the sub-agent specific to the task

----

## References

1. https://arxiv.org/abs/2606.01770
2. https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices#give-claude-a-role
