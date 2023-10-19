Let's break down some trends I've noticed that I think simply don't work:

---

## The many ways interviews are broken

### Long take home projects

One startup started their process with a *4-day* take home before even a virtual face-to-face to see if there's a good culture fit.  To be frank, even 4-hour take homes are probably too long.

I understand the appeal of take home projects because it can provide a holistic picture of the candidate's skills.  But it has an inherent flat: because a candidate could have someone help them, you end up *increasing your workload because now you have to review the work AND have a followup session where they code some change or add-on in front of you*.  It's a waste of everyone's time from the candidate's to your own engineering team's time.

Additionally, this approach can ultimately filter for the wrong candidates: those that have the long blocks of time to work on such projects.

**Alternatives**:

- **Review their existing code**. If you really want to see how a candidate codes more holistically, ask for access to a side project they are working on or review their personal GitHub projects if they have one.  Yes: not everyone has side projects and GitHub repos, but if they do, the why not start there?
- **Pay the candidate for it**.  At one seed-stage startup, the CTO presented me a real-world, well-isolated, small-scale project they were working on and offered to pay for it.  At the least, offering to compensate a candidate for their time will prevent some early attrition.
- **Make it fun**.  The urge is to use a project that's representative of the company's work; I get it.  But why not make it more engaging and make it fun?  If the candidate is interested in making a small game, then why not?  Offering something that the candidate would *enjoy* building is a good tradeoff that can prevent some early attrition in the pipeline.

### Over-emphasis on engineering complexity

For example:  *"How would you build Twitter?"* If any of us could design Twitter in 30 minutes, then Elon should personally pay us $44bn. Twitter and Facebook are the product of *years* of mistakes and learning built by *thousands* of talented engineers; it's absurd to reduce it to a 30 minute talking point.  Besides that: your company is an enterprise SaaS; why compare it to Twitter?

It is equally absurd to think that any given company's technical challenges are on the scale of a Facebook or Twitter.  Why default to engineering for systems at that scale?  In one architecture session, I was presented a system design question and I asked how many users they expected to perform the action in a given day.  10,000.  I think the interviewer thought that was some massive scale.  But if we were to even average that out over say 6 peak hours, that's only ~28/minute.  A small Postgres instance wouldn't even flinch!

- **Focus on simple problems and simple solutions**.  In a 60 minute call, focus on the basics and whether the candidate can solve simple problems with simple solutions rather than over-complicating and over-designing.
- **Focus on specific aspects of complex systems**.  Rather than "How would you build Twitter", narrowly focus on one specific technical aspect of interest.

### Over-emphasis on algorithms

I had one interview where the interviewer asked me to solve N-Queens as he watched over my shoulder and prefaced that some candidates could solve it in 15 minutes.  Sure, any candidate that has already seen the N-Queens algorithm and solution doing leetcode practices could regurgitate it in 10 minutes!

Is it important that a candidate knows specific algorithms?  This might be the case when working in specific spaces like computer graphics, for example.  Low-latency, high-frequency trading systems.  AI and machine learning.  But building SaaS?  Consumer and enterprise software?  Web apps?!?

Perhaps it's just me, but I have never in my career made an algorithmically complex design decision alone on any project I've worked on.  For anything of interest, it has always been a team discussion with peers to review and discuss possible algorithms.  In some cases, we might review and reference research papers for ideas.  We might search for how others have solved similar problems and understand how to adapt a pattern or algorithm for our specific case.

### Endless rounds

The BBC wrote about this a few years back in an article titled *"[The rise of never-ending job interviews](https://www.bbc.com/worklife/article/20210727-the-rise-of-never-ending-job-interviews)"*

It's not uncommon to have 4 hour blocks of interviews and sometimes even multiple sessions!  As if the engineering team doesn't have better things to work on?