---
title: "TypeScript Conditional Types for Type Safety (Without Assertions)"
description: "Using conditional types to achieve type safety without having to use 'as'"
pubDate: "2025 June 14"
socialImage: "/public/img/complexity/rube-goldberg.gif"
slug: "2025/06/typescript-conditional-types-for-type-safety-without-assertions"
tags: "typescript"
---

----

## Summary

TypeScript conditional types are a useful tool to have in your tool belt for dealing with type safety across a large number of related types.  In this example, see how to use a type value to discriminate types without having to use the `as` assertion to "cast" the type.

---

## Intro

[TypeScript conditional types](https://www.typescriptlang.org/docs/handbook/2/conditional-types.html) are one of the more interesting constructs that allow some flexibility in how to safely manage a large number of related types without running into the limitations with discriminated unions.

A perfect use case is for a system that is intended to receive webhooks where the payloads have a small set of common properties -- one of which can be used to discriminate the message type -- but each message type also carries a distinct payload.

It can be a bit gnarly to wrap your head around it the first time, but let's see how we can use it to create type-safe call paths at build time.

> 💡 [See the finished TypeScript Playground example](https://www.typescriptlang.org/play/?#code/C4TwDgpgBAshDO8CGBzCAVc0C8UDkSArsABZ5QA++8IAdgMblV6G3yFhgD2ATsBABM8AKGGhIsBMjQAhJPAgAedFAgAPfrQHxJiVBiwA+KLgDewqFHEQAXFHQAaC1ACWAu-GA8XtFE8tgSCAANlxI7lCe3r7CAL6i1lAAgsQkcHpoJlDmlm4QtMAuoB5ePihxUABkutIQcgqKBKl4hglYUADKdPTptVk5Vi4AtgjASENgJdHllsBcYC70U2UV1b369Up4NAwtbRIAqmwc3HyC65m4F3XyW6zsnLz8Qq1i7dfKqhr52jX6mJBjLhnCp1Jpfk1SOQAPzJVLXKA2EFfcE6bbdGGdboIpGWI4PU7Pa6iABmrHohS4tCgPAg9AgLgAbhAABRDeAoOwfa4AiCGACU2Wc8AA7kV6CQoGyOQA6ayCgaWei3fBEKF2WnAQg8akkJBaYIQFKkaUofnOJUq9G7DUQLU6qB6g0QLoMU3myyWAQQElEYLAW323X6gSG-EnJ6Cd3OeLxYRkhiU4PO40kU12VPXBWiOMJikuKmOkOG130dNYhhZoVxUnkpNF53hx5nATlpuE85SfTZmvCIA)

--

## Basic Example

### Define the Message Type

To start with, we'll define our message types:

```ts
type MessageType = 'auth' | 'sync'
```

Here, we user simple strings to define the different types of messages that we expect.  I'm not sure if there is a practical limit, but I tested up to 40 without issue.

### Define the Base or Common Message

Now we can define a base or common message structure:

```ts
type MessageBase<T extends MessageType> = {
  type: T,
  id: string,
  payload: string
}

type AuthMessage = {
  identity: string
} & MessageBase<'auth'> // 👈 pick our type

type SyncMessage = {
  timestamp: string
  topic: string
} & MessageBase<'sync'> // 👈 pick our type
```

### Define the Conditional Type

With our types in place, we can then define our conditional type:

```ts
type Message<T extends MessageType> =
  // prettier-ignore
  T extends 'auth' ? AuthMessage :
  T extends 'sync' ? SyncMessage :
  never
```

The conditional type allows us to define a "concrete" type based on *a discriminating property value*.

If you're familiar with C#, this may look like [a C# switch expression with pattern matching](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/switch-expression).  In fact, it behaves very similarly.

An alternate is to declare an explicit `unsupported`:

```ts
type MessageType = 'auth' | 'sync' | 'unsupported'

type UnsupportedMessage = MessageBase<'unsupported'>

type Message<T extends MessageType> =
  // prettier-ignore
  T extends 'auth' ? AuthMessage :
  T extends 'sync' ? SyncMessage :
  UnsupportedMessage
```

### Define the Handler Function

Finally, we can define our handler function:

```ts
function receive(msg: Message<MessageType>) {
  switch (msg.type) {
    case 'auth': return handleAuth(msg)
    case 'sync': return handleSync(msg)
    default: return handleUnsupported(msg)
  }
}

function handleAuth(msg: AuthMessage) {}
function handleSync(msg: SyncMessage) {}
function handleUnsupported(msg: UnsupportedMessage) {}
```

Note how each handler method gets a typed parameter cleanly derived from the `msg.type` in the `switch` statement.
