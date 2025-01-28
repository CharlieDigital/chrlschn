---
title: "Lessons Learned from Working at Startups"
description: "Self-note on some lessons learned from working at a variety of startups over my career"
pubDate: "2024 Dec 04"
socialImage: "/public/img/5-startup-lessons/plant-sprout.png"
slug: "2024/12/lessons-learned-from-working-at-startups"
tags: "startups"
---

----

## Summary

A collection of lessons learned from working at various startups over the years from bootstrapped startups in the enterprise commercial-off-the-shelf space to VC-backed seed/series-A/B/C stage Silicon Valley startups with non-existent/mediocre/explosive growth.

I've founded a few companies (that went nowhere) and worked with great co-founders and *questionable* co-founders *(lessons learned!)*.

I am an engineer so each of these lessons was learned from that perspective.

If you're somewhere in your journey with a startup as an engineer, I hope that these can be useful lessons.

(To be updated periodically)

---

## On Competitors

Having competitors is probably a good thing because it validates that there is a market of people that have a real problem to solve and will pay to solve that problem.  Within that market, then, there is a lot of room to differentiate based on price, featureset, usability, market segment, industry verticals, etc.

Rod Cuthbert -- co-founder of Viator -- makes an interesting point [in an interview with Guy Raz](https://wondery.com/shows/how-i-built-this/episode/10386-viator-rod-cuthbert/) (around the 01:01:00 mark):

> Because we were the first and there wasn't any competition, we were able to buy advertising and also get organic traffic relatively easily. But conversely, not having competitors was not great. When you've got competitors, you know, they bring customers into the market as well, and those customers, you know, look at their website and then a percentage of them will say, well, who else does this? What are the competitors that I can look at? So if there's other people out there who are also doing marketing, that's good for you as well. There was nobody out there who was marketing tours and activities.

Competitors are good because some of those customers want an alternative -- be it cheaper, easier to use, more focused, some use case not covered by an incumbent, etc.

Your job as a startup becomes to understand which part of the market you are going after and how you'll sell into that market.  In other words, what's your go-to-market (GTM) strategy?

NDAs have some legitimate use cases, but in most cases, you can freely tell your idea to anyone and let them poke holes in it.  If your idea is so easy to execute that any team could build it and sell it, then it probably wouldn't have really had any moat.

**Advice to self**: spend a good amount of time researching and understanding the other competitors in the space.  Where are there gaps?  What does the market segmentation look like?  What are people complaining about on Reddit?  Is there a specific industry or vertical where there is still an opportunity?

## On Non-Technical Co-Founders

If you are thinking about being a technical founder of a startup, the hardest challenge probably isn't technical at all; the hardest thing to do is to find the right non-technical co-founder.

The role of the CEO of a startup -- as I have come to understand it now -- is to bring customers and book sales, secure funding, and drive the overall GTM strategy and vision of the startup as a business (how it will make money).  This is probably one reason why YCombinator recommends having a team instead of going solo because the early days of sales and marketing is a full time job in and of itself.

It's risky to form a startup with a non-technical founder without some proof or very strong evidence that they can hit those key deliverables because without cashflow, there is no business.

Potential non-technical co-founders will *insist* that if you build it, customers will come. As an engineer, your *instinct* is to build.  **DO NOT build it until there is proof that there are customers** (see next section on MVPs).  Build the simplest possible asset -- a deck, a Figma, etc. -- first and have the non-technical co-founder *sell that*.  "Sell" in this context does not mean "Oh, that's a great idea; I would totally pay for that"; "sell" means "here's some money; I want to be the first investor/customer/user". If they cannot even sell it to their close circle, there is probably no chance.  Only build if it's for fun or exploration and in rare cases, when the idea is so novel (but maybe in that case, all the better if you can first distill it down into a simple pitch).

You don't have to see eye-to-eye on everything with your co-founder, but you have to have similar value systems and understanding of how to agree to disagree from time to time and still move forward.  In short, "vibes" are very important with a co-founder and it's very hard to quantify it (see *Culture* below).  You can use various tools like:

- [First Round's 50 Questions](https://proof-assets.s3.amazonaws.com/firstround/50%20Questions%20for%20Co-Founders.pdf)
- [YCombinator's 10 Questions](https://www.ycombinator.com/blog/10-questions-to-discuss-with-a-potential-co-founder)
- [Founder Institute's 34 Questions](https://fi.co/insight/34-questions-to-ask-a-potential-co-founder)

**Advice to self**: ideally evaluate a non-technical co-founder by how many *paid* deals they can close without even having a product.  Can they get customers to be on a *paid* waitlist?  Can they prove that there is a market and demand with the most minimal and barebones MVP?  Make sure that you match on some key facets of value and culture.  Form the company *at the last moment possible* because this lets you really get to know the co-founder before committing equity and makes it easier to break up if something feels off.  Formally creating a business entity with a co-founder is just like a marriage (a legally binding contract that has tax implications) so do it with extreme caution.   ***If a potential non-technical co-founder just wants you to build their idea, then they should be pointed to Fiverr or Upwork; DO NOT ENGAGE.***

## On MVPs

A big part of the early days of the startup is actually validating the assumptions and initial research.  When doing so, do the minimum necessary (unless you are doing it as a technical learning/exploration exercise).  The more you build, the further you'll be off course because only customer feedback (read: what someone will pay for) determines what a viable business is (this doesn't always hold true, especially for things like social media startups).

In the hierarchy of things you can do before actually building a technical MVP:

- Reach out to folks in your network if you have deep industry connections and see if you can get people to put cash down for promise for access to the upcoming product.  Don't build anything; if people will hand you money for something that doesn't even exist yet, then you've found a painful (and valuable) problem to solve.  If you can sell it with just a description, then that's amazing.  If you can sell it with a deck, also a strong signal.
- Reach out to a wider network and see if you can get people to sign up for a wait list.  If strangers will sign up for a waitlist, that's a strong signal.  A *paid waitlist*?  Lock and load.
- Build the simplest MVP possible that solves some minimum problem set that people will pay for.  AI generated is OK.  Unless you are a deep domain expert in a narrow space, it's very hard to know exactly which problem your customers need to solve and the externalities of how they want to go about solving it.
- Just book your first payments manually if you have to; don't worry about building an integration.  In fact, if your process has friction and people will still pay you, then you know you're on a very hot opportunity.
- It's OK if people complain and tell you that they won't pay you because of X, Y, Z.  They've just told you "I *would* pay, but for X, Y, Z".  Get them to commit to paying you if you solve X, Y, Z.  If you get the same X, Y, Z from multiple rejections, then the roadmap has defined itself.
- It's OK if customers churn; expect people to churn.  Don't worry about breaking things and causing customers to churn.  At Motion, customers were constantly churning early on for sometimes head-scratching reasons.  What is important is to capture the reason why they are churning.  This becomes the priority queue of problems to solve to triage the churn.

**Advice to self**: don't build anything until someone has committed to paying.  Only build if it's for fun or if the idea is so out there that you really need to see it to understand (but there's a lot of inherent risk there).  When you build, don't overbuild until it is clear what customers value ($$$).  Perfect is the enemy of shipping.

## On the Elusive Product Market Fit (PMF)

Building a startup is like building a fire with damp wood...in the rain: at some point, it's just a spark, some smoke, some smoldering embers.  It takes effort, knowledge, and skill to turn that into a self-sustaining fire.  There is a clear turning point when the effort required to grow the fire shifts and the fire simply needs to be fed with more fuel.

I think this is the best analogy I have for PMF: that bifurcation point from *building* the fire to *feeding* the fire.

**Advice to self**: every business, every market, and every team is different so there's no singular timeline or pattern for PMF.  But there will be a turning point when the type of effort required to grow the revenue stream shifts and that is when you've reached PMF.

## On Startup Culture

Culture is make-or-break when it comes to startups.  The environment can be extremely stressful at every stage and creating the right culture can help smooth rough waves and keep the team dynamics aligned.

I particularly like [an essay written by Brian Chesky](https://medium.com/@bchesky/dont-fuck-up-the-culture-597cde9ee9d4) of Airbnb fame:

> ​By upholding our core values in everything we do. Culture is a thousand things, a thousand times. It’s living the core values when you hire; when you write an email; when you are working on a project; when you are walking in the hall. We have the power, by living the values, to build the culture. We also have the power, by breaking the values, to fuck up the culture. Each one of us has this opportunity, this burden.
>
> Why is culture so important to a business? Here is a simple way to frame it. The stronger the culture, the less corporate process a company needs. When the culture is strong, you can trust everyone to do the right thing. People can be independent and autonomous. They can be entrepreneurial. And if we have a company that is entrepreneurial in spirit, we will be able to take our next “(wo)man on the moon” leap. Ever notice how families or tribes don’t require much process? That is because there is such a strong trust and culture that it supersedes any process. In organizations (or even in a society) where culture is weak, you need an abundance of heavy, precise rules and processes.
>
> There are days when it’s easy to feel the pressure of our own growth expectations. Other days when we need to ship product. Others still where we are dealing with the latest government relations issue. It’s easy to get consumed by these. And they are all very important. But compared to culture, they are relatively short-term. These problems will come and go. But culture is forever.

Write down and articulate how you want the team culture to operate and hold each other accountable to that culture.

One of the reasons I joined Motion was that I really identified with [how the founding team outlined their culture](https://motionapp.notion.site/Motion-Company-Culture-21bd766b1f864764b0db2fa72529b182). There are a couple of bangers like "disagree and commit", but honestly, every bullet there makes total sense for an early stage startup.

**Advice to self**: when founding a company, clearly establish and communicate the culture early and of course, co-founders should agree on culture.  If you hire the wrong person and it turns out that they don't fit the culture, be swift in removing them from the team because one poor fit can have a big impact on the overall vibes of the team.

## On Tech Selection

Generally, select technologies that are:

1. **Widely adopted and easy**.  I like Vue and React as SPAs for most use cases that don't require SEO.  I would personally not default to Nuxt nor Next unless the startup needed SEO.  I like Vue over React because there are less foundational choices to make (case in point: Pinia vs. MobX/Valtio/Jotai/Zustand/Redux/Context/Recoil/Nanostores, etc.).  Postgres is a great choice for backend and you can start free with Supabase.  Firestore is another great choice if you prefer document-oriented DBs since the free monthly grant is generous.
2. **Free or very low cost to use**.  This can facilitate experimentation early on without pressure.  Supabase, Firebase, Azure CosmosDB Serverless, Google Cloud Run, Azure Container Apps.  I would avoid Lamba or Azure Functions for lock-in (unless you know that you only need AWS or Azure).  True story: at Ownit, many of our commerce customers are on Google or Azure and will not run workloads on AWS.  Cloud Run and Container Apps are particularly good for compute because containers are portable and provide the flexibility to run on-prem, in another provider, in managed K8s, etc.  Additionally, you're not limited to supported frameworks or runtimes like you would be with compute instances like Elastic Beanstalk, for example.
3. **Productive and fast to iterate**.  If you're building a web API, Rust is probably not a good choice. If you're polyglot, then pick the language, platform, and runtime framework that can let a small team move fast with the least footguns.  Productive also means "don't waste time doing non-product work" like updating/managing dependencies. C#/.NET is highly underrated in this space because of how good EF Core is for productivity and how generally stable and mature it is.  Firebase is fantastic as an all-in-one platform for early stage and there's a good ramp to later stages of maturity with Cloud Run and GKE.  Be wary of AWS Amplify; it has more footguns and a high complexity cliff that manifests differently from Firebase.

Things you *probably* don't need (of course, context dependent):
- Microservices
- Kubernetes
- Multi-repos
- Terraform
- Serverless ***functions***
- *System* telemetry

Things that contribute to speed and adaptability:
- [Monoliths](https://chrlschn.dev/blog/2024/01/a-practical-guide-to-modular-monoliths/)
- Mono-repos
- [Serverless ***containers***](https://youtu.be/GlnEm7JyvyY)
- Minimal infrastructure
- Simple build/deploy automation
- Feature flags
- *User* telemetry
- An appropriate level of documentation or notes (Notion or `README`)

Documentation is underrated because it can seem like a waste of time in the moment.  But having good documentation makes it easier to "stack dump" for someone else or even for yourself when you come back to some decision/system later.  It need not be complicated or formal.  If your deployment is executed by a series of scripts, document the scripts and the order until you build the automation.  If the environment is set up with a series of CLI commands, jot those down as you execute them.

Terraform and K8s are particularly potent red flags for early stage startups.  Motion didn't even have a "test" environment for a very long time; everything just shipped to production.  If you break it, revert it.  Terraform, CDK, Pulumi, and IaC in general only make sense if your infrastructure is complex (otherwise, you can just manually recreate it), you need to deploy multiple copies of it, or you need it to be recoverable.  None of which make sense for early stage startups.

**Addendum 1**: don't default to NoSQL databases unless you already have a strong sense of how your data model and use cases look.  For [Turas.app](https://turas.app), it makes total sense because each itinerary is "bounded" in scope and it is easy to naturally see the document(s).  In many other use cases, a traditional relational database will be more flexible and offer less footguns.

**Addendum 2**: it's generally never too early to use queues (SQS, Pub/Sub, Cloud Task Queues, Azure Service Bus, [even Postgres](https://leontrolski.github.io/postgres-as-queue.html)).  The complexity and cognitive overhead is low, but the flexibility you gain is high.  You are almost always going to need a queue for any part that needs to scale asymmetrically; might as well start with a queue since the overhead is so low.

**Advice to self**: avoid Next.js or Nuxt.js unless there is a clear need for SSG/SSR for SEO purposes.  If needed only for content site, use only for content site.  Vite + Vue/React SPA is fast to iterate, easy to deploy and manage, and low complexity.  CloudFormation is an expensive AWS tax that gets incurred on deployment; prefer Google Cloud Run or Azure Container Apps for speed of iteration and deployment.

## On Early Engineering Hires

Ownit's early team was primarily ex-Amazon with several senior ex-Amazon engineers.  The problem with ex-`[BIG TECH]` engineers -- who haven't worked in startups before -- is that they will tend to over-engineer in the wrong ways and make things too complicated.

Often times, these engineers are not going to be well-rounded enough since their careers may have been siloed due to many layers of the tooling being provided for them by other teams; they may not have built a full, functioning system from zero.

![Screenshot of YC session.](/public/img/other/ycomb-faang.png)
*YC partner Harj Taggar dedicated a part of his session one year to discussing the potential pitfalls of hiring ex-FAANG in early-stage startups.  His observation across many teams was that ex-FAANG are very good at going fast from 0-1, but poor at iterating and finding their way through the fog of those early days.*

Indeed, a sub-set of those early engineers on the Ownit team required a lot of oversight and direction to function.  Much of the output and system design was prematurely complicated (including adoption of microservices and repo-per-service (the Amazon way)) and slowed the team's ability to iterate quickly.  Some were not able to ramp up and adapt new skills that might have been handled by the larger engineering "scaffolding" at an organization like Amazon.

A key part of selecting for good engineers for early stage startups is finding people with the right mindset.  In particular, things are very messy and unclear -- requirements are constantly changing due to customer feedback.  Prefer builders and engineers with evidence that they've built *something* 0 to 1. Some folks don't deal with that well so part of the job is to identify people that can thrive (or at least tolerate) that type of environment without creating grief for the rest of the team.

**Advice to self**: not every FAANG engineer is cut out to function in the wild-west of early stage startups. With the wrong mindset, they can inadvertently sabotage the startup by building a system that's too complicated, too early.

## On Joining Early Stage Startups

There are several things to consider when joining early stage startups depending on what you're prioritizing for.

Of course, culture and team are important, but so is probability of success, especially when accepting reduced cash compensation in exchange for equity (hard lessons learned here).

Some questions that to ask about the business:

- **What does the current runway look like?** If the business is not cash flow positive, how long does it have to burn if it can't close another round of funding?
- **How many customers and what does the revenue look like?** It's important to understand how the startup intends to make money and evaluate whether the model is realistic or not.  Do some simple math to see if the business makes financial sense.  Round up the cost of each resource to $15,000 per month and see if the plan makes sense.
- **Who are your competitors and what are they doing wrong? What are they missing?**  Understand why this startup is unique and what their advantage is.  Has the team done their homework on this space and the different competitors?  Is their GTM and market segmentation viable?
- **What is the total addressable market (TAM) and what is your plan for acquiring customers?** Understand how big their space is and how the team plans to acquire customers. Facebook ads?  How much are they budgeting for that?  SEO?  What is the current impressions and click through rate?  How well are they converting?  How realistic is the plan?  Basically, real metrics for the team's approach to acquiring new customers.  If there are no metrics, you should approach with caution.
- **What's the roadmap look like and can you share some customer feedback?** Customer feedback is good because it means there are customers to give feedback.  If there aren't enough customers yet, get a sense of how the team is shaping their roadmap; what is their basis for their overall direction?  Does it make sense?
- **What does the process look like to shape the roadmap?** The goal is to understand how the team is connected to *some* user base.  It could be *potential* users, it could be paying users; but the goal is to ascertain how the team is driving their features and requirements from user feedback.
- **What does product market fit look like for this product?** Have the team articulate what PMF looks like in their mind and see if this is realistic and makes sense.  Remember the analogy above: it's the turning point from when you're building the fire to when you're feeding the fire.  What does that look like for this business?

Some questions that to ask about the technology side:

- **Why Terraform/microservices/serverless functions/multi-repos?**  For early stage startups, these can be bad signs that the team is going to be too overly focused on complexity and not (little "a") agile enough to pivot quickly.
- **How does the team make hard architectural/system design decisions?**  Understand how the team debates some of the hard stuff.  How do decisions get made?  How is input evaluated?  How does the team deal with necessary and accidental complexity?
- **What does the engineering roadmap look like in the next 6, 12, 18 months?  What are the biggest technical challenges?**  *Understand what you're getting yourself into*.  Is it work that you want to do?  Or will you feel boredom and dread when you wake up each day?

Some questions to ask about the team/culture side because often, as an IC, you won't have much control over this so you want to make sure that it aligns with how you want to work:

- **What's the meeting culture look like in a typical week?**  True story: I once resigned from a startup after 3 days upon learning that the founder and CEO wanted an all-hands meeting every day with a team of ~20.  So this would normally eat up 1h *each day*.  It is vitally important that if you are an IC that thrives on flow state, then you actively ensure that you find the right fit.  I always ask this question now.
- **What do your processes look like?  How do you expect them to change in the coming months?**  At Motion, mistakes were made.  Many.  Even by the head of engineering.  In production.  But the culture was one that truly bought into "move fast and break things".  The team accepted that these things would happen with a lack of process and controls.  Understand if the team process fits how you want to work.  Different domains have different levels of rigor.  When I was at Apprentice, the team was just transitioning from "loose and fast" to "rigid and slow" as a requirement to support GxP processes in life sciences; this wasn't what I was interested in.

In most cases, I have found startup founders to be receptive of these types of questions because it shows that you actually care about the quality of the startup and overall fit -- that you're not just looking for a job, but a place where you feel like you can be part of a journey.  Don't be afraid to ask hard questions to founders and if founders avoid sharing these insights into their business, it's probably a red flag.

But you know what?  Sometimes it's *OK* to join a startup because it looks like it'll be fun; there's something to be said for working with a great team, even if the team doesn't produce some product that reaches commercial success.  Life is short; live a little.  I don't regret taking 6 months off to work on [Turas.app](https://turas.app) (a little bit of regret leaving Motion early 🤣).

**Advice to self**: really understand the business, the team, and how the team operates before committing.  There are so many startups out there and so many interesting things to work on -- even your own projects -- that it is vitally important to consider whether the fit is right beyond just the $$$.
