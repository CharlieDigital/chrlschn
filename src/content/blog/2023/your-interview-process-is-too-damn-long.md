---
title: "Your Interview Process Is Too Damn Long (and How To Fix It)"
description: "Long interview processes have become a bane in the tech industry. Can we fix it?"
pubDate: "2023 Oct 19"
socialImage: "/public/img/interview/too-damn-long.jpg"
slug: "2023/10/your-interview-process-is-too-damn-long"
tags: "musings,software engineering"
---

----

## Summary

- Long, convoluted interview processes waste time for all parties and ***such interviews often yield the "best-worst candidate"*** because such processes are more accessible to candidates with time to participate in a drawn out interview process (unemployed, underemployed, or simply unengaged with their work in the first place) and thereby filtering out potentially better candidates.
- If your interview process includes a take home, consider what would really blow your socks off if you saw it in the submission.  Rather than issuing a take home, can you structure a series of  questions or a simple exercise in such a way to illicit the same response from a candidate with a 10-15 minute session instead?  In other words, ***can you design a better experiment?***  A take home isn't palatable to many candidates (leaving you with the best worst candidates) and also requires an investment in time on the hiring team to review as well.
- In a landscape where technology is constantly changing, ***the ability to identify candidates with a "growth mindset" is highly underrated***.  These candidates can grow quickly with your team and adapt to shifting priorities, changes in technologies, and rapidly absorb business requirements.  There's no magic formula, but candidates who know what they don't know and quickly acknowledge these gaps, candidates who ask great questions, candidates who show curiosity will often be individuals with a growth mindset.
- A more efficient process ultimately lets a hiring team evaluate more candidates faster and with less wasted motions.  Therefore, ***a tight, well-designed process means that your team gets to the pick from a larger pool of candidates with less effort*** and a better chance of closing your best candidates rather than the best-worst candidates.

----

## Intro

![Reddit post](/public/img/interview/reddit-post.png)

A [Reddit post on r/dotnet](https://www.reddit.com/r/dotnet/comments/1788wag/optimizing_net_interviews_for_better_results/) recently caught my attention.  The subject was regarding optimizing interviews and how to get better results.

I have had the opportunity to participate in many interviews at different organizations ranging from founding teams to seed-stage startups to enterprises since exiting a 10 year tenure at the end of 2020. Indeed, today’s interview process feels decidedly more *excessive* compared to those of the decade prior.

Nowadays, interviews seem to have adopted a level of complexity not unlike what we see in web front-end development: at the end of the day, it's just HTML, CSS, and JavaScript, but now we have layers on layers of complexity for building what used to be simple, 10 line scripts.  Like modern web front-ends, the interview process seems to have evolved from something that should be fairly straight forward to something decidedly Rube Goldberg!

![Rube Goldberg](/public/img/interview//rube-goldberg-banner.png)
*What tech interviews feel like in 2023 and how we can fix it.*

From ***4-day*** take-home assignments to timed leetcode challenges to long, drawn out, multi-session, multi-team, 6 hour+ interview processes -- it all feels a little unnecessary and frankly quite inefficient for all parties involved.  Particularly so given tech's infatuation with efficiency and productivity.

***But the problem is that many of these practices simply result in selecting the "least worst" candidates*** because it filters out many great candidates that don't want to or do not have the scheduling flexibility go through these long slogs.

As a technical lead and director of engineering at the aforementioned small bootstrapped startup for almost 10 years, I had the opportunity over that time to personally interview and qualify dozens of candidates.  I also occasionally did contract work to help other companies perform technical screenings for key roles.

My approach to hiring focused on 3 simple, high-level objectives:

1. Make the process efficient and not waste my own time, my team's time, nor the candidate's time.
2. Identify candidates that fit with the team and culture *on one key trait*.
3. Reduce the latency from initial interview to decision and offer

---

## Efficient use of time

Selfishly, I hate wasting my own time with protracted, multi-session interview processes.  To that end, for any particular type of role, I end up designing a tight set of technical questions that aim to identify a candidate's level of experience in 10-12 questions/exercises for any given topic.

Take an example of a C#/.NET backend engineer; I ended up with an assessment that only requires ~45 minutes and 10 questions to determine a candidate's level of seniority and experience:

1. Write a simple generic method
2. What is the C# `default()`
3. What are `Func` and `Action`
4. Presented a simple skeleton class, demonstrate usage of `Func` and `Action`; I ask the candidate to write a function that takes a `Func` or `Action` and invoke it; bonus: make it generic.
5. Make the previous example `async`; explain `Task` vs `ValueTask`
6. Write a simple extension method
7. `IDisposable` - describe and implement; vs `finalizer`; explain GC at high level
8. Interface vs abstract class - when one or the other?
9. Methods marked `virtual` vs `abstract`
10. Given an RPC endpoint like `ExecuteOperation(string operation, int id, string value)`, provide a rough implementation that supports the operations `'add'`, `'delete'`, `'update'`. Now we want to add `'archive'`; how can we implement it in a way that's extensible and maintainable?

The first two are on generics. The next three are on passing functions around and `async/await`. The next two are on specific language/platform features. And the last 3 are on high level OOP and reflection and/or source generators.

You can see the progression on the last 3. The last one, if the candidate starts with an `if-else` or `switch-case`, I ask how they would make it more extensible and maintainable and iterate on the design. The goal is to see which constructs and techniques they already know. If the candidate can come up with an `OperationBase`, `AddOperation`, `DeleteOperation`, `UpdateOperation`, and `OpTypeAttribute(string operation)`, then we're pretty much all set. Alternatively, if the candidate suggest `if/else` or `switch/case` with source generators and why it would be preferable to reflection (perf, AOT), even better!

Put it another way: ***what are you hoping to see in a take home exercise and can you just reduce the interview to just seeing if a candidate can produce that desired output? What solution or approach would be a "home run" if you saw it in a take home? Can you create a simple scenario that can yield such an outcome without all of the cruft?***

Here's a perfectly satisfactory solution to #10, the only open-ended question (code doesn't have to work):

```csharp
class OpTypeAttribute : Attribute {
  OpTypeAttribute(string name) { }
}

abstract class OperationBase {
  abstract void Execute(int id, string value)
}

[OpTypeAttribute("add")]
class AddOperation : OperationBase { }

[OpTypeAttribute("delete")]
class DeleteOperation : OperationBase { }

[OpTypeAttribute("update")]
class UpdateOperation : OperationBase { }

[OpTypeAttribute("archive")]
class ArchiveOperation : OperationBase { }

ExecuteOperation(string operation, int id, string value) {
  // Resolve operation using attribute and reflection
  // TODO: Add error handling for invalid operation types.
  var operation = ResolveOperation(operation);
  operation.Execute(id, value);
}
```

It can be easily done in 5-10 minutes; doesn't require running code -- just demonstration of understanding of how it *could* be done; allows for a lot of discussion on advanced techniques (reflection, source generators), tradeoffs (performance, maintainability, modularity), and error handling; and let's me talk a candidate through the solution (*more on this later*).  But you can see that the questions build on each other and allow demonstrating expertise around one simple exercise.

I can ask about adding `async/await`.  I can ask about making the operation generic.  I can ask the candidate to add in error handling.  I can ask the candidate to add in input checking.  All in one simple exercise.

While the above questions are C# .NET specific, it's not hard to apply the same principles to CSS, JavaScript, TypeScript, SQL, Go, Rust, Ruby, whatever key stack pieces are being selected for.

For example, a satisfactory solution to #10 in TypeScript:

```typescript
type Operation = 'add' | 'delete' | 'update' | 'archive'

// import
function addOperation(id: number, val: string) { }
function deleteOperation(id: number, val: string) { }
function updateOperation(id: number, val: string) { }
function archiveOperation(id: number, val: string) { }

const operationHandlers: Record<Operation, (id: number, val: string) => void> = {
  'add': addOperation,
  'delete': deleteOperation,
  'update': deleteOperation,
  'archive': archiveOperation,
}

function executeOperation(operation: Operation, id: number, val: string) {
  // TODO: Check if we have the key or not
  const handler = operationHandlers[operation]
  handler(id, val)
}
```

These questions are designed so that I can very quickly understand:

1. What does the candidate already know?
2. How quickly can they pick up and understand the existing codebase?
3. What will we have to teach them?
4. What should we pay them? (level of experience and seniority)

There's really no need to bother with data access, unit testing, project setup, algorithmic complexity, etc. because the assumption is that:

1. There is going to be a body of code in place or an existing one to model; I'd rather a candidate that can follow the existing patterns and practices than one that will go rogue.
2. Tools, techniques, and best practices can be taught and trained if we like a candidate; skill gaps can be filled quickly as needed if we hire the right people.
3. Important design decisions where there may be algorithmic or system design complexity are usually performed as a team; not as a one-person decision.  Not only that, we'll usually research, sandbox, and try different algorithms and designs anyways if we are working on a really critical part of the system.
4. There is no one "right" design or implementation; "right" is heavily situational and contextual to the business objectives; moreover, complexity in systems is emergent over time and based on shifting demands. This means that arriving at a "right" solution often requires a lot of history and situational context.

If you believe the above to be true, *then you don't need a sum of 6+ hours to select a good candidate*; you only need 10-12 well-selected technical exercises and 1-2 hours.

Perhaps even more important than technical acumen, is whether the candidate is a good culture fit in one specific trait...

---

## Identifying a growth mindset

[In an Inc. article, This Is the Book That Inspired Microsoft’s Turnaround, Justin Bariso writes](https://www.inc.com/justin-bariso/this-is-book-that-inspired-microsofts-turnaround-according-to-ceo-satya-nadella.html):

> It’s been almost five years since Satya Nadella became the third chief executive of Microsoft. When he took over, the company was in the midst of an identity crisis. Many felt Microsoft had become lethargic, content to ride a rapidly dissipating wave of success while competitors charged forward with new, innovative ideas.
>
> But in those five years, Nadella has conducted a stunning turnaround. With increased efforts on attracting young talent and changing the “old guard” culture…

That book is Carol Dweck’s *Growth Mindset* (recommended!) and this is the key: the ability and drive to continue to grow, inquire, introspect, and learn.  It is the ability to acknowledge that one does not know what one does not know, but the goal should be to have a growth mindset to seek that knowledge without an ego and without shame.

I have hired a candidate that failed almost every item on a technical assessment. But the way he failed revealed an innate sense of curiosity and drive to learn.

When I start the interview, I preface and say that if the candidate doesn't know the answer to a question, they should say so right away and we can either skip the question or I can walk them through it.  This is really key because if you run even a few of these interviews, I can quickly identify candidates that have an *ego* and a *fixed mindset* versus one that has a *growth mindset* and is capable of rapidly learning.

The short technical assessment is designed to be easy enough for an experienced engineer to complete without much trouble.  But to find those gems that can be brought up to speed quickly, I'm looking for something that's perhaps a bit more *subjective*: how they react and respond when they hit something they don't know.  Do they readily acknowledge it?  Do they ask for help? Do they take guidance well when given?

Engineers with a growth mindset can adapt quickly, learn what needs to be learned, explore what needs to be explored, and aren't afraid to un-stuck themselves by asking questions and seeking help.  A growth mindset also tends to be a strong correlation with a candidate's humility and lack of ego.  To me, these are key traits that are often overlooked.

---

## Reducing latency

Part of the reason why having an efficient, focused process is important is that when working with *active* candidates, they are likely concurrently interviewing across multiple opportunities.  If you find a great candidate that is a fit for yor culture and can quickly plug into the team, the biggest favor you can do for yourself -- especially as a small team -- is to act expediently and reduce the *latency* from initial screening to the offer.

Consider a typical take home:

1. **(1d)** Meet with the candidate 30-60 minutes first.
2. **(1-7d)** Assign a take home, but it would need to work within the candidate's schedule which may mean a weekend.
3. **(1d)** Once the take home is returned, review the take home submission first and schedule a followup.
4. **(1d)** Because a candidate could ostensibly "cheat" on a take home, this final session needs to be a hands on coding session again to actually ask the candidate to extend the project in some way while watching.

Remember the earlier comment about the increasing complexity of web front-ends?  It is easy to see that to even support this protracted process requires complex software to manage candidate tracking, scheduling, and calendar reconciliation across 1-3 weeks.  The excessive process *is* the source of complexity, friction, and latency.

In a competitive market or for a great resource, it is easy to lose out with an excessively long process and not showing decisiveness. This extended process creates multiple opportunities for attrition in the funnel due to either time constraints or other opportunities getting to an offer faster; ***the net result is picking from the "least worst" candidates -- ones that had no other choice but to stick around***.

Rather than jumping through all of these hoops, using a focused set of questions that measure for experience and seniority  --  in 1 single live coding session - -  seems far better at reducing the latency involved with more drawn out processes since the time expended per candidate is significantly lower.  The side effect is that it allows a team to *compare more candidates* in a smaller window; if a process can qualify candidates faster with lower attrition rates in the funnel, it yields access to a bigger pool of candidates.

But this also requires a shift away from a focus on static measures of present knowledge and skills against current needs and towards perhaps a more subjective sense of a candidate’s level of experience and growth mindset.

---

## Closing thoughts

If like me, you also believe that interview processes have gotten out of hand or you're just trying to gain back more of your time and sanity, *be the change that you want to see*.

Rather than focus technical interviews on long, multi-hour or multi-day take homes, break it down to a few core exercises that measure a candidate's depth of knowledge.  A simple heuristic is that if existing senior resources on a team couldn't provide a satisfactory solution in 5-10 minutes off the top of their head, it's too complex.

Acknowledge that algorithmically challenging work rarely happens in a vacuum and often involves collaborating with peers, research, and discussion so it seems a silly primary facet to select for unless the field demands it (e.g. computer graphics, ultra low-latency systems, mathematical computation, etc.).

Finally, find ways to seek those with the right culture fit and a growth mindset because good *teammates* are hard to find and the specifics of a tool or platform can be taught and learned.

---

## Addendum

Check out my OSS project [CodeRev.app](https://coderev.app): a simple, lightweight tool designed to facilitate code review as interview.

And the blog post [Interviews in the Age of AI: Ditch Leetcode - Try Code Reviews Instead](https://chrlschn.dev/blog/2023/07/interviews-age-of-ai-ditch-leetcode-try-code-reviews-instead/).
