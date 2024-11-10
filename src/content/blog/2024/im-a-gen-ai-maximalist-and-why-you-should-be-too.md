---
title: "I'm a Gen AI Maximalist and Why You Should Be, Too"
description: "Putting things into perspective: many think that we are in the trough of disillusionment; but really, gen AI is just getting started."
pubDate: "2024 Oct 17"
socialImage: "/public/img/ai-maximalist/gandalf.jpeg"
slug: "2024/10/im-a-gen-ai-maximalist-and-why-you-should-be-too"
tags: "ai"
---

----

## Summary

- Content production and distribution has always been constrained by "bandwidth".  For example, the early days of broadcast TV meant that with 3 channels, there could only be 72 hours of programming in a 24 hour day because of limited bandwidth.
- Advancements in technology (e.g. printing press, WWW) have always expanded that bandwidth and along the way, increased the ratio of producers to consumers
- Generative AI is the next engine for that expansion of bandwidth and it has the potential to push that ratio closer to 1
- This expansion has always followed a reliable pattern of `text → images → audio → video` and we see this same pattern with OpenAI and current LLMs
- We are still quite far from the endpoint and the hype and high valuations are justified; generative AI will create "infinite bandwidth" for content

---

## Are We in the Trough of Disillusionment?

Spend enough time on the Internet, and there will be no lack of doubters about the state of AI and specifically generative AI (gen AI).  Some bemoan the "AI slop" that has pervaded everything from Facebook feeds to [Google Image search results for terms like "baby peacock"](https://news.ycombinator.com/item?id=41767648).  [Some think that Nvidia's stock is overvalued](https://www.reddit.com/r/wallstreetbets/comments/1g57kry/nvidia_is_worth_117_of_the_us_gdp_now_at_the_peak/) and that there is a glut of GPUs -- who needs more?  Some are already in the trough of disillusionment with AI.

I beg to differ; *I think there's not nearly enough hype around gen AI and we're barely getting started*.

## Content and the Concept of "Bandwidth"

Some time in the mid 2000's, I was speaking with an older coworker about [Al Gore's TV channel, Current TV](https://en.wikipedia.org/wiki/Current_TV).  Gore's Current TV would air pieces produced by citizen journalists; a novel idea at the time.  My coworker shared with me an interesting perspective: the history of content, media, and information is one of "bandwidth" -- he called it -- and Gore's Current TV represented an expansion of that valuable bandwidth which was once reserved for the likes of Walter Cronkite, Edward R. Murrow, and David Brinkley.

When broadcast television first started, there were a handful of channels; the bandwidth was limited, he said.  Audiences watched more or less the same few shows on the tiny number of channels because there was only so much "bandwidth".  He meant this word in every sense: from the broadcast capacity to the production capacity. 3 channels and 24 hours a day meant, at most, 72 hours of television programming in any given day and there was only so much capacity for professional crews to create that content.

Then came cable which created more bandwidth and more content followed to fill that capacity.  That transition created enough bandwidth to deliver content for a variety of special interests whether that was cooking (Food Network), nature and documentaries (Discovery, History), DIY (HGTV), or even whole channels dedicated to kids shows (Nick, Cartoon Network).

Of course, the mid 2000's is also around the time that YouTube started.  YouTube represents an *exponential* explosion in the number of "channels"; it represents *seemingly* infinite "bandwidth".  There is a channel or a creator for nearly any niche interest one could think of and more content is generated and published every day at an astounding rate; everyone with a phone in their pocket can be a creator.

*Aside: it is natural, then, that the overall quality of that content decreases, but there is a broadening of the the space by enabling new producers and publishers.*

This same evolution has happened in nearly every content industry from publishing to music to games.  At each step of advancement, there is a common pattern: *the ratio of producers to consumers increases*.

Viewed from this perspective, gen AI is the *next* explosion and we are just barely off of the starting line.

## The Pattern of Advancement

Looking at this historically, there has been a repeatable pattern of advancement in terms of the creation and dissemination of content and information.  First is always text, then still images.  Once we had the ability to reproduce audio and video, then audio and moving images followed text and still images.

In every era of advancement of content and information, this exact progression has repeated because each step requires more capacity and bandwidth.  Think about the early Internet and how it progressed from text to images.  Eventually came MP3s and Napster.  And finally, we are in the era of YouTube, Zoom, and Netflix; we can start a video call with anyone in the world seemingly free of cost or choose from a catalog of thousands of shows and movies to watch any time we want for less than $20 per month.

It is important to acknowledge this because I think this gives context to where we are at this moment with gen AI.  OpenAI's ChatGPT first launched with text.  Then came [multi-modal models](https://openai.com/index/chatgpt-can-now-see-hear-and-speak/).  Most recently, [OpenAI released their realtime API](https://openai.com/index/introducing-the-realtime-api/) that supports bi-directional audio streaming.  *Could it be more obvious what the next leap will be?*

## Gen AI as "Infinite Bandwidth"

Today, YouTube content already feels infinite; but the ratio of producers to consumers on the platform is still asymmetric and there can only be as much content that is as tailored to a specific audience as there are producers of that content and those producers are constrained by real-life constraints like time and the cost of generating certain types of content such as 3D animated films, for example.

Gen AI promises something else: a channel of content for every consumer generated specifically for that consumer; *"infinite bandwidth"*.  Effectively, gen AI will accelerate the push of the ratio of producers to consumers ever closer to 1 where every consumer can produce content tailored specific to their niche, interests, preferences, and tastes.

At [Ownit](https://ownit.co) (where I work), this is precisely the route that we took.  In the early days of our efforts with AI (just a year ago 🤣), we were pre-generating content targeting long-tail SEO terms for our retail customers -- dozens to thousands of articles at a time.

After encountering [a Hacker News thread on benchmarking LLMs](https://news.ycombinator.com/item?id=40137441) in May, [I started to experiment with multi-stream concurrent content generation](https://chrlschn.dev/blog/2024/05/need-for-speed-llms-beyond-openai-w-dotnet-sse-channels-llama3-fireworks-ai/).  The dramatic increase in throughput (this was before GPT-4o) meant that we could deliver all new experiences which were previously unthinkable because of the poor UX caused by low throughput.  So instead of pre-generating a set of articles targeted to a set of questions, what if we could literally generate high quality answers to *every question* on the fly (video below)?

<video
  preload="none"
  controls
  autoplay="false"
  name="media"
  style="width: 100%"
  poster="https://storage.googleapis.com/media.chrlschn.dev/ownit-safeway-cover.webp"
  title="AI augmented search">
  <source
    src="https://storage.googleapis.com/media.chrlschn.dev/ownit-safeway-out-sm.mp4"
    type="video/mp4" />
</video>

Of course, for anyone following the industry, Google came to the same conclusion and [released AI Search Overviews around the same time](https://blog.google/products/search/generative-ai-google-search-may-2024/).  While Google has taken their punches for some questionable responses from their search overviews, I have no doubt that they are on the right path because it is effectively moving the producer-consumer ratio towards 1: for every question, a custom and tailored chunk of content generated specifically for the consumer to answer their question.

## Perspective for the Doubters

It is easy to be a doubter in this moment because much of the tooling to generate high quality results is still quite niche and complex while often being inaccessible to lay people without the hardware and know-how to pull everything together.  The more accessible parts of the ecosystem are producing the low quality content and, frankly, there are a lot of low-quality efforts to leverage gen AI.

It is easy to look at the low quality, obviously AI-generated content and come to the conclusion that gen AI is a dud; destined to aggravate the *[enshiffitication](https://en.wikipedia.org/wiki/Enshittification)* of the once pure web.

![Gandalf smiling with the text "You start seeing fewer AI generated images" followed by Gandalf stunned with the text "You start seeing fewer AI generated images"](/public/img/ai-maximalist/gandalf.jpeg)
*When we cross this threshold, you may not even notice the transition.*

But pay attention and there are some folks out there producing really jaw dropping content and we're seeing the state of the art advancing quite rapidly.  [10 years ago, this was the state of the art research in image generation from text](https://www.reddit.com/r/StableDiffusion/comments/y9zxj1/you_think_thats_insane_heres_what_an_aigenerated/):

![An AI generated image of a cow from 10 years ago](/public/img/ai-maximalist/ai-cow.webp)
*An AI generated image of a cow from 10 years ago.*

[And just two months ago](https://www.reddit.com/r/StableDiffusion/comments/1f0b45f/flux_is_a_gamechanger_for_character_wardrobe/) (click the video below):

<video
  preload="none"
  controls
  autoplay="false"
  name="media"
  style="width: 100%"
  poster="https://storage.googleapis.com/media.chrlschn.dev/cow.png"
  title="AI generated video">
  <source
    src="https://storage.googleapis.com/media.chrlschn.dev/flux-character.mp4"
    type="video/mp4" />
</video>

Companies like Meta are investing heavily in this space with research projects like [Movie Gen](https://ai.meta.com/research/movie-gen/).  It's obvious to see how this tooling could one day be accessible to anyone right in Instagram or Facebook.

Reddit's [/r/aivideo sub](https://www.reddit.com/r/aivideo/) is one community to keep an eye on.  Examples like this [AI generated fashion show](https://www.reddit.com/r/aivideo/comments/1g5rk0e/womens_fashion_show/) or [fictional depiction of how ancient Egypt was built](https://www.reddit.com/r/aivideo/comments/1g5196e/footage_of_the_giza_pyramid_complex_being_built/) give a peek into what's possible and how quickly, week-over-week, the capabilities are advancing.

<video
  preload="none"
  controls
  autoplay="false"
  name="media"
  style="width: 100%"
  poster="https://storage.googleapis.com/media.chrlschn.dev/ai-fashion-cover.png"
  title="AI generated fashion show from sketches">
  <source
    src="https://storage.googleapis.com/media.chrlschn.dev/ai-fashion.mp4"
    type="video/mp4" />
</video>

Adobe's Project Turntable is also quite eye opening as the implication is that the underlying model can extrapolate a 3D model from a set of 2D vectors:

<iframe style="aspect-ratio: 16/9; width: 100%;" src="https://www.youtube.com/embed/gfct0aH2COw?si=x21eIqVBVQORJHHh" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

[Teams are also experimenting with playable AI generated games](https://www.reddit.com/r/singularity/comments/1g2ogq0/counterstrike_runs_purely_within_a_neural_network/) (also video):

<video
  preload="none"
  controls
  autoplay="false"
  name="media"
  style="width: 100%"
  poster="https://storage.googleapis.com/media.chrlschn.dev/cs-go.png"
  title="AI generated video">
  <source
    src="https://storage.googleapis.com/media.chrlschn.dev/ai-cs-go.mp4"
    type="video/mp4" />
</video>

The implications for industries from special effects to film making to game design are immense.  They are yet to be disrupted by gen AI, but there is no doubt that as the capabilities expand, accessibility increases, and prices decrease, many industries will have to adjust.

## What's at Stake?

To understand just how high the stakes are at the moment, the three major cloud providers -- Amazon, Google, and Microsoft -- have all recently announced investments in nuclear energy to power a massive expansion of compute capacity to fulfill this endpoint of "infinite bandwidth".

Here are some headlines from just the last few weeks:

- [Amazon buys stake in nuclear energy developer in push to power data centres](https://www.ft.com/content/00776191-b010-4104-add4-8dc430386911)
- [Amazon and Google have plans for fueling their data centers: Nuclear power](https://www.cbsnews.com/news/amazon-nuclear-reactor-investment-google-kairos-power/)
- [Three Mile Island nuclear plant will reopen to power Microsoft data centers](https://www.npr.org/2024/09/20/nx-s1-5120581/three-mile-island-nuclear-power-plant-microsoft-ai)

This is a signal that the gen AI era is still early in its infancy. The build-out of this capacity and continued advancements in the algorithms, tooling, and ecosystem will push us closer to that 1:1 ratio of producers to consumers (one might still consume content or a model produced by a creator, but producing a personalized version of it will be a prompt away).

Meta invested billions in creating the next content platform -- virtual reality -- only to have the Metaverse largely fizzle.  But what if a Star Trek inspired holodeck-lite experience isn't far in the future?  What if the Meta Quest could deliver that virtual experience, tailored to each consumer's specific interests and tastes?  What if it could render any world and any experience from just a few lines of text?

The stakes are incredibly high because just as Google and Meta became trillion dollar advertising companies with the transition of content consumption from broadcast and cable to the Internet -- if the future of content is AI generated -- the platform that can deliver this future of content to the consumer will enrich themselves with trillions more.

---

## Closing Thoughts

While I do not yet have concrete thoughts on artificial general intelligence (AGI) and "singularity", my framework for thinking about generative AI is drawn from this concept of "bandwidth" that my coworker shared with me almost two decades ago.

The pattern is obvious: at each stage of bandwidth expansion, the ratio of producers to consumers increases and generative AI seems to be the engine that will push this ratio ever closer to 1.  The expansion always moves from text to images to audio to video to interactive.  To power this expansion, expect to see continued investments by the likes of Nvidia, Microsoft, Meta, Google, and Apple into generative AI.

It is easy to look at the middling state of generated content today and dismiss "AI" as a dud; an over-hyped promise that has under-delivered.  But history has shown that technology that expands bandwidth for content and decreases the friction for production and distribution -- from the printing press to the World Wide Web -- will ultimately prevail and deliver riches.

---

Can AI startups actually create a moat?  Check out [AI Startup Moats: The Difference Between Bakers and Ovens](https://chrlschn.dev/blog/2024/11/ai-startup-moats-the-difference-between-bakers-and-ovens/)
