---
title: "Mysterious Maps"
date: 2022-01-19T16:09:23+01:00
summary: ""
categories: []
draft: true
---

## What is a map?

A map, in the context of V8, is a specification of the heap layout of an object.

## The Map Class

```cpp
class Map {
}
```

## Automatic Map Generation

`codegen/code-stub-assembler.h:4292` defines the following macro:

```cpp
#define CLASS_MAP_CONSTANT_ADAPTER(V, rootIndexName, rootAccessorName,     \
                                   class_name)                             \
  template <>                                                              \
  inline bool CodeStubAssembler::ClassHasMapConstant<class_name>() {       \
    return true;                                                           \
  }                                                                        \
  template <>                                                              \
  inline TNode<Map> CodeStubAssembler::GetClassMapConstant<class_name>() { \
    return class_name##MapConstant();                                      \
  }
```

This does not what I expected. It seems I am at a loss once again.

**Seemingly important files:**

+ `objects/map.cc`
+ `roots/roots.h`
+ `heap/factory.h`
+ `compiler/js-graph.cc`
