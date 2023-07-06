---
title: "Nuxt 3 with SSR on Google Cloud Firebase Functions (2023)"
description: "If you're seeking a no-cost, low-ops, low-friction solution for deploying SSR workloads then look no further!"
pubDate: "2023 Jun 13"
socialImage: "/public/img/nuxt3-firebase/nuxt+firebase.png"
slug: "2023/06/nuxt3-with-ssr-on-google-cloud-firebase-functions-2023"
tags: "vue,frontend"
---

----

If you’re a weekend hacker or seeking to stand up a no-cost, low-maintenance, low-ops solution that requires an SSR component, then Nuxt.js with Firebase is a great solution as the fantastic CLI tooling and emulator for Firebase combined with the ease of deployment and operations — while still offering tons of room to grow should a project take off— makes it the best choice for moving fast.

Likewise, [if you’ve read my past articles, I’m a big fan of Vue for moving fast](https://chrlschn.dev/blog/2022/12/react-vs-vue-vs-everything-in-2023/). Its more opinionated ecosystem (compared to standalone React) along with the fine-grained reactivity means that even if you don’t do it perfectly, you won’t be punished the same way as you would in React.

In this article, we’ll explore the configuration needed to get Nuxt 3 with SSR to run on Google Cloud Functions (via Firebase) and how to add standalone Functions that are separate from the SSR Function.

----

## Background

When I first started building [Turas.app](https://turas.app) as a tool for my wife and I to plan our spring break trip, it was perfectly suited for a single-page application (SPA).

As the use cases evolved and we added “stories” — a micro-blog published from the itinerary — I then found myself having to tack on SSR to support several use cases for SEO. At the time, I put together my Grug-brained SSR hack which only took some 20 lines of code and in less than 2 hours, I had a homegrown SSR solution deployed and working as a Google Cloud Function without having to refactor any code.

The desktop performance for a story page like our Taiwan trip is pretty damn good:

![](/public/img/nuxt3-firebase/nuxt3-firebase-lh-desktop.webp)
*Desktop Lighthouse scores*

Not only that, the page also has a full set of JSON-LD structured metadata:

![](/public/img/nuxt3-firebase/nuxt3-firebase-json-ld.webp)
*The non-critical issues are missing optional fields.*

As well as OpenGraph and Twitter meta tags.

But without true SSR, the mobile scores suffer:

![](/public/img/nuxt3-firebase/nuxt3-firebase-lh-mobile.webp)
*Mobile Lighthouse scores*

With that in mind, it feels like it’s time to investigate Nuxt to see if it is the right solution going forward for my SSR needs.

----

## Why Google Cloud Functions

For SSR, we’ll need a server-side runtime to process the requests and generate the server rendered content on a per-request basis. On Google Cloud, the two best options for projects like Turas — where we’re trying to keep the operating costs low — are either Cloud Functions or Cloud Run.

In my case, the ability to use the Cloud Functions emulator and the integrated deployment of Cloud Functions with the Firebase CLI means that until such time that I need the increased flexibility of Cloud Run, Cloud Functions is the best solution.

Another option to keep an eye on is [the Firebase Hosting Web Frameworks solution](https://firebase.google.com/docs/hosting/frameworks/frameworks-overview) which is currently in beta but effectively allows SSR solutions — like Nuxt and Next — to be deployed with even less friction. As of this writing, Nuxt support does appear to be merged into the Firebase CLI tooling via these two GitHub PRs:

* https://github.com/firebase/firebase-tools/pull/5551
* https://github.com/firebase/firebase-tools/pull/5490

However, there’s still no documentation for the moment so we’ll stick with the manual configuration option.

----

## The Configuration

Our objective is to get everything configured so that we have:

1. Static assets and files served from Firebase Hosting
2. Nuxt SSR routes served from Firebase Functions
3. Fully integrated with standalone Cloud Functions for our other backend needs.

If we can achieve this, we will have a very streamlined deployment pipeline that dramatically simplifies our ops workload while still providing all of the benefits of SSR and minimal cost.

To get this to work, start by following the standard setup procedure for both Nuxt 3 and Firebase.

Create the Firebase Functions in the default `/functions` directory.

In `nuxt.config.ts`, we’ll need to add in the `firebase` preset for Nitro:

```js
export default defineNuxtConfig({
  nitro: {
    preset: 'firebase'
  },
  // Other config here.
}
```

When we run `yarn build` on the Nuxt solution, this will generate the server routes in `./output/server`; this is the directory where we’ll want to serve our SSR routes.

In `/.output/server/chunks/nitro/firebase.mjs` you’ll find a function that’s been configured for Google Cloud Functions:

```js
// Additional lines omitted

const nitroApp = createNitroApp();
const useNitroApp = () => nitroApp;

//  This is the standard mechanism of exporting a Cloud Function.
const server = functions.https.onRequest(toNodeListener(nitroApp.h3App));

export {
  useRuntimeConfig as a,
  getRouteRules as g,
  server as s,
  useNitroApp as u
};
```

To serve this, we need the following changes to our `firebase.json`:

```js
{
  "functions": {
    "source": ".output/server",  // The output directory
    "codebase": "nuxt"
  },
  "hosting": {
    "public": ".output/public",
    "cleanUrls": true,
    "rewrites": [
      {
        "source": "**",
        "function": "server" // The function name from above
      }
    ]
  }
  // Other config here
}
```

If we only want to serve SSR and use the [Nuxt server API routes](https://nuxt.com/docs/guide/directory-structure/server#server-routes), this is all we need!

But to be able to write additional standalone Functions, we’ll need to make an additional configuration change.

Consider the following Function in `/functions/src/test/helloWorld.ts`:

```js
export const helloWorld = functions
  .runWith({
    maxInstances: 5,
  })
  .https.onCall(
    async (data: EchoRequest): Promise<EchoResponse> => {
      return {
        message: `${data.message} @ ${new Date().toISOString()}`,
      };
    });
```

We can hook this up in `/functions/src/index.ts` as we normally would:

```js
import { helloWorld } from "./test/helloWorld";

exports.helloWorld = helloWorld;
```

But to make this work, we’ll need to update our `firebase.json` configuration:

```js
{
  "functions": [ // Note we change this to an array
    {
      "source": ".output/server",
      "codebase": "nuxt"
    },
    // Add this entry for the standalone Functions
    {
      "source": "functions",
      "codebase": "default",
      "ignore": [
        "node_modules",
        ".git",
        "firebase-debug.log",
        "firebase-debug.*.log"
      ],
      "predeploy": [
        "npm --prefix \"$RESOURCE_DIR\" run lint",
        "npm --prefix \"$RESOURCE_DIR\" run build"
      ]
    }
  ],
  // Other configuration here...
}
```

And don’t forget to update `/functions/package.json` since we are using TypeScript:

```js
{
  // "main": "lib/index.js",
  "main": "lib/functions/src/index.js",
}
```

With this, we have a low-cost (free), low-ops, low-effort solution that still offers plenty of flexibility to build apps with fully-featured backends that lets us take full advantage of Cloud Functions.

The best part of Firebase is how well Google has integrated the tooling with the backend infrastructure to support really low friction ops. While it’s certain that at scale you will need more control over the backend, the good news is that Firebase offers an easy route to transitioning from Functions to Cloud Run as needed. In other words, start with Cloud Functions for speed and refactor to Cloud Run as your needs change!

Check out my other articles on Firebase and Cloud Run if your curiosity is piqued!

* [Google Firebase with .NET 6](https://chrlschn.dev/blog/2022/09/google-firebase-dotnet6)
* [Using Playwright and .NET 6 to Generate Web App Recordings in Docker](https://medium.com/itnext/using-playwright-and-net-6-to-generate-web-app-recordings-in-docker-99094b860db7)
* [Google Cloud Run: Low Cost, Low Complexity Apps in Minutes (YouTube)](https://www.youtube.com/watch?v=ysXVta8_JWs)
* [gcr-dn6-api (GitHub)](https://github.com/CharlieDigital/gcr-dn6-api)

And of course, check out [Turas.app](https://turas.app)!