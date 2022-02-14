---
title: "Grizzly Genesis"
date: 2022-01-26T14:31:26+01:00
summary: ""
categories: []
draft: true
---

When a new JS execution context is created, it is done through the so called `Genesis`-Class. Calling the constructor of this class initializes its' member `Handle<Context> result_` to a handle pointing to a newly generated context.

## The gigantic Constructor

Basic blocks:

+ Save the context (not sure)
+ Initialize `global_proxy_` (either as uninitialized global proxy, or obtain serialized proxy)
+ Initialize `native_context_` if Isolate was initialized from snaspshot
+ If `native_context_` != `null` (if it was loaded from snapshot):
  + set isolate context to `native_context_`
  + increment number of contexts created by snapshots in Isolate
  + If the passed  in `global_proxy_template` is not null:
    + Create a global object from the template
    + Hook it up (`HookUpGlobalObject`)
  + Else:
    + Just hook up the global Proxy (`HookUpGlobalProxy`)
+ Else:
  + Do everything from scratch I guess
  + `CreateRoots`: Create Basic Objects (allocate new native context, add it to some list, set as new isolate context)
  + Initialize the new context (for whatever reason, that is a method of `MathRandom`)
  + `CreateEmptyFunction`: Create a `Handle<JSFunction>` for a function that returns *undefined*
  + Create a whole bunch of things:
    + `CreateSloppyModeFunctionMaps`
    + `CreateStrictModeFunctionMaps`
    + `CreateObjectFunction`
    + `CreateIteratorMaps`
    + `CreateAsyncIteratorMaps`
    + `CreateAsyncFunctionMaps`
  + Create a new global object
  + Initialize map caches (whatever those are)
  + Initialize the global object (`InitializeGlobal`)
  + Initialize iterator functions
  + Initialize callsite builtins
  + `InstallABunchOfRandomThings` (yes, thats how its called - does what it says)
  + `InstallExtrasBindings`
  + `ConfigureGlobalObject`
