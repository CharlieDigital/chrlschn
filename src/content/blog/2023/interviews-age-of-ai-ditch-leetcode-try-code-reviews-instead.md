---
title: "Interviews in the Age of AI: Ditch Leetcode - Try Code Reviews Instead"
description: "In the emerging age of AI generated code, is leetcode really the best way to evaluate technical candidates (was it ever)?"
pubDate: "2023 Jul 31"
socialImage: "/public/img/code-review-as-interview/coderev-3.png"
slug: "2023/07/interviews-age-of-ai-ditch-leetcode-try-code-reviews-instead"
tags: "musings,software engineering"
---

----

## Intro

A Medium story first popped in to my feed last year and it got me thinking about the state of how the tech industry interviews and evaluates candidates.

![Medium article focused on why many senior devs fail leetcode interviews](/public/img/code-review-as-interview/coderev-1.png)
*https://medium.com/the-coding-diaries/why-experienced-programmers-fail-coding-interviews-b22210ba343*

Recently, a friend shared with  me his plans to take some time off to study for common leetcode problems to practice as he was planning his applications for FAANG companies.

Is that really what one wants to select for when hiring for a new member of an engineering team - the person that had the most free time to invest in studying leetcode challenges?

Having been on both sides of the coin, I have always had a dislike for leetcode exercises as an evaluation mechanism for technical candidates for a number of reasons:

1. As much as solving algorithmically complex problems is fun and rewarding, the reality is that for most productive developers, most of our coding time isn't really focused on this type of work.
2. Most of us regularly reference StackOverflow, documentation, and other online resources when solving challenging coding problems day-to-day. We'll chat with peers and whiteboard different ideas. With the explosion of AI coding tools, many devs are already including Copilot and ChatGPT into their workflows.
3. Many types/classes of complex problems, we may only code once or twice in a blue moon. For example, I have a complex Firestore abstraction layer that I wrote once, that I just keep reusing. The point of writing complex, high-abstraction code is so that you only write this type of code once.
4. It creates an artificial situation that induces stress. Few of us code while others are watching over our shoulders. And fewer of us are coding against a fixed time constraint in real life. For hard problems, I may go for a walk, talk to a co-worker, research algorithms, build small toy codebases first, etc. Quite honestly, most of us probably create our best code in isolation when we're in a flow state; the exact opposite of a high-stakes interview.
5. Developers code best in their own environments with their own tools. Using an unfamiliar tool can be disorienting and again, add to stress and anxiety as someone watches.

All of this means that *using this approach could end up measuring for all the wrong metrics or measuring for metrics that have very little impact on how well a candidate can productively fit into a team*. 

Not only that, as teams become more reliant on generated code to increase productivity - whether Copilot or GPT-I think that the ability to quickly read that code and identify subtle defects in the context of the larger application or domain space becomes more valuable.

![GPT generating a solution to N-Queens](/public/img/code-review-as-interview/coderev-2.gif)
*GPT generating the solution to a common leetcode challenge N-Queens*

A while back, I had the opportunity to participate in an interview which was structured around *reviewing code* rather than writing code. It was a revelation. Rather than focus on coding exercises, using code reviews as interview can be a better way to evaluate software engineering candidates.

----

## 8 Reasons to consider code review as interview
There are a few reasons why I think code reviews make for an inherently better technical interview experience:

1. In the age of AI, an emerging reason is that using AI generated code is a source of risk in terms of performance, security, and internal best practices (especially important in regulated industries).  Being able to effectively evaluate code in the context of the larger codebase becomes an even more important skill when relying on piecemeal generated code.
2. It is a better reflection of the day-to-day activities that engineers - especially more senior roles - engage in. Providing effective guidance and quality feedback to peers and especially junior team members can improve overall productivity and output quality in the long run.
3. It provides a more holistic view of how a candidate reasons, thinks, and communicates; in other words, it provides a better picture of the candidate as a teammate on the whole. This includes insights into the candidate's depth of experience.
4. Code reviews are inherently more collaborative whereas writing code tends to be a more isolated activity (personally, I tend to code best in complete silence in evening hours). Code reviews are perhaps a better indicator of how it feels to work together with a candidate rather than having the candidate solve a technical puzzle.
5. There is more subjectivity to a code review; it's not black or white. It naturally provides more opportunities for discussion and interpretation. It also provides more opportunities to arrive at outliers since there's no single "solution". For 5 candidates, you'll get 5 unique variations whereas common algorithmic challenges may have a small number of optimal responses.
6. It is hard to "cheat" using generative AI or studying for leetcode problems. 
7. It is better suited for evaluating technical roles that have a bias towards reading code versus writing code. This includes engineering managers, architects, and support staff.
8. It better reflects how a candidate is going to be spending their first few days or weeks anyways: learning the existing system by reading through the code. Doesn't it make sense to measure how well they can do this rather than how quickly they can solve N-Queens?

---

## Strategies
If you're convinced, the next step is to think about different strategies to use when setting up your code review. There are a few strategies that you can mix and match to measure the range of a candidate.

A common thread to each of the strategies is that rather than inventing - or worse finding - a time-boxed puzzle to solve, an approach focused on code reviews lends itself to using bits and pieces of an existing codebase and actual problems that a team has been working on.

### "Au naturel"
Take actual, relevant and interesting parts of an active codebase as-is and use those as the context for review. Data access, exception handling, input handling - these all make for great points of focus to see a candidate's feedback on an existing codebase and how quickly they can read and understand your existing code.

### Bug hunter
Intentionally introduce some logical flaw or defect and see if a candidate can spot it. A good idea is to go back and find recent bugs that were solved and pull the source before the fix was applied. Can the candidate identify the root cause? How would the candidate suggest resolving the defect? How does that response differ from the one that was implemented?

### Refactor and redesign
Recently completed a refactor or planning a refactor? Use the code prior to the refactor as the context and see how the candidate thinks about the code before the refactor and what strategies a candidate would use to plan and execute the refactor. See if the candidate can identify why a refactor would be desired and evaluate the sophistication of their approach; you might be surprised and find an entirely novel alternate approach!

This is particularly useful when a candidate is joining a brownfield project.

### Performance oriented
Find code that was recently fixed for a performance issue and see if the candidate can spot why a piece of code might be slow. See if the candidate can propose an algorithm, alternate design, or fix to improve the performance of the code. 

Include existing SQL DDL schema and common natural language queries that the application will perform.  Remove the index definitions and see if the candidate can propose indices or alternate designs to improve performance.

Instead of asking about the principles of Big-O notation, see if the candidate can actually spot some `O(n^2)` code or `N+1` issues in data access code!

### Test focused
Share a fragment of code and a set of unit tests for the code. Are all the cases covered? Are there cases not covered? How could the unit tests be improved? This perspective may be more important in the coming age of AI generated code: understanding the domain space and use case and how to write high coverage unit tests - or evaluating the completeness of generated unit tests - becomes a key skill.

### Security hawk
Use code that has subtle security flaws and see if the candidate can identify said flaws. Rather than merely asking what an XSS or SQL injection attack is, see if the candidate can identify such flaws in code by using code that lacks protection against said attacks. Again, as teams come to rely on AI-generated code, having the experience to identify potential security flaws in the generated code becomes more important.

### Best practices
For more senior positions, focusing on best practices is a great way to find candidates that can identify, communicate, and teach best practices to more junior candidates and direct reports.

---

## Try CodeRev.app
If you're sold on the idea like me, check out my new project [CodeRev.app](https://coderev.app):

![CodeRev screenshot](/public/img/code-review-as-interview/coderev-3.png)
*CodeRev.app candidate workspace.*

CodeRev.app is a (free) lightweight tool that's designed to facilitate code review as interview. 

Why use it instead of CodeSandbox, Stackblitz, or GitHub?

- CodeRev is tightly focused on reading and commenting rather than coding whereas GitHub only has comments on PRs
- CodeRev works great with fragments of code and you can mix and match different files without trying to make the project as a whole actually run. Just throw in whatever files make for interesting discussion!
- CodeRev eliminates boilerplate that is inherent in modern projects one might be tempted to load into CodeSandbox or Stackblitz. Just pick the interesting source files and put them into CodeRev.
- CodeRev lets you quickly toggle between the responses from different candidates so you can compare and see the insights and feedback they provided.

It's still early stage so let me know if you run across bugs or you have ideas!

---

## Closing thoughts

In *The End of Average*, Todd Rose writes:

<blockquote>
Just about any meaningful human characteristic - especially talent - consists of multiple dimensions. The problem is that when trying to measure for talent, we frequently resort to the average, reducing our jagged talent to a single dimension like a score on a standardized test or grades or a job performance ranking.
</blockquote>

Indeed, if we select for a narrow set of problem solving skills, then we end up selecting against an "average" of candidates who have simply studied and prepared to achieve against those dimensions and miss out on many candidates whose *jagged talents* contribute to the character and productivity of a team.

Incorporating code reviews as interview as part of a team's repertoire can help better measure a wider range of a candidate's skill than leetcode alone.

As our industry shifts and the profession comes to rely more on AI tools like Copilot and GPT to generate code for productivity gains, there is a greater importance on finding ways to identify jagged talent; robotically solving algorithmic puzzles becomes the lesser of the qualities we want in a candidate. Instead, perhaps the key skill is the ability to read and review that code for correctness, best practices, and security.

Replacing leetcode with code reviews as interview can help teams not only better measure candidates on the whole, but also prepare for this shift in how we approach building software by focusing on more relevant skills and those aspects of a candidate that make for a better *human* teammate.

*Thanks for [Lefan Tan](https://lefantan.com/), Arash Rohani, and Rob Alarcon for reviewing draft versions of this article and providing their feedback.*

---

If you liked this article, also check out ***[Your Interview Process Is Too Damn Long (and How To Fix It)](https://chrlschn.dev/blog/2023/10/your-interview-process-is-too-damn-long/)***

![Too damn long](/public/img/interview/too-damn-long.jpg)