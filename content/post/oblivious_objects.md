---
title: "Oblivious Objects"
date: 2022-01-20T12:10:05+01:00
summary: ""
categories: []
draft: true
---

**Disclaimer:** The title is addressing my own obliviousness to how Objects work in V8.

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

### The 'Tag' in 'TaggedImpl'

V8 discerns strong and weak pointers. Data pointed to by at least one strong pointer will not be freed by the garbage collector. Wheter or not there are weak pointers referencing the data is irrelevant. V8 uses [pointer tagging](https://en.wikipedia.org/wiki/Tagged_pointer) to differentiate weak from strong pointers and `Smi`s (small integers) from `HeapObjects`. For this, the two LSBs of a pointer are used. If the LSB is 1, the pointer references a heap object, otherwise a small integer. If the next bit is 0, it is a strong pointer, otherwise a weak one.

## Object

`Object` implements `TaggedImpl` for STRONG HeapObject references. However, an `Object` is not necessarily stored on the heap. The exception being `Smi` (small Integer), where the pointer is used to store a 31 bit singed integer.

```cpp
class Object : public TaggedImpl<HeapObjectReferenceType::STRONG, Address> {
    // No new data members
}
```

`Object` also contains `is##Type()` functions for each of its subtypes, as explained [here]({{<relref "malignant_macro_mass.md#macro_getters" >}}).

## MaybeObject

