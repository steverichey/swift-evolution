# Allow arbitrary compile-time code execution

* Proposal: [SE-NNNN]()
* Author(s): [Steve Richey](https://github.com/steverichey)
* Status: **Awaiting review**
* Review manager: TBD

## Introduction

Allow the execution of arbitrary Swift code at compile-time to handle lengthy one-time data generation, or tasks that the developer would like to automate to run every time the application is compiled.

Swift-evolution thread: [link to the discussion thread for that proposal](https://lists.swift.org/pipermail/swift-evolution)

## Motivation

It is often necessary to execute code at build time. This is commonly handled through the use of build scripts (such as Xcode's "Build Phases"), but there's no reason why that code should be considered a special case. Swift developers could benefit from executing code at build time to perform tasks, and even sharing code between their build environment and runtime environment.

One practical example is generating lookup tables. The data that makes up these tables may be relatively expensive to compute. By generating these lookup tables at compile-time, we can reduce the runtime cost of accessing these tables to near zero, and provide a better experience for the end user.

## Proposed solution

Jai, an experimental programming language designed for game development, uses the octothorpe-prefixed `#run` syntax (as described [here](https://sites.google.com/site/jailanguageprimer/#TOC-Arbitrary-Compile-Time-Code-Execution)) which matches nicely with Swift's special expression syntax for the following:
* `#available`
* [SE-0022](https://github.com/apple/swift-evolution/blob/master/proposals/0022-objc-selectors.md)'s `#selector`
* [SE-0028](https://github.com/apple/swift-evolution/blob/master/proposals/0028-modernizing-debug-identifiers.md)'s `#file`, `#line`, `#column`, `#function`, `#dsohandle`

An example:

```swift
func myFunction() -> String {
  return "foo"
}

let someValue = #run myFunction()
```

At compile-time, `myFunction` is evaluated, and the result is assigned to `someValue`. At run-time, `someValue` is a `String` with the value `"foo"`.

## Detailed design

There may arise cases where a function depends on code that hasn't been compiled yet, as in the following example:

```swift
func foo() -> String {
  return bar()
}

let value = #run foo()

func bar() -> String {
  return "bar"
}
```

In this case, [TODO]. (Thanks to Adrian Kashivskyy for pointing this out on the `swift-evolution` mailing list).

This does represent an issue for types that cannot be serialized. [TODO: discussion]. (Thanks to Félix Cloutier for pointing this out).

To alleviate some of the issues related to serialization, compile time dependencies, and so on, we may want to introduce an attribute to flag a function as compile-time available:

```swift
@compile_time func myFunction(foo: String) -> Expr { ... }
```

This allows us to call functions at compile time, with intelligent compiler warnings if we attempt to call a function which is not marked as `@compile_time` (or whatever similar attribute term is chosen). [TODO: further discuss]. (Thanks to Haravikk for recommending the attribute concept).

In some cases, the compiler may be able to pre-optimize our code. A function that generates powers of two, which is only called once, could be inlined by the compiler. In many cases, this would obviate the need for a `#run` command or similar. However, I don't believe that the functionality offered by this proposal is limited to optimizing code. This proposal is intended to benefit those who are doing  time-intensive processes that they'd like to "bake" into the generated executable, while also allowing for compile-time functionality like updating build numbers, application icons, and so on. If we avoid `#run` in favor of compiler optimizations, we avoid a whole class of functionality while also creating uncertainties about the cost of running our code at runtime. The explicitness of the proposed call helps to alleviate that uncertainty. (Thanks to Haravikk for pointing out issues pertaining to compiler optimizations).

## Impact on existing code

This is a purely additive feature which is not expected to impact existing code. No alternatives exist in the current Swift API which should be considered for replacement or deprecation.

## Alternatives considered

A variant of the [Lua](http://www.lua.org/) programming language, [Metalua](http://metalua.luaforge.net), offers syntax similar to the following for building values and abstract syntax trees (ASTs) with explicit and flexible shifts between meta levels:

```swift
// Compile-time definitions which produce Expr nodes
#{
  func compileTimeFunction() -> Expr { ... }
}#

// Evaluate a compile-time function and expand the result expression here
let myValue = #( compileTimeFunction() )
```

In the proposed solution, [TODO]. (Thanks for Joe Groff for referencing Metalua and providing the example syntax).

Another issue pertains to external modules. At compile-time, it may be difficult or impossible to detect whether or not we need to reevaluate the result of a `#run` call. This may require the developer to manually clean their build products to ensure that the result is recomputed. [TODO: how to fix]. (Thanks to Wallacy for pointing out issues related to modules).
