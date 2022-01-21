---
title: "Oblivious Objects"
date: 2022-01-20T12:10:05+01:00
summary: ""
categories: []
draft: true
---

**Disclaimer:** The title is addressing my own obliviousness, to how Objects work in V8.

All JavaScript objects in V8 inherit from `Object` ([src](https://chromium.googlesource.com/v8/v8/+/refs/heads/main/src/objects/objects.h)), which, in turn, inherits from `TaggedImpl`.

## TaggedImpl

`TaggedImpl<StorageType>` manages a pointer of type `StorageType`:

```cpp
enum class HeapObjectReferenceType {
    STRONG,
    WEAK
}
template <HeapObjectReferenceType kRefType, typename StorageType>
class TaggedImpl {
    private:
        StorageType ptr_;
}
```

`TaggedImpl` provides a bunch of predicates, checking various aspects of the underlying object. Some examples include:

+ `IsHeapObject()`
+ `IsStrongOrWeak()`: True if strong/weak pointer to HeapObject
+ `IsStrong()`: True if strong pointer to HeapObject

Also contains some functions to obtain the underlying HeapObject, if one such is present.

## Object

`Object` implements `TaggedImpl` for STRONG HeapObject references. However, an `Object` is not necessarily stored on the heap. The exception being `Smi` (small Integer), where the pointer is used to store a 31 bit singed integer.

```cpp
class Object : public TaggedImpl<HeapObjectReferenceType::STRONG, Address> {
    // No new data members
}
```

`Object` also contains `is##Type()` functions for each of its subtypes, as explained [here]({{<relref "malignant_macro_mass.md#macro_getters" >}}).

## MaybeObject

