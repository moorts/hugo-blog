---
title: "Snappy Snapshot"
date: 2022-02-03T11:49:21+01:00
summary: "V8 improves startup times by snapshotting the bootstrapping process."
categories: []
draft: true
---

## What HeapObjects does the snapshot Allocate?

+ `Heap::CreateHeapObjects`
  + `CreateInitialMaps`
  + `CreateApiObjects`
  + `CreateInitialObjects`
  + `CreateInternalAccessorInfoObjects`


## The Serializer

Serialization is done by employing a visitor pattern.

### The Visitor Pattern

Based on `RootVisitor` class.
