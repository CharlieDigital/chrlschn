---
title: "Thoughts on React vs Vue vs Everything Else in 2023"
description: "My personal thoughts on front-end in 2023"
pubDate: "2022 Dec 03"
socialImage: "/public/img/react-vue-2023/react-vue-2023-2.webp"
slug: "2022/12/react-vs-vue-vs-everything-in-2023"
tags: "vue,react,frontend"
---

----

A thread on Reddit [Beginner here, start with react, svelte, or solid?](https://www.reddit.com/r/webdev/comments/zbedcd/beginner_here_start_with_react_svelte_or_solid/) caught my eye and gave me a chance to hash out some of my own thoughts on these platforms and where beginners should start in 2023.

While I’d hardly call myself a front-end specialist, I’ve worked full stack at a number of VC backed startups building with React, Next.js, and Vue. I also started building vanilla JS apps in the late 90’s so I’ve spent quite a bit of time with front-end frameworks so I hope I have something useful to offer here!

This writeup is broken out into a few parts, so don’t jump ship!

----

## The Professional Take

Or alternatively “how do I get paid?”

[The State of JS 2021 survey shows a pretty clear picture of adoption and usage by framework](https://2021.stateofjs.com/en-US/libraries/front-end-frameworks):

![](/public/img/react-vue-2023/react-vue-2023-1.webp)
*A fairly stiff dropoff from the 50% level of Angular and Vue to the 20% of Svelte.*

There’s not much change to the 2022 version:

![](/public/img/react-vue-2023/react-vue-2023-2.webp)
*Usage of React actually ticking up; likely related to Next.js growth, IMO.*

There is no ambiguity that if you’re trying to decide which of these front-end platforms to invest your time in from a professional perspective, ***you should learn React***.

Not only React, but specifically ***Next.js flavored React***.

Why? Next.js is having a moment and likely to be the platform of choice for the foreseeable near- and mid-term future in both the enterprise and startup space because React on its own is too un-opinionated and therefore, somewhat open to being misused, mis-configured, and poorly architected since there are many choices involved in any non-trivial implementation.

Next.js solves some of this complexity (in addition to providing SSR and SSG solving for the SEO problem) by providing an opinionated approach to React.

It has been supremely successful. Target.com and Walmart.com, for example, are both Next.js applications.

![](/public/img/react-vue-2023/react-vue-2023-3.webp)
*Target’s dotcom with Next.js*

![](/public/img/react-vue-2023/react-vue-2023-4.webp)
*Walmart’s dotcom with Next.js*

If you scan YCombinator’s [workatastartup.com](https://www.workatastartup.com/), you’ll also find a strong bias for React. Of the **1103** YC companies posting jobs:

![](/public/img/react-vue-2023/react-vue-2023-5.webp)
*1103 total listings*

**690** of them (63%) are seeking React developers:

![](/public/img/react-vue-2023/react-vue-2023-6.webp)
*690 or 63% matching React*

**91** of them ( 8%) are seeking Vue developers, **73** have Angular (6.6%), and just a meager **12** postings (1%) for Svelte.

![](/public/img/react-vue-2023/react-vue-2023-7.webp)
*A good proxy of the broader startup space.*

You’ll find a preponderance and growing presence of the following stack:

* **React + Next.js** front-end with **Tailwind**
* **Next.js** or **Express** derivative back-end
* **Prisma** (+ **Apollo** if using GraphQL)
* **Postgres** (flavored), **Mongo**, **Firestore**, or **Dynamo** database
* Python based backends if the company is doing any ML

That’s the ticket for any front-end dev wondering where to focus one’s energy purely from a professional perspective.

In fact, this stack is so pervasive in the startup world, that VC’s might even give the side eye when picking a deviation as it is seen as a risk for scaling the team quickly or dealing with resource churn.

----

## The Terrible State of Web Front-end

Front-end frameworks and platforms have come such a long way from the early days of handcrafted JS, Prototype.js, jQuery.js (*legends never die* 🤣), Knockout.js, Backbone.js and many, many more that have seen their peak days come and go.

And yet, I really, *really* hate the state of front-end!

As a developer who has seen it all, the level of complexity that is applied to building even simple interactions today seems somehow very misguided. Things that should be as simple as toggling the class name on a DOM element have somehow become twistedly complex requiring whole toolchains to be set up!

Not only that, modern applications have become resource hogs.

For all of the benefits of using Next.js, it outputs some truly awful markup. That `__NEXT_DATA__` script? It contains the full server-side dataset used to render the view on the server because the front-end React components may need the data as well (it’s not smart enough to know which data can be omitted).

Here’s the Walmart data in the `__NEXT_DATA__` script:

![](/public/img/react-vue-2023/react-vue-2023-8.gif)
*Looking carefully at the HTML in the network traffic, much of this has already been rendered on the server side! Kind of defeats the performance benefits of SSR and SSG.*

Target’s output is more compact, but it relies on a *separate* call to get the results in most cases. This seems to defeat the purpose of SSR! Take a look at Next.js markup for image output and you might be equally put off by the messy markup.

While these frameworks have undoubtedly made some aspects of development easier in the 80% case, in the remaining 20% case they can make development *significantly more complex* because it then becomes about learning the internals of the framework and often figuring out how to get it to behave.

Then of course there’s the mess of `node_modules` consuming hundreds of MB for each project…

----

## My Personal Take and Advice

First is that I’d strongly recommend Essential TypeScript 4 by Adam Freeman. It’s one of the most well written, structured, and formatted technical books I’ve ever read. Whether you plan on doing React, Vue, Angular, Svelte, or whatever the flavor of the year is, your project will end up converging to TypeScript at some point.

For professional use, there’s really no choice but to learn React and Next.js. For personal development and side projects, my own choice is Vue because it is simply harder to do poorly. I’ve been solo building [Turas.app](https://tuas.app) and the productivity with Vue is off the charts because I don’t have to worry about debugging/managing the render cycle.

In particular, there’s a point of nuance with respect to how React and Vue handle the render cycle. [Arek Nawo has the best write up on this I’ve read, but the simple gist of it is that with React, the developer needs to opt-out of redraws. With Vue and many other frameworks, the developer opts-in to redraws](https://areknawo.com/vue-composition-api-vs-react-hooks-the-core-difference/).

![](/public/img/vue-3x3/vue3x3-13.webp)
*See this innocuous code?*

![](/public/img/vue-3x3/vue3x3-14.gif)
*What actually happens with the React render cycle.*

Because of this, developers need to be more vigilant with React code as it is more susceptible to performance issues and weird side effects due to over-rendering since developers need to consciously opt-out (e.g. `useMemo`). This tends to lead to an overuse of `useMemo` and `useCallback` in an attempt to brute force your way out of this conundrum.

The thing is, React is deceptively simple early on. In the early phases of dev, it never pops up because the app is small; the components are small; the datasets are small; even if there is a performance penalty on over-rendering, it does not register because the app is so small. Even if the app over rendered 10x, who notices or cares when the app is small?

But as the project grows, it is the endless source of performance issues and weird side effects. ([If you want to understand the true nature of React, check out this write-up](https://itnext.io/using-usememo-and-usecallback-to-save-history-from-react-langoliers-8eb7bb72c87))

Nadia Makarevich has two ***really good***, definitive articles on managing this complexity that every React developer should read:

1. [How to `useMemo` and `useCallback`](https://adevnadia.medium.com/how-to-usememo-and-usecallback-you-can-remove-most-of-them-b8ef01b2020d)
2. [React Rerenders Guide](https://adevnadia.medium.com/react-re-renders-guide-preventing-unnecessary-re-renders-8a3d2acbdba3)

Vue’s approach of opt-in to renders (via `ref`, `computed`, and `watch`) means that it’s not very likely to foot-gun yourself with the same types of problems.

React also has [at least a handful of major state management models](https://leerob.io/blog/react-state-management) (Context, Redux, Recoil/Jotai (atom model), Zustand, Valtio/Mobx (proxy model), etc.) so there are too many different ways to think about what should be a simple “I just need a working state model” problem. Teams really need to know the problem they will be solving for 3–6 months down the line when the team ultimately picks one. If the app does not start with a state management model, the app is going to end up needing one at some point once prop drilling becomes an unbearable strain (which it will as soon as the app is doing something non-trivial).

I also think in general, there are some really bad practices in the React community because of the way React is architected and these bad practices have propagated to the broader JavaScript community.

One is the demonization of OOP in JavaScript when React changed to the hooks model. A cursory look at some well-known JavaScript projects is enough to see that big, big swaths of the codebases of these major projects use classes and OOP paradigms *because it allows better code organization than loose functions in module exports*.

For reference:

* [MongoDB Driver](https://github.com/mongodb/node-mongodb-native/blob/main/src/collection.ts)
* [Prisma ORM](https://github.com/prisma/prisma/blob/main/packages/client/src/runtime/DataLoader.ts)
* [Nest.js](https://github.com/nestjs/nest/blob/master/packages/core/nest-application.ts)
* [Storybook](https://github.com/storybookjs/storybook/blob/next/code/lib/channels/src/index.ts)
* [Apollo Client](https://github.com/apollographql/apollo-client/blob/main/src/core/QueryManager.ts#L82)

The other is eschewing comments because of how funky comments are in JSX. The majority of the React code I’ve dealt with, the JSX is almost always lacking in comments which makes it hard to maintain at times because the devs also slapped a ton of logic into the JSX!

I personally prefer Vue as it is less of a foot-gun due to the opt-in nature of the redraws. The ecosystem is more unified. The main downside of Vue is that the tooling isn’t quite as good; just a tad below React, IMO. For example, devs have to know about this [takeover mode](https://vuejs.org/guide/typescript/overview.html#volar-takeover-mode) with Volar (Vue language server). Some of the editor behavior in the Vue template isn’t quite as nice as JSX.

I think that Vue is perhaps a bit more complex than React very early on. But unlike React, Vue’s performance, complexity, and difficultly scales much more constantly and linearly as the project scales. In other words, *a React project becomes much harder to do well and maintain as the project and team scales (due to it’s unopinionated nature) whereas a Vue project’s complexity scales with a lower slope* (just my experience doing both). Both can be done poorly, but Vue’s rails make it harder to create certain classes of bugs.

That aside, Vue is also a bit more proven than Svelte with more libraries available so I’d pick it over Svelte purely for practical reasons. [Wiki Foundation](https://github.com/wikimedia/wvui), [Gitlab](https://www.vuemastery.com/conferences/vueconf-us-2018/how-we-do-vue-at-gitLab-jacob-schatz/), NASA, BMW, Nintendo, Apple all have some properties (not necessarily their main dotcom) using Vue.

----

## The Future of Frontends?

I don’t think React and Next.js are going to be dethroned any time soon, but I do sense that there is a growing wave against the complexity brought forth by the current champs. Because of the benefits to speed and SEO, many consumer facing web apps are going to switch to a SSG model and thereby creating faster, lighter front-ends that probably don’t need the complexity of React and Next.js; after all, there are numerous ways of templating HTML.

Projects like [Arrow.js](https://www.arrow-js.com/) and [HTMX](https://htmx.org/) represent a movement away from the increasingly complex nature of front-end frameworks and reconsider what teams really need from front-end libraries and frameworks. While projects like Astro.js give me hope that we can do better than the mess of Next.js when it comes to SSR and SSG. Other projects like [Nanostores](https://github.com/nanostores/nanostores) provide super lightweight state management without the complexity of heavy frameworks like Redux.

These days, I’m more and more inclined to just write vanilla 🤣. The reality is that JavaScript has come a long way as has CSS. The death of IE also means that we’re in a much better place as far as browser capability goes.

If you’re diving into front-end development in 2023? Perhaps invest in React and Next.js because that’s where the jobs are — for now — but don’t lose focus on mastery of core JavaScript and TypeScript as we await the next swing of the pendulum.