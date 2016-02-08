# Allow arbitrary compile-time code execution

* Proposal: [SE-NNNN]()
* Author(s): [Steve Richey](https://github.com/steverichey)
* Status: **Awaiting review**
* Review manager: TBD

## Introduction

Allow the execution of arbitrary Swift code at compile-time to handle lengthy one-time data generation, or tasks

Swift-evolution thread: [link to the discussion thread for that proposal](https://lists.swift.org/pipermail/swift-evolution)

## Motivation

It is often necessary to execute code at build time. This is commonly handled through the use of build scripts (such as Xcode's "Build Phases"), but there's no reason why that code should be considered a special case. Swift developers could benefit from executing code at build time to perform

## Proposed solution

```swift
let value = #run myFunction()
```

## Detailed design

Describe the design of the solution in detail. If it involves new
syntax in the language, show the additions and changes to the Swift
grammar. If it's a new API, show the full API and its documentation
comments detailing what it does. The detail in this section should be
sufficient for someone who is *not* one of the authors to be able to
reasonably implement the feature.

## Impact on existing code

Describe the impact this change will have on existing code. Will some
Swift applications stop compiling due to this change? Will applications still
compile but produce different behavior than they used to? Is it
possible to migrate existing Swift code to use a new feature or API
automatically?

## Alternatives considered

Describe alternative approaches to addressing the same problem, and
why you chose this approach instead.
