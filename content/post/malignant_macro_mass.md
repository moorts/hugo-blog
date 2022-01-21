---
title: "Malignant Mass of Macros"
date: 2022-01-19T16:31:19+01:00
summary: ""
categories: []
draft: true
---

The V8 JavaScript-Engine, like many C++ projects, makes heavy use of macros. Many of these macros are extremely confusing to me. The following documents the process of deciphering these macros.

## DECL_GETTER {#macro_getters}

`DECL_GETTER` is presumably a shorthand for *declare getter*. It expands into the following:

```cpp
#define DECL_GETTER(name, type) \
  inline type name() const;     \
  inline type name(PtrComprCageBase cage_base) const;
```
This declares two overloads of the function `name`, both returning `type`, one without parameters and one that takes a `PtrComprCageBase`. `PtrComprCageBase`, as far as I can tell, is some base pointer. Although, when pointer compression is disabled, it is an empty class with three constructors (see [here](https://github.com/nodejs/node/blob/d8f6b383f689babda74ab4058104da2fc0638d04/deps/v8/src/common/globals.h#L1822)). I have absolutely no idea what thats about. Anyways, the complementary macro to `DECL_GETTER` is `DEF_GETTER`, which is kinda weird:

```cpp
#define DEF_GETTER(holder, name, type)                       \
  type holder::name() const {                                \
    PtrComprCageBase cage_base = GetPtrComprCageBase(*this); \
    return holder::name(cage_base);                          \
  }                                                          \
  type holder::name(PtrComprCageBase cage_base) const
```

The getter without arguments is defined as obtaining the `PtrComprCageBase` and calling the other getter with it. When using the macro, it is followed by a scope, in which the second getter is implemented. An example usage for defining a getter for the String class. The resulting function asserts if the String is in one byte representation.

```cpp
DEF_GETTER(String, IsOneByteRepresentation, bool) {
  uint32_t type = map(cage_base).instance_type();
  return (type & kStringEncodingMask) == kOneByteStringTag;
}
```

## TQ_OBJECT_CONSTRUCTORS

The two complementary macros `TQ_OBJECT_CONSTRUCTORS` and `TQ_OBJECT_CONSTRUCTORS_IMPL` define and implement constructors for Torque-generated classes.

```cpp
#define TQ_OBJECT_CONSTRUCTORS(Type)               \
 public:                                           \
  constexpr Type() = default;                      \
                                                   \
 protected:                                        \
  template <typename TFieldType, int kFieldOffset> \
  friend class TaggedField;                        \
                                                   \
  inline explicit Type(Address ptr);               \
  friend class TorqueGenerated##Type<Type, Super>;

#define TQ_OBJECT_CONSTRUCTORS_IMPL(Type) \
  inline Type::Type(Address ptr)          \
      : TorqueGenerated##Type<Type, Type::Super>(ptr) {}
```

They specify a default constructor with no arguments, as well as a constructor taking an `Address` as argument. `Address` is simply an alias for `uintptr_t`. Both constructors have empty bodies.

## IS_TYPE_FUNCTION_(DECL/DEF)

This macro behaves similarly to `(DECL/DEF)_GETTER`, in that it declares two functions, one without arguments, one with a `PtrComprCageBase`. The purpose is to make sure upcasting an object does not lose it's type information. The macro is used, to generate `is##Type` methods for every existing object.

```cpp
#define IS_TYPE_FUNCTION_DECL(Type) \
  V8_INLINE bool Is##Type() const;  \
  V8_INLINE bool Is##Type(PtrComprCageBase cage_base) const;

#define IS_TYPE_FUNCTION_DEF(type_)                                        \
  bool Object::Is##type_() const {                                         \
    return IsHeapObject() && HeapObject::cast(*this).Is##type_();          \
  }                                                                        \
  bool Object::Is##type_(PtrComprCageBase cage_base) const {               \
    return IsHeapObject() && HeapObject::cast(*this).Is##type_(cage_base); \
  }
```

The actual functions first check if the Object is a HeapObject, and if it is, cast it down to a HeapObject and forward the check there. Therefore, this relies on `HeapObject` implementing the same methods. And sure enough, in the definition of `HeapObject`, the same `DECL` macro exists, and is used in the same way. The definiton-macro however, is not present.

For some heap objects (like the different Strings) there are implementations of the `IsType` methods (using the `DEF_GETTER` macro) in *src/objects/objects-inl.h*, for others (like Name) there are not.

For even other types (the *Oddball* types) there is a different version of the macro:

```cpp
#define IS_TYPE_FUNCTION_DEF(Type, Value)                        \
  bool Object::Is##Type(Isolate* isolate) const {                \
    return Is##Type(ReadOnlyRoots(isolate));                     \
  }                                                              \
  bool Object::Is##Type(LocalIsolate* isolate) const {           \
    return Is##Type(ReadOnlyRoots(isolate));                     \
  }                                                              \
  bool Object::Is##Type(ReadOnlyRoots roots) const {             \
    return *this == roots.Value();                               \
  }                                                              \
  bool Object::Is##Type() const {                                \
    return IsHeapObject() && HeapObject::cast(*this).Is##Type(); \
  }                                                              \
  bool HeapObject::Is##Type(Isolate* isolate) const {            \
    return Object::Is##Type(isolate);                            \
  }                                                              \
  bool HeapObject::Is##Type(LocalIsolate* isolate) const {       \
    return Object::Is##Type(isolate);                            \
  }                                                              \
  bool HeapObject::Is##Type(ReadOnlyRoots roots) const {         \
    return Object::Is##Type(roots);                              \
  }                                                              \
  bool HeapObject::Is##Type() const { return Is##Type(GetReadOnlyRoots()); }
```

 That provides sufficient implementations for the methods automatically.

**TL;DR** I still have no idea where the `IsName` function comes from.
