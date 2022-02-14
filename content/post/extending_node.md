---
title: "A non-comprehensive Guide on extending V8"
date: 2022-01-17T18:26:33+01:00
summary: ""
categories: []
draft: true
---

V8 is an open-source JavaScript engine by Google. There is decent documentation for it on their [homepage](https://v8.dev/). However, certain parts of it are slightly outdated and others lack in depth. The goal of this post is to convey a basic understanding of how V8 works and how it can be extended by developers.

As I understand it, the following steps have to be taken in order to add a new feature (e.g. a new global method) to V8:

1. Define required classes, functions, etc. in Torque
2. (Optional) If you added new torque files or namespaces in step 1., add these to BUILD.gn
3. (Optional) Additional C++ code, if required
4. Link the defined features to the JavaScript world
5. Compile the project

I'm gonna start with an example—adding a method to the String class.

Step one is implementing the method in torque. We want this method to be a javascript builtin, so it can be called directly from javascript.

```
javascript builtin StringPrototypeTest(
    js-implicit context: NativeContext, receiver: JSAny)(): Boolean {
        return True;
}
```

## Adding a new Torque object

1. Add torque code somewhere in *src/objects*
2. Write header and source files belonging to the code
3. Add filename to *torque_files* in *BUILD.gn*
4. Add created header files to *sources* in *BUILD.gn*
5. Be very confused about weird STATIC_ASSERTS
6. In case of adding new members to String please edit kStringResourceOffset instead of commenting out the ASSERTION...

## Torque

Torque is a language used to ease development of JavaScript built-ins, global objects, methods, etc.
