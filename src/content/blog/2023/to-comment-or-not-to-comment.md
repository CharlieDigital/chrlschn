---
title: "To Comment or Not to Comment"
description: "Ruminations on the timeless debate of comments in code."
pubDate: "2023 Jul 6"
socialImage: "/public/imgto-comment-or-not-to-comment/socrates-coding.webp"
slug: "2023/07/to-comment-or-not-to-comment"
tags: "software engineering"
---

----

Probably the most memorable course in my computer science curricula was CS431: Software Engineering at Rutgers. The course detail website describes the class as such:

> The course studies the problems, methods, processes and tools involved in the development of large software systems that are reliable and maintainable, and meet their users’ needs. This is carried out in the context of a significant, term-long multi-person team project.

Aside from the project, much of the course focused around reading “classics” in software engineering and open discussion. One influential piece of reading that has had a profound effect on my personal philosophy in the field of software engineering is Code Complete by McConnell.

This particular sentence is one that I often recall when I think about how I want to construct a particular piece of code and when I review others’ code:

> The smaller part of the job of programming is writing a program so that the computer can read it; the larger part is writing it so that other humans can read it.

A big part of coding for human consumption is understanding the role and purpose of commenting.

In chapter 32.3 To Comment or Not to Comment, McConnell opens with an imaginary dialogue between Socrates and his computer science students on the topic of comments (which I’m condensing here):

> CALLICES: Comments are an academic panacea, but everyone who’s done any real programming knows that comments make the code harder to read, not easier. English is less precise than Java or Visual Basic and makes for a lot of excess verbiage.
>
> THRASYMACHUS: Good comments don’t repeat the code or explain it. They clarify it’s intent. Comments should explain, at a higher level of abstraction than the code, what you’re trying to do.
>
> ISMENE: Writing a comment makes you think harder about what your code is doing. If it’s hard to comment, either it’s bad code or you don’t understand it well enough.

The exchange spans several pages and highlights the various facets and perspectives on commenting in code and my own takeaway are three key bullets from McConnell:

1. “Poor comments are worse than no comments”
2. “Write comments at the level of the code’s intent”
3. “If you find anything that isn’t obvious from the code itself, put it into a comment”

To that, I’ll add a fourth that wasn’t quite as applicable when McConnell wrote the book:

- Add a link to the relevant reference.

Let’s examine comments with each of these perspectives in mind.

----

## “Poor comments are worse than no comments”

We’ve all seen the useless comment that adds nothing to the code and instead only repeats the code itself:

```ts
/**
* Register a subscription.
*/
public register(
  name: string,
  subscription: Unsubscribe
) {
  if (this._subscriptions.has(name)) {
    return
  }

  this._subscriptions.set(name, subscription)
}
```

Indeed, as Callicles would agree, this comment simply adds noise and no value whatsoever. In such cases, there is no purpose in adding this comment.

What’s missing is the intent of this function: why are we registering this?

## “Write comments at the level of intent”

While the intent can be deduced by reading the parameter and type names, spelling it out clearly can help readers understand why the function exists and how it behaves:

```ts
/**
* Register a subscription keyed by name with a callback function to
* unsubscribe which will be invoked when the module is unloaded.
*/
public register(
  name: string,
  subscription: Unsubscribe
) {
  if (this._subscriptions.has(name)) {
    return
  }

  this._subscriptions.set(name, subscription)
}
```

The comment now identifies the intent of registering a subscription: so that we can unsubscribe later using the named key.

## “If you find anything that isn’t obvious from the code itself, put it into a comment”

Looking at this code, there are two aspects which are not obvious when reading it from two different perspectives:

1. First and most important is why a subscription should be unsubscribed and what purpose this has in the scope of the system.
2. Second is from the perspective of a user of the function seeing the comment in the IDE’s intellisense: the behavior of the name parameter.

We can make this more clear for both cases by expanding our comment:

```ts
/**
* Register a subscription keyed by name with a function to unsubscribe which
* will be invoked when the module is unloaded. Subscriptions which are not
* registered must be manually cleaned up otherwise it will cause excessive
* websocket connections to be open and result in unexpected updates.
*
* @param name If the name already exists, operation is idempotent
* @param subscription The callback function provided when a subscription is created
*/
public register(
  name: string,
  subscription: Unsubscribe
) {
  if (this._subscriptions.has(name)) {
    return
  }

  this._subscriptions.set(name, subscription)
}
```

There are other cases where I think this guidance can be applied:

First is explaining a business/domain constraint. In many cases, there are decisions or compromises made in the code based on specific business/domain constraints and it is often easier to document or reference such constraints within the code.

For example:

```ts
/**
* Adds a named calendar on behalf of the user.  Returns a successful result
* if the calendar is added for the user.
*
* @param name The display name of the calendar to add
* @param user The user that the calendar belongs to
*/
public addCalendar(
  name: string,
  user: User
) : AddCalendarResult {
  // If the user already has 3 calendars, check the subscription level to
  // determine if they are on a paid tier since the free tier only allows 3
  // calendars.
  if (user.activeCalendars.length === 3) {
    // Additional code omitted
  }

  // Additional code omitted
}
```

_(Side note: there’s a decision to be made here whether to document the parameters or use longer, more explicit names for the parameters like calendarDisplayName and calendarOwnedByUser; my preference is for shorter names and using comments as it makes the code itself more concise to read.)_

Here, it is not so important to say Check if user already has 3 calendars, rather there is a business rule and the value 3 is not an arbitrary number but rather tied to a business requirement.

Of course, an alternate is to write this instead:

```ts
/**
* Adds a named calendar on behalf of the user.  Returns a successful result
* if the calendar is added for the user.
*
* @param name The display name of the calendar to add
* @param user The user that the calendar belongs to
*/
public addCalendar(
  name: string,
  user: User
) : AddCalendarResult {
  if (user.activeCalendars.length === process.env.FREE_CALENDAR_LIMIT) {
    // Additional code omitted
  }

  // Additional code omitted
}
```

In this case, the comment becomes optional as the code is quite clear on the intent of the conditional.

Second is explaining why a compromise or technical decision or was made. In some cases, it may not be obvious why a specific implementation has been made. For example, there may be downstream technical constraints on a different subsystem that a piece of code must account for:

```ts
/**
* Adds a named calendar on behalf of the user.  Returns a successful result
* if the calendar is added for the user.  The name will be normalized to
* remove non-alphanumeric characters as the calendar API will throw an error
* otherwise.
*
* @param name The display name of the calendar to add
* @param user The user that the calendar belongs to
*/
public addCalendar(
  name: string,
  user: User
) : AddCalendarResult {
  if (user.activeCalendars.length === process.env.FREE_CALENDAR_LIMIT) {
    // Additional code omitted
  }

  // Calendar API only allows alpha-numeric characters.
  const normalizedName = removeNonAlphaNumericCharacters(name)

  // Additional code omitted

  return {
    succeeded: true,
    message: 'Added calendar',
    name: normalizedName
  }
}
```

Other cases might be when a piece of code is working around a known bug or issue or constraint so it uses a non-standard design: add a note as to why the implementation isn’t optimal. Or perhaps in the case of prompt engineering, examples of prompts that didn’t work well so future optimizations and improvements can bypass those options.

## Add a link to the relevant reference.

In many cases, adding a link to the relevant reference can be useful as there are specific details that can help future travelers understand the details of the implementation.

Following our previous example:

```ts
/**
* Adds a named calendar on behalf of the user.  Returns a successful result
* if the calendar is added for the user.  The name will be normalized to
* remove non-alphanumeric characters as the calendar API will throw an error
* otherwise.
*
* See: https://example.com/calendar/api/docs/addNewUserCalendar
*
* @param name The display name of the calendar to add
* @param user The user that the calendar belongs to
*/
public addCalendar(
  name: string,
  user: User
) : AddCalendarResult {
  if (user.activeCalendars.length === process.env.FREE_CALENDAR_LIMIT) {
    // Additional code omitted
  }

  // Calendar API only allows alpha-numeric characters.  See link above
  // for restrictions and rules.
  const normalizedName = removeNonAlphaNumericCharacters(name)

  // Additional code omitted

  return {
    succeeded: true,
    message: 'Added calendar',
    name: normalizedName
  }
}
```

By adding a link to the reference for the constraint, developers that encounter this code can quickly find the reference to the relevant documentation.

A common use case for myself is when a business decision is made to create a sub-optimal implementation for the sake of shipping the code with acceptable risk (for example, there’s a complex optimization that can be made later). Often, it can be helpful to attribute the decision so it can be referenced later.

I find that there are three types of “links” that make sense to add:

1. Links to reference documentation (example above)
2. A link to an issue in a tracking system or at least an issues identifier so a future traveler can find the additional background and decision making around a piece of code. For example See issue #3456 for more context.
2. A link to where the code was sourced from. If the code is taken from some reference documentation or from a Stackoverflow post, it can be helpful to provide a link or reference for additional context. For example: See: https://stackoverflow.com/... for discussion and options.

----

“To comment or not to comment” is an eternal debate that every developer and team faces when coalescing on a common style (or your own best practices).

For myself, as I often work on multiple codebases, there’s a very pragmatic reason to comment code: it simply makes it easier to unload and arbitrarily pick up a codebase later on. Code is dense and few people can read and understand 100 lines of code as fast as they can read 100 lines of prose. It is often easier to explain to my future self in prose what a piece of code is doing before context switching into another piece of code.

McConnell’s Code Complete is a classic that I recommend for every developer’s bookshelf. His discourse on commenting and the broader art of programming may seem outmoded in this age of AI-generated code, but then again, perhaps it makes it all the more relevant to separate those who embrace the craft and those practiced only in assembling.