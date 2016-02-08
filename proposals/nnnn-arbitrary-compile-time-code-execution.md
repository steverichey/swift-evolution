# Allow arbitrary compile-time code execution

* Proposal: [SE-NNNN]()
* Author(s): [Steve Richey](https://github.com/steverichey)
* Status: **Awaiting review**
* Review manager: TBD

## Introduction

Allow the execution of arbitrary Swift code at compile-time to handle lengthy one-time data generation, or tasks that the developer would like to automate to run every time the application is compiled.

Swift-evolution thread: [link to the discussion thread for that proposal](https://lists.swift.org/pipermail/swift-evolution)

## Motivation

It is often necessary to execute code at build time. This is commonly handled through the use of build scripts (such as Xcode's "Build Phases"), but there's no reason why that code should be considered a special case. Swift developers could benefit from executing code at build time to perform

## Proposed solution

Jai, an experimental programming language designed for game development, uses the octothorpe-prefixed '#run' syntax (as described [here](https://sites.google.com/site/jailanguageprimer/#TOC-Arbitrary-Compile-Time-Code-Execution) which matches nicely with Swift's special expression syntax for the following:
* `#available`
* [SE-0022](https://github.com/apple/swift-evolution/blob/master/proposals/0022-objc-selectors.md)'s `#selector`
* [SE-0028](https://github.com/apple/swift-evolution/blob/master/proposals/0028-modernizing-debug-identifiers.md)'s `#file`, `#line`, `#column`, `#function`, `#dsohandle`

An example:

```swift
func myFuction() -> String {
  return "foo"
}

let someValue = #run myFunction()
```

At compile-time, `myFunction` is evaluated, and the result is assigned to `someValue`. At run-time, `someValue` is a `String` with the value `"foo"`.

## Detailed design

Describe the design of the solution in detail. If it involves new
syntax in the language, show the additions and changes to the Swift
grammar. If it's a new API, show the full API and its documentation
comments detailing what it does. The detail in this section should be
sufficient for someone who is *not* one of the authors to be able to
reasonably implement the feature.

## Impact on existing code

This is a new feature which is not expected to impact existing code. No alternatives exist in the current Swift API which should be considered for replacement or deprecation.

## Alternatives considered

_pending discussion on swift-evolution_
