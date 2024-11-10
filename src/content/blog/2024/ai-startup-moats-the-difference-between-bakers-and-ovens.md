---
title: "AI Startup Moats: The Difference Between Bakers and Ovens"
description: "Are AI startups \"just another wrapper for a 3rd party model\"? Does having an oven make you a baker?"
pubDate: "2024 Nov 09"
socialImage: "/public/img/ai-startups/just-a-wrapper.png"
slug: "2024/11/ai-startup-moats-the-difference-between-bakers-and-ovens"
tags: "ai"
---

----

## Summary

- Many folks who have not worked with LLMs tend to see most AI startups as simple wrappers around third party models and APIs.
- However, this is the wrong way to think about LLMs and AI.  A more accurate analogy is to think of LLMs and AI as an oven -- an appliance that transforms the input mixture of ingredients into some intermediate product.
- In other words, when evaluating any AI startup, focusing on the oven is the wrong approach.  Focus instead on how well the team understands the input ingredients (the data) and how adept they are at preparing that data long before it goes into the "oven" (the LLM).
- Teams that do not invest in deeply understanding, preparing, and structuring the input data sources to the LLM are simply baking supermarket box cake mix.

---

![Quote that says "I don't really find it exciting at all, I'm sorry.  It's just another wrapper for a 3rd party model"](/public/img/ai-startups/just-a-wrapper.png)
*A misguided response in a Hacker News thread launching the startup Codebuff*

In [a recent Hacker News thread](https://news.ycombinator.com/item?id=42078536) launching an AI code generation CLI tool [Codebuff](htt[s://codebuff.com]), I saw this comment at the bottom:

> I don't really find it exciting at all, I'm sorry.  It's just another wrapper for a 3rd party model.  -- Hacker News Poster

This is a comment that is quite common to come across in the current tech landscape of AI and LLMs where venture capital has started a gold rush in *AI-for-{X}* with countless startups in the wake of this boom.  Most early "AI startups" really were just thin wrappers for OpenAI, but -- believe it or not -- some AI startups really *do* have a moat.

Before continuing, it is important to differentiate startups that work *on* AI (and AI adjacent services) -- like OpenAI, Anthropic, HuggingFace, Mistral, et al -- and startups that work *with* AI that use LLMs and gen AI to transform and create content (be that text, data, images, or video).  Here, let us consider only startups that work *with* AI.

## Having an Oven Does Not a Baker Make

Folks who have not worked heavily with gen AI -- day-in, day-out -- severely underestimate how difficult it is to get consistently good results from any LLM at the moment.  The team at Ownit AI (where I work) first started working with OpenAI around May of 2023, when the APIs first became available for consumption.

What is clear through the many different iterations of services we've built around LLMs is *just how lacking they are*.  Let me count the ways:

- Without retrieval augmented generation (RAG), the LLM will hallucinate
- A consequence is that, until web search was integrated, the LLM had no way of knowing about knowledge that came after its training data
- Of course, it also won't have knowledge about information that it never had access to (e.g. private enterprise data)
- Basic RAG has its limits so teams need to continuously find clever ways to improve the RAG like using graph-based RAG
- The LLM's understanding of instructions for complex tasks is quite "robotic" in that it prefers following very specific instructions and any ambiguity risks undesired/useless output
- Even with extremely large context windows, LLMs tend to suffer in its comprehension of the context as the window gets larger

In almost all cases, the solution to getting the desired output from the LLM is to *fix the input* by making the prompt and context data more precise, more targeted, or transformed in a way that makes it more likely to get the desired output from the LLM.

In a sense, this is not unlike *baking*: the AI is an appliance -- the most fantastical and amazing appliance man has made, but yet still an appliance -- and the artistry and science all happens *before* the ingredients go into the oven and we hit "bake".

Indeed, there are many "AI startups" that quickly fizzled because *having an oven does not a baker make* and many startups were just content putting supermarket box mix cake batter into the oven and calling it a day.

## How AI Startups Build Their Moat

In my 20's, Alton Brown's *Good Eats* series was the first time I had given purchase to the concept that cooking is equal parts art and science.  The preparation and combination of ingredients followed by the application of energy (usually in the form of heat or mixing) fundamentally changes the chemistry of the ingredients themselves (I actually think it was specifically his episode on mayonnaise and how the different ingredients play their roles in creating an emulsion).

<iframe style="aspect-ratio: 16/9; width: 100%;" src="https://www.youtube.com/embed/L-rLiYl-b1E?si=xZFwdzDecLtj1UTF"" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Watching the recent Netflix cooking competition *Culinary Class Wars*, I was often reminded of this as I watched the competitors meticulously transform a set of ingredients into incredibly creative dishes.  Every detail from how "Triple Star" (a nickname of one of the competitors) finely sliced wonton wrappers to achieve the correct texture when fried to "Napoli Matfia" creating a Tiramisu that wowed the judges using only ingredients available in a convenience store.

Startups that want to succeed working with AI similarly must understand that the moat is actually in the data:

- How well does the team understand what data is needed to achieve the desired output
- How creative the team is in transforming and using that data given the limitations of LLMs
- How the team thinks about combining different pieces of data to feed context into the LLM
- How the team puts the finish touches on the output present the results to the consumer

It is not unlike the culinary arts: the best chefs and bakers deeply understand the input ingredients be it their flavor and texture profiles, how they should be shaped for different applications, how they have to be prepared, how one ingredient will complement another, how to combine it all, and how to present the finished product tastefully and creatively.  Hitting "bake" on the oven is simply one step that applies heat to combine and transform the ingredients; all of the artistry and science is actually in the preparation steps before and the presentation steps afterwards.

For most AI startups, the moat probably really comes down to how well the team can do ETL and how well the team understands the domain.  Does the team have some insight into datasets in a particular domain?  Have they come up with a novel or creative way to use that data?  Do they understand how to shape and prepare that data given the constraints of LLMs?  Can they combine the right pieces of data to achieve the desired output?

## Closing Thoughts

What's clear is that Codebuff is anything *but* just another "wrapper for a 3rd party model".  That third party model is simply an oven; everyone has an oven, but few could bake world-class pastries because at the end of the day, the oven is an appliance that transforms the input ingredients to the output product.

In the case of Codebuff, the art and science is how the team prepares, resolves, and structures the correct and relevant pieces of the codebase to feed into the LLM; it is not just a simple matter of shoving all of the codebase into the context.  For Ownit, it is an analogous challenge (thought a bit more abstract than a codebase) where we've invested an incredible amount of effort into acquiring, transforming, and mapping out product data from retailers -- classic ETL -- so that we can accurately resolve the correct products.

For both companies -- and many other AI startups that will have a moat -- a good portion of the effort and resultant software system is dedicated to designing and building the pipeline that performs the extract-transform-load and the smaller part of the effort is interfacing with the LLM.  If you want to build an AI startup moat, everything starts with the ingredients (the data) and how adept you are in your preparation of the data before hitting "bake".
