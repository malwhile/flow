---
author: "Halvo (Human)"
title: "Set to NULL After Free: Summations of Secure Coding in C and C++"
date: 2022-08-17
tags:
  - free-and-null
  - secure-coding
  - c
  - cpp
  - memory-management
  - pointers
  - security
draft: false
summary: |
  A breezy, slightly tongue‑in‑cheek look at why setting pointers to `NULL` right after `free` (and a few related memory‑management niceties) can save you from nasty use‑after‑free bugs, memory leaks, and the occasional midnight debugging panic.
---

## Introduction

Continuing the series of summarizing the themes in "Secure Coding in C and C++" by Robert C. Seacord, we will discuss freeing pointers. The title of this section is specifically about setting to `NULL` after calling free, but this post will cover a lot more than that. Here we will discuss the problems with forgetting to free, functions whose return value needs to be freed, and freeing without allocating/double free.

As for the title of this piece, some of the most common problems can be solved simply by setting pointers to `NULL` at declaration or after freeing.

This is written for an audience that has a broad overview of security concepts. Not much time is spent explaining each concept, and I encourage everyone to read the book.

## Always `free` When Done

First off lets discuss why `free` is important. Without freeing variables, best case scenario is you end up with leaked memory and worst case could introduce vulnerabilities. 

### Memory Leaks

When non-pointer variables are declared, they are restricted to the scope in which they were created. The operating system will clear the memory at the end of the scope. For pointers however, allocated memory is not restricted by scope. So if a pointer is not cleared before the end of scope, that memory will still be held by the process. Depending on how large these allocations are, you could fill memory quite quickly. At best this will lead to crashing your own program (if the OS restricts memory), at worst you will crash the system.

One of the best ways to handle this is with `goto`'s. Yes, despite the hate for `goto` statements, this is a perfect use for them. Set an anchor at the end of the function, and have all clearing code there. Then during any error within the function, jump to the anchor using `goto`. The thing is, the `goto` must be within the same method and later in the logic. A `goto` statement should never leave the method scope and should never more earlier in the function.

Also by using the `goto` and anchor, it will prevent another possible vulnerability, use after free. This is also discussed in the next section.

### Vulnerabilities

The other problem with forgetting to call free is allowing an attacker to gain access to your memory space, which could cause sensitive data to be leaked. By exploiting other vulnerabilities an attacker could gain access to memory that was supposed to be freed. Another problem with forgetting to free is denial of service attacks. An attacker can specifically target the memory leak to overload the system.

Another vulnerability isn't forgetting to free, but forgetting that you did free. Use after free can be a big issue. If an attacker can fill the memory space that was previously freed, when the program uses the pointer again, instead of erroring out the vulnerable program will use the new data. This could result in code execution, depending on how the memory is used.

## Knowing When to `free`

When you as the developer call `calloc`, `malloc` or almost anything else with an `alloc` it's pretty clear that those need to be freed. You declared the pointer and created the memory. But there are other situations that are not as clear, when calling functions that allocate memory.

These functions could either be built in functions like `strdup` or ones which you write yourself. Be sure to check and write documentation to be clear on if the memory needs to be freed. This type of allocation can be very easy to forget and could be an easy place to cause memory leaks. The best way to verify, is double check all declared pointers if they need to be freed before finishing the method.

This is a perfect situation for a `goto` to an anchor at the end of the method. Then there only needs to be a single `free` preventing use after free and double free. It also then requires only a single return. This will prevent returning before freeing, reducing the risk of memory leaks.

## Knowing When NOT to `free`

Knowing when not to free is not as big of an issue as not freeing, but can still cause issues. Double frees, freeing before allocating, and freeing without allocating can cause your program to crash. It doesn't cause additional errors, but can make you vulnerable to denial of service.

## Conclusion

Freeing is vitally important to keeping your programs safe. All allocations need to be freed and it's best to free at the end of the method the pointer was allocated in. This will help prevent use after frees and forgetting to free. An anchor at the end of a method with `goto` is the best way to accomplish this.

