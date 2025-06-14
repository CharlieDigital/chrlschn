---
title: "TypeScript Conditional Types for Type Safety (Without Assertions)"
description: "Using conditional types to achieve type safety without having to use 'as'"
pubDate: "2025 June 14"
socialImage: "/public/img/ts-conditional/conditional-types.png"
slug: "2025/06/typescript-conditional-types-for-type-safety-without-assertions"
tags: "typescript,c#"
---

----

## Summary

TypeScript conditional types are a useful tool to have in your tool belt for dealing with type safety across a large number of related types.  In this short walkthrough, see how to use a property value to discriminate types without having to use the `as` assertion to "cast" the type.

Using conditional types this way can offer a limited facsimile of C# switch expressions with pattern matching (a very, very limited approximation 🤣).

----

## Intro

[TypeScript conditional types](https://www.typescriptlang.org/docs/handbook/2/conditional-types.html) are one of the more interesting constructs that allow some flexibility in how to safely manage a large number of related types without running into the limitations with discriminated unions.

A perfect use case is for a system that is intended to receive webhooks where the payloads have a small set of common properties -- one of which can be used to discriminate the message type -- but each message type also carries a distinct payload.

It can be a bit gnarly to wrap your head around it the first time, but let's see how we can use it to create type-safe call paths at build time.

> 💡 [See the finished TypeScript Playground example](https://www.typescriptlang.org/play/?#code/C4TwDgpgBAshDO8CGBzCAVc0C8UDkSArsABZ5QA++8IAdgMblV6G3yFhgD2ATsBABM8AKGGhIsBMjQAhJPAgAedFAgAPfrQHxJiVBiwA+KLgDewqFHEQAXFHQAaC1ACWAu-GA8XtFE8tgSCAANlxI7lCe3r7CAL6i1lAAgsQkcHpoJlDmlm4QtMAuoB5ePihxUABkutIQcgqKBKl4hglYUADKdPTptVk5Vi4AtgjASENgJdHllsBcYC70U2UV1b369Up4NAwtbRIAqmwc3HyC65m4F3XyW6zsnLz8Qq1i7dfKqhr52jX6mJBjLhnCp1Jpfk1SOQAPzJVLXKA2EFfcE6bbdGGdboIpGWI4PU7Pa6iABmrHohS4tCgPAg9AgLgAbhAABRDeAoOwfa4AiCGACU2Wc8AA7kV6CQoGyOQA6ayCgaWei3fBEKF2WnAQg8akkJBaYIQFKkaUofnOJUq9G7DUQLU6qB6g0QLoMU3myyWAQQElEYLAW323X6gSG-EnJ6Cd3OeLxYRkhiU4PO40kU12VPXBWiOMJikuKmOkOG130dNYhhZoVxUnkpNF53hx5nATlpuE85SfTZmvCIA)

----

## Basic Example

### Define the Message Type

To start with, we'll define the expected message types:

```ts
type MessageType = 'auth' | 'sync'
```

Here, simple strings are used to define the different types of messages that the system will consume.  (I'm not sure if there is a hard limit as is the case with discriminated type unions, but I tested up to 40 without issue.)

### Define the Base or Common Message

Next define a base or common message structure that exposes a generic parameter which will be the type of the discriminating property (`MessageType` in this case):

```ts
type MessageBase<T extends MessageType> = {
  type: T, // 👈 👆 The discriminator
  id: string,
  payload: string
}

type AuthMessage = {
  identity: string
} & MessageBase<'auth'> // 👈 pick a specific value

type SyncMessage = {
  timestamp: string
  topic: string
} & MessageBase<'sync'> // 👈 pick a specific value
```

### Define the Conditional Type

With the types in place, then it is possible to move on to defining the conditional type which will resolve into a specific type based on the `MessageType`:

```ts
type Message<T extends MessageType> =
  // prettier-ignore
  T extends 'auth' ? AuthMessage :
  T extends 'sync' ? SyncMessage :
  never
```

The conditional type allows defining a "concrete" type based on *a discriminating property value*.

An alternate here is to declare an explicit `unsupported`:

```ts
type MessageType = 'auth' | 'sync' | 'unsupported'

type UnsupportedMessage = MessageBase<'unsupported'>

type Message<T extends MessageType> =
  // prettier-ignore
  T extends 'auth' ? AuthMessage :
  T extends 'sync' ? SyncMessage :
  UnsupportedMessage
```

This way, there is always a concrete type.

For those familiar with C#, this may bear some resemblance to [C# switch expressions with pattern matching](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/switch-expression) (more on this later).  In fact, it behaves very similarly.

### Define the Handler Function

The last step is to define the receiver of these messages and routing to the handlers:

```ts
// 👇 This is the entrypoint for the generic payload
function receive(msg: Message<MessageType>) {
  switch (msg.type) {
    case 'auth': return handleAuth(msg)
    case 'sync': return handleSync(msg)
    default: return handleUnsupported(msg)
  }
}

// 👇 The specific handlers
function handleAuth(msg: AuthMessage) {}
function handleSync(msg: SyncMessage) {}
function handleUnsupported(msg: UnsupportedMessage) {}
```

Note how each handler method gets a typed parameter cleanly derived from the `msg.type` in the `switch` statement.  Each function receives a typed payload correctly and no type assertion with `as` is required to coerce the types!

This provides a single entry point where messages can be routed with the correct type to standalone handlers.  This is a very useful tool for writing maintainable, well-encapsulated code by allowing types to flow through the call chain from a single entry point.

## Putting it All Together

```ts
type MessageType = 'auth' | 'sync' | 'unsupported'

type MessageBase<T extends MessageType> = {
  type: T,
  id: string,
  payload: string
}

type AuthMessage = {
  identity: string
} & MessageBase<'auth'>

type SyncMessage = {
  timestamp: string
  topic: string
} & MessageBase<'sync'>

type UnsupportedMessage = MessageBase<'unsupported'>

type Message<T extends MessageType> =
  T extends 'auth' ? AuthMessage :
  T extends 'sync' ? SyncMessage :
  UnsupportedMessage

function receive(msg: Message<MessageType>) {
  switch (msg.type) {
    case 'auth': return handleAuth(msg)
    case 'sync': return handleSync(msg)
    default: return handleUnsupported(msg)
  }
}

function handleAuth(msg: AuthMessage) { }
function handleSync(msg: SyncMessage) { }
function handleUnsupported(msg: UnsupportedMessage) { }
```

----

## Switch Expressions in C#

I mentioned earlier that this strongly resembles switch expressions with pattern matching in C#.  How exactly does that look?

```csharp
void Receive(Message message)
{
  Action result = message switch
  {
    AuthMessage authMessage when message.Type == MessageType.Auth =>
      () => handleAuthMessage(authMessage),
    SyncMessage syncMessage when message.Type == MessageType.Sync =>
      () => handleSyncMessage(syncMessage),
    _ => () => handleUnknownMessage(message),
  };

  result();
}

void handleAuthMessage(AuthMessage authMessage) {}
void handleSyncMessage(SyncMessage syncMessage) {}
void handleUnknownMessage(Message message) {}

enum MessageType { Auth, Sync };

record Message(MessageType Type, string Id, string Payload);

record AuthMessage(string Id, string Payload, string Identity)
  : Message(MessageType.Auth, Id, Payload);

record SyncMessage(string Id, string Payload, string SyncId)
  : Message(MessageType.Sync, Id, Payload);
```

Here, you can see just how similar these two constructs are at achieving the same result.  The key difference is that the C# implementation *remains type-safe at runtime* as the type metadata remains whereas the TypeScript implementation should probably still be implemented with a schema like Zod or Valibot.

It should be noted that C# switch expressions with pattern matching are much, much more powerful and allow for far more expressive pattern expressions than what has been demonstrated in this basic example.  [See Tim Deschryver's excellent writeup for a more in depth exploration](https://timdeschryver.dev/blog/pattern-matching-examples-in-csharp).

----

## Closing Thoughts

This mechanism of using TypeScript conditional types can help write easy to maintain, type-safe code paths when the system needs to handle a large number of payload variations without relying on type assertions with `as`.
