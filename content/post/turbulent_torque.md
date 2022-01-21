---
title: "Turbulent Torque"
date: 2022-01-20T14:11:49+01:00
summary: ""
categories: []
draft: true
---

Cheatsheet for Torque or whatever.

## Files to add Stuff when you add Stuff... and Stuff

+ `BUILD.gn` (torque_files, sources)
+ `src/init/boostrapper.cc` (JS function, class, etc. definitions - link C++/torque to JS)

## Keyword Summary

+ **macro:** Macros are callables that are inlined where they are used (hence the name)
+ **builtin:** Builtins are callables that are used to generate V8 builtins
+ **transient**: Transient objects can potentially change their heap layout at runtime
+ **transitioning:** Callables marked as transitioning can potentially change the layout of a transient type, after such a callable, the respective transient objects are invalidated and can not be accessed, thereby guaranteeing safe access

### Creating a Builtin

```
transitioning javascript builtin NameOfBuiltin(js-implicit context: NativeContext, receiver: JSAny)(...arguments): JSAny {
    ...
}
```
