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

--

##
