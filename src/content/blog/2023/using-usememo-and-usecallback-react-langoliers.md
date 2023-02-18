---
title: "Using useMemo and useCallback to Save the Past from React Langoliers"
description: "If you're confused about useMemo and useCallback and you have 10 minutes and nostalgia for 90’s sci-fi? You came to the right place!"
pubDate: "2023 Feb 03"
socialImage: "/public/img/react-memo/react-memo-app-1.gif"
slug: "2023/02/using-usememo-and-usecallback-react-langoliers"
tags: "react,frontend"
---

----

If you’re looking for a deep dive into `useMemo` and `useCallback`, then you should check out Nadia [Makarevich’s excellent article](https://adevnadia.medium.com/react-re-renders-guide-preventing-unnecessary-re-renders-8a3d2acbdba3) which goes into great depths and details on how to make sure you avoid excess re-renders.

If you have 10 minutes and a short attention span and nostalgia for 90’s sci-fi? You came to the right place! 🤣

I’m inspired to write this article after recently troubleshooting an issue in my team’s codebase with multiple redraws and APIs being called repeatedly. These are almost always sure signs that you’ve got some excess renders to clean up.

----

## The Langoliers

[If you’ve never seen this adaptation of the Stephen King novel](https://en.wikipedia.org/wiki/The_Langoliers_(miniseries)), a plane ends up in a time gap in the past and [the passengers encounter the Langoliers who are responsible for “cleaning up” past space-time by devouring it](https://www.youtube.com/clip/Ugkx6Le03R6Bl3soS7qG3iI0S3xtA1vAUFRG).

<iframe width="560" height="315" src="https://www.youtube.com/embed/x_2LpM3gNj8?clip=Ugkx6Le03R6Bl3soS7qG3iI0S3xtA1vAUFRG&amp;clipt=EPTFAxjHgwQ" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen style="margin: auto auto;"></iframe>

[Consider the following super basic React app](https://jsfiddle.net/0zsh85rv/):

```js
const { useState } = React;

function App (props) {
  console.log('Render App');

  const [message, setMessage] = useState('');

  return (
    <div>
      <input type="text" onChange={(value) => setMessage(value)}/>
    </div>
  );
}

ReactDOM.render(
  <App />,
  document.getElementById('container')
);
```

See that `console.log` on line 4? Let’s see what happens when we type some text into our textbox:

![React App](/public/img/react-memo/react-memo-app.gif)
*Every change in state causes the component — and all of its children — to re-evaluate. If that’s not what you expected, then *buckle up* my friend: your whole understanding of React is about to change.*

The Langoliers have wiped the slate clean with each change of state and our function `App` gets invoked on every key stroke! This is the fundamental nature of React that one must understand to grasp why we need `useMemo` and `useCallback`.

*(Note: React doesn’t actually replace the HTML DOM; internally it’ll diff to see if the returned HTML DOM needs to be spliced into the document, but each update is a new evaluation of the App function; we’ll talk about this at the end)*

----

## Escaping the Langoliers

In React land, `useMemo` and `useCallback` are the two basic tools to escape the Langoliers. Let’s examine this in a few easy examples.

### Part 1
Let’s start from a slightly more detailed example:

```jsx
const { useState } = React;

function Person(props) {
  console.log('Render Person');
  return (<span>{ props.identity.firstName } { props.identity.lastName }</span>);
}

function Logger(props) {
  console.log('Render Log')
  return (<button onClick={props.onClick}>Log</button>);
}

function App (props) {
  console.log('Render Hello');
  const [count, setCount] = useState(0);
  const einstein = { firstName: "Albert", lastName: "Einstein" };
  const logConsole = () => console.log("HELLO, WORLD");
  return (
    <div>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <Person identity={einstein} />
      <Logger onClick={logConsole} />
    </div>
  );
}

ReactDOM.render(
  <App />,
  document.getElementById('container')
);
```

When we run this and click “Increment”:

![App state update renders everything](/public/img/react-memo/react-memo-app-1.gif)
*The update of the root parent state causes each of our child component functions to re-run on each click; the entire component tree is effectively re-evaluated because of one update at the root, even though the child components haven’t changed state. This is the default behavior of React.*

We can see that each of the components: `App`, `Person`, and `Logger` re-evaluates because the parent `App` re-evaluates on the change of state to count.

If we memoize `Person`, this will surely prevent it from being re-evaluated, right?

```jsx
const { useState, useMemo } = React;

function Person(props) {
  console.log('Render Person');
  return (<span>{ props.identity.firstName } { props.identity.lastName }</span>);
}

const MemoPerson = React.memo(Person) // <------- Memoize this

function Logger(props) {
  console.log('Render Log')
  return (<button onClick={props.onClick}>Log</button>);
}

function App (props) {
  console.log('Render Hello');
  const [count, setCount] = useState(0);
  const einstein = { firstName: "Albert", lastName: "Einstein" };
  const logConsole = () => console.log("HELLO, WORLD");

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <MemoPerson identity={einstein} /> {/* <------ */}
      <Logger onClick={logConsole} />
    </div>
  );
}

ReactDOM.render(
  <App />,
  document.getElementById('container')
);
```

Let’s see:

![Memoizing the component alone isn't enough](/public/img/react-memo/react-memo-app-2.gif)
*Same as before!*

Nope! The component is still devoured by the React Langoliers and we end up with a fresh evaluation! Why didn’t the memoization work? Because the memoized component thinks that there’s a legitimate reason to re-evaluate: a prop changed. But did it? Everything looks the same.

### Part 2 — useMemo

The real problem is actually on line 18:

```js
const einstein = { firstName: "Albert", lastName: "Einstein" };
```

*The Langoliers have consumed the original instance*. Even though we haven’t changed the values, the redraw of `App` creates a new instance of `einstein'` because it belongs to App and from the perspective of the `MemoPerson` component, this appears to be a legitimate reason to re-evaluate because the reference to the instance held by the prop has changed! Recreating App recreated `einstein'` and `MemoPerson` is holding on to a defunct reference.

To fix this, we need to memoize the instance (or otherwise pull it out of the path of the React Langoliers — we’ll see an alternative in the end) and basically pull it into the future:

```jsx
const { useState, useMemo } = React;

function Person(props) {
  console.log('Render Person');
  return (<span>{ props.identity.firstName } { props.identity.lastName }</span>);
}

const MemoPerson = React.memo(Person) // <------- Memoize this

function Logger(props) {
  console.log('Render Log')
  return (<button onClick={props.onClick}>Log</button>);
}

function App (props) {
  console.log('Render Hello');
  const [count, setCount] = useState(0);
  const einstein = useMemo(() => ({ firstName: "Albert", lastName: "Einstein" }), []);
  const logConsole = () => console.log("HELLO, WORLD");

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <MemoPerson identity={einstein} /> {/* <------ */}
      <Logger onClick={logConsole} />
    </div>
  );
}

ReactDOM.render(
  <App />,
  document.getElementById('container')
);
```

Line 18 is where we add our memo and now:

![Now our person is memoized](/public/img/react-memo/react-memo-app-3.gif)
*We only get the output for App and Logger.*

Only `App` and `Logger` are being re-created; we’ve saved einstein and Person from the React Langoliers.

### Part 3

But we can see that the Langoliers have devoured our `Logger`. Surely — since we don’t have any state bound to it — we can just memoize it, right? Check out line 15 and 27:

```jsx
const { useState, useMemo } = React;

function Person(props) {
  console.log('Render Person');
  return (<span>{ props.identity.firstName } { props.identity.lastName }</span>);
}

const MemoPerson = React.memo(Person)

function Logger(props) {
  console.log('Render Log')
  return (<button onClick={props.onClick}>Log</button>);
}

const MemoLogger = React.memo(Logger) // <------- Memoize this

function App (props) {
  console.log('Render Hello');
  const [count, setCount] = useState(0);
  const einstein = useMemo(() => ({ firstName: "Albert", lastName: "Einstein" }), []);
  const logConsole = () => console.log("HELLO, WORLD");

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <MemoPerson identity={einstein} />
      <MemoLogger onClick={logConsole} /> {/* <------ */}
    </div>
  );
}

ReactDOM.render(
  <App />,
  document.getElementById('container')
);
```

Well, not quite…

![Our logger is still devoured](/public/img/react-memo/react-memo-app-4.gif)
*Memoizing the control is not enough.*

The Langoliers are at it again. Since `MemoLogger` takes the `logConsole` function as a prop and because the Langoliers consumed the past version of the function, the MemoLogger's reference to the prop is holding on to the devoured function. On the re-evaluation of `App`, it sees the new `logConsole` as a legitimate prop change and re-evaluates as well.

We can fix this in two ways .

### Part 4 — useCallback

Like einstein, we need to take the function `logConsole` out of the path of the Langoliers. Because it is a function, we use the `useCallback` hook to do so on line 21:

```jsx
const { useState, useMemo, useCallback } = React;

function Person(props) {
  console.log('Render Person');
  return (<span>{ props.identity.firstName } { props.identity.lastName }</span>);
}

const MemoPerson = React.memo(Person)

function Logger(props) {
  console.log('Render Log')
  return (<button onClick={props.onClick}>Log</button>);
}

const MemoLogger = React.memo(Logger) // <------- Memoize this

function App (props) {
  console.log('Render Hello');
  const [count, setCount] = useState(0);
  const einstein = useMemo(() => ({ firstName: "Albert", lastName: "Einstein" }), []);
  const logConsole = useCallback(() => console.log("HELLO, WORLD"), []);

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <MemoPerson identity={einstein} />
      <MemoLogger onClick={logConsole} /> {/* <------ */}
    </div>
  );
}

ReactDOM.render(
  <App />,
  document.getElementById('container')
);
```

Now finally:

![We've pulled everything to the future](/public/img/react-memo/react-memo-app-5.gif)
*Only the root App component updates; we have a clean render cycle where our child components aren’t being re-evaluated.*

We’ve saved our `Person` and our `Logger` from the Langoliers.

The alternate approach is to pull `einstein` and `logConsole` out of the component. In this case, it would work fine since there is no dependency on the component state. But that is not always the case; as long as there is a dependency on the state within the component, we’ll need to use `useMemo` or `useCallback`.

----

## A Bit of Opinion

In our simple case, there’s hardly any harm nor foul from the excess re-evaluation. In fact, you might have apps now where excess re-evaluation seemingly have no ill side effects.

However as apps grow in complexity, what tends to happen is that over-renders can lead to excess API calls or other weird side effects in complex applications when an unexpected re-evaluation occurs and creates new references in its wake causing child components to re-evaluate. And indeed, you’ll hit performance issues when there are large numbers of components to re-evaluate indiscriminately.

Ideally, all of your components are pure and free of errant side effects. But if that were the case, surely you would not have made it this far in this writeup 🤣.

<blockquote>
The React docs would lead one to believe that this is just some advanced optimization whereas it should probably be the very first topic in the docs.
</blockquote>

Conceptually, what `useMemo` and `useCallback` are doing is kind of like reaching into the past and pulling out a value or a function out of the path of the React Langoliers and into the future (more accurately, it shielded the values from being in their path to begin with by injecting them into the component function). When the next draw occurs, instead of creating new instances of `einstein` and `logConsole`, we get back the ones we saved from the past and thereby preventing our child components from re-evaluating by preventing a prop change (unless a legitimate dependency changed).

What is a bit surprising to me is that the React docs on event handling do not mention this at all! The React docs would lead one to believe that this is just some advanced optimization whereas it should probably be the very first topic in the docs. “Hey, React is kind of indiscriminate about the state of your app so you need to be smart and move it out of its way”.

In my opinion, this is taking functional purity too far. Just as we generally wouldn’t use Scheme or Prolog or OCaml to build user interfaces, it doesn’t make sense to take such a puritanical approach to building web app interfaces with JavaScript; just because you can, doesn’t mean you should!

![Classic](/public/img/react-memo/react-memo-scientists.webp)
*Functional purity for processing a stream of data? Yes; makes total sense. Functional purity for UI apps which fundamentally model some interactive process? I’m not sold that it’s the answer.*

There’s a reason why every major game engine, for example, is built using stateful, object-oriented principles: ***interactive interfaces are inherently stateful*** but React wants you to pretend that they’re not and move all state out of the way.

![React train](/public/img/react-memo/react-memo-train.gif)
*React coming through!*

Maybe this paradigm makes sense when you have thousands of front-end engineers contributing and maintaining a project. At that scale, maybe functional purity has some benefit. For the rest of us? The complexity is excess and the model leads us to doing weird things like nested contexts inside of nested contexts inside of nested contexts…

[That’s why these simple 15 lines of code (JSFiddle) can outperform React in both speed, simplicity, and non-functional metrics like maintainability](https://jsfiddle.net/7szur3ex/1/): it doesn’t assume a stateless interface but instead embraces it. In fact, a DOM element already knows if it’s selected or not and [HTML custom data attributes](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/data-*) — which we already use for E2E testing purposes — is perfectly suitable for this.

![A vanilla alternative](/public/img/react-memo/react-memo-vanilla-js.webp)
*Probably all that you need for 80–90% of the interactivity needed on an e-commerce product detail page. It’s blazingly fast. It will never need to be migrated nor updated for non-functional reasons like updates to Node, breaking NPM changes, React 19+, or Next.js migrations. No toolchain to adopt and configure. No cost of transpilation nor compilation necessary. Minimize it if you want, but this code is already microscopic compared to a React library include. 5 years from now, this code will still work as-is in every single browser without having to be touched for non-functional reasons. You’ll never have to update your CI/CD for this code. It’s never going to break your build process because of some package incompatibility. It’s never gonna give you up, never gonna let you down.*

This functional purity that React is aligning with means that you do more work (perhaps building a UI library with Prolog or Scheme would be a fun exercise!). Manually managing the dependencies array always feels so primitive like I’m slapping away the React Langoliers unless one of these things changes. *(I wonder if more people would be turned off by React and consider alternatives like Solid, Vue, or Svelte if they realized this from the beginning)*.

[Even the docs on `useMemo` has performance as the first bullet](https://beta.reactjs.org/reference/react/useMemo). I find that incredibly misleading because worse than poor performance is buggy behavior because of an unexpected re-evaluation from breaking functional purity with an errant side effect. [React’s documentation on `useCallback` is equally puzzling](https://beta.reactjs.org/reference/react/useCallback#preventing-an-effect-from-firing-too-often) because it fails to mention the reason why you need to shield the function to protect it from the React Langoliers; it’s not very forthcoming about the nature of React’s component and state management lifecycle (perhaps the assumption is that it is obvious?).

While our trivial case hardly matters, changing of objects or event handlers passed as props ***will*** cause a sub-portion of the component tree or the whole tree to re-evaluate so even in trivial cases it should always be avoided as it makes it more difficult to track this class of bugs down the line as the application grows and any one of dozens or even hundreds of components could be causing an errant side effect (of course, if we all wrote perfectly pure functional components every time, this wouldn’t be an issue).

Surely, no one creating a new reactive web UI library today would use such a brute force approach which forces developers to work harder to get it right (but here we are, all of us stuck dealing with React because of its ecosystem). The reason React is counter-intuitive — once you understand it — is probably because if we were just writing vanilla JS, we wouldn’t think *“Oh, let me throw everything away”*(stateless, functional purity when in fact, it makes sense for an app to be stateful); we’d keep the things that didn’t change and only propagate and evaluate the things that did change. React assumes everything might have changed and us devs are now responsible for telling it what didn’t change: *“Here, keep this thing and that thing and this other thing in a memo.”* to keep each evaluation pure.

For all of that, [React’s approach is neither performant](https://timkadlec.com/remembers/2020-04-21-the-cost-of-javascript-frameworks/):

![Via Tim Kadlec](/public/img/react-memo/react-memo-kadlec-1.webp)
*Via Tim Kadlec*

nor efficient:

![Via Tim Kadlec](/public/img/react-memo/react-memo-kadlec-2.webp)
*Via Time Kadlec*

[***And is consistently one of the poorest performing of the major FE frameworks***](https://krausest.github.io/js-framework-benchmark/2023/table_chrome_109.0.5414.87.html#eyJmcmFtZXdvcmtzIjpbImtleWVkL2FuZ3VsYXIiLCJrZXllZC9yZWFjdCIsImtleWVkL3JlYWN0LWhvb2tzIiwia2V5ZWQvc29saWQiLCJrZXllZC9zdmVsdGUiLCJrZXllZC92YW5pbGxhanMiLCJrZXllZC92dWUiLCJub24ta2V5ZWQvcmVhY3QiLCJub24ta2V5ZWQvc3ZlbHRlIiwibm9uLWtleWVkL3ZhbmlsbGFqcyIsIm5vbi1rZXllZC92dWUiXSwiYmVuY2htYXJrcyI6WyIwMV9ydW4xayIsIjAyX3JlcGxhY2UxayIsIjAzX3VwZGF0ZTEwdGgxa194MTYiLCIwNF9zZWxlY3QxayIsIjA1X3N3YXAxayIsIjA2X3JlbW92ZS1vbmUtMWsiLCIwN19jcmVhdGUxMGsiLCIwOF9jcmVhdGUxay1hZnRlcjFrX3gyIiwiMDlfY2xlYXIxa194OCIsIjIxX3JlYWR5LW1lbW9yeSIsIjIyX3J1bi1tZW1vcnkiLCIyM191cGRhdGU1LW1lbW9yeSIsIjI1X3J1bi1jbGVhci1tZW1vcnkiLCIyNl9ydW4tMTBrLW1lbW9yeSIsIjMxX3N0YXJ0dXAtY2kiLCIzNF9zdGFydHVwLXRvdGFsYnl0ZXMiXSwiZGlzcGxheU1vZGUiOjEsImNhdGVnb3JpZXMiOlsxLDIsMyw0LDVdfQ==) in nearly every metric.

![Terrible performance](/public/img/react-memo/react-memo-perf.webp)
*For all of it’s “purity”, React simply fails to perform against its peers.*

In any application at scale, your best bet is to simply keep the state and callbacks out of the path of the Langoliers to begin with. Valtio’s `getters` and `proxy-memoize` is one way to do this. [Nanostores docs also mention the same philosophy](https://github.com/nanostores/nanostores#best-practices):

<blockquote>
We recommend moving all logic, which is not highly related to UI, to the stores. Let your stores track URL routing, validation, sending data to a server.
</blockquote>

This works because it preserves referential integrity by instantiating the objects and functions outside of the reach of the React Langoliers.

My pro-tip? If you like JSX/TSX, use Solid.js or [Vue.js with JSX/TSX](https://github.com/WalkAlone0325/tsx-naive-admin/blob/main/src/App.tsx) — at least you’ll get better performance. If you need SSR/SSG? Use [Astro.js](https://astro.build/) instead of Next.js and pick Solid.js, Vue.js, or Svelte.js. Even better if you’re using Tailwind since you’re not bound by React.js specific component libraries.

The truth is that React’s model is dated but React is stuck in a Catch-22. React is the modern equivalent of *“No one ever got fired for buying IBM/Oracle/Microsoft”*. The core model can’t be modernized without a lot of pain because of the huge ecosystem so innovation can only happen outside of React’s path (see Solid, Preact, Svelte, Vue and Vapor mode). React is seemingly stuck in a cycle of terrible ergonomics and terrible performance *because of its own success*.

----

I hope that you’ve found this short walkthrough useful in understanding how to actually use useMemo and useCallback. There are countless numbers of more in-depth articles and write-ups, but maybe this one can help you better visualize how these two interact to pull the past into the future. Now you just need to imagine whether it’s OK when the React Langoliers consume an object or function to decide if you need `useMemo` or `useCallback`!as