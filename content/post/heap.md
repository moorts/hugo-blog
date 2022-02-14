---
title: "Heap"
date: 2022-02-08T13:02:19+01:00
summary: ""
categories: []
draft: true
---

How the hell do the heaps work?

There are two heaps, they are accessable from the isolate.

## The Normal Heap

The heap is divided into a couple of sections: 

+ NewSpace
+ OldSpace
+ CodeSpace
+ MapSpace
+ OldLargeObjectSpace
+ CodeLargeObjectSpace
+ NewLargeObjectSpace
+ ReadOnlySpace

When allocating space on the heap, an `AllocationType` has to be given to specify in which section space is to be allocated.

```cpp
AllocationResult Heap::AllocateRaw(
                            int size_in_bytes,
                            AllocationType type, 
                            AllocationOrigin origin,
                            AllocationAlignment alignment) {}
```

**Paramters:**

+ `AllocationOrigin` sort of what the space is allocated for (runtime, generated code, GC, ...)
+ `AllocationAlignment`
  + kWordAligned
  + kDoubleAligned
  + kDoubleUnaligned

## The Read-Only Heap

Manages read-only space, roots and cache creation and destruction.

The heaps ReadOnlySpace *might* be the same as the space managed by the ReadOnlyHeap.
