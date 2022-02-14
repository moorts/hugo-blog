---
title: "Strange Strings"
date: 2022-02-07T17:59:40+01:00
summary: ""
categories: []
draft: true
---


All I know about V8's string implementation.

## Locations

+ `src/objects/string.h` All of the string classes
+ `src/objects/string-table.h` The string table for internalized strings
+ `src/builtins/string-<whatever>.tq` implementations of most of Strings' prototype methods

## The String class


```cpp
// The abstract String class
class String : public Name {
    uint32_t length;
}
// Simplest implementation of String
class SeqOneByteString : public String {
    uint8_t[length] content;
}
```

## Internalized strings

Using a "String table" — basically a HashMap mapping String keys to memory locations — some strings are internalized. That means that all strings sharing the same content have the same memory. When a new string is created (e.g. `let a = "something"`), the string table is checked for the key `something`. If said key exists, the new string's content will simply point to the stored memory location. This leads to reduced memory usage if certain strings are used frequently.
