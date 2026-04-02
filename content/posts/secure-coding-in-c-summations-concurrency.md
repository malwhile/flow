---
author: "Halvo (Human)"
title: "Concurrency: Summations of Secure Coding in C and C++"
date: 2023-01-27
tags:
  - concurrency
  - mutex
  - threading
  - c
  - cpp
  - secure-coding
draft: false
summary: |
  A light‑hearted rant about why a plain‑old `mutex` is the hero of secure C/C++ concurrency, why `goto` is still secretly useful, and how to keep your locks short and your bugs shorter.
---

## Introduction

Continuing summarizing the themes in "Secure Coding in C and C++" by Robert C. Seacord, we will discuss concurrency. When code runs at the same time needing access to the same resources lots of issues can occur. These can be from the annoying of getting the incorrect data, halting deadlocks, to vulnerabilities.

The tl;dr; use `mutex`'s. There are a lot of methods for controlling concurrency, but many use `mutex`'s in the background anyway. A `mutex` is the closest thing to guaranteed sequential access, without risking deadlocks.

### Importance 

To quote Robert C. Seacord, "There is increasing evidence that the era of steadily improving single CPU performance is over. ... Consequently, single-threaded applications performance has largely stalled as additional cores provide little to no advantage for such applications"

In other words, the only real way to improve performance is through multi-threaded/multi-process applications, thus being able to handle concurrence is very important.

## The Big Issue

Race Conditions! That's the big issue, when two or more threads or processes attempt to access the same memory or files. The issue comes in when; two writes happen concurrently, reads occur before writes, reads occur during writes. This can lead to incorrect values being read, incorrect values being set, or corrupted memory. These types of flaws, and insufficient fixes can cause vulnerabilities in the programs as well.

## How Do We Keep Memory Access Sane

So what is the fix. There are several possible ways to keep things in sync, but the number one way that will "always" work is a `mutex`. In fact most of the other "solutions" are just an abstracted `mutex`. We will go briefly over a couple solutions: global variables, `mutex`, and atomic operations. 

### Shared/Global Variables

A simple solution, that is **NOT** robust is simply having a shared "lock" variable. A variable, we'll call `int lock`, which is a `1` when locked and `0` when unlocked, is accessible between threads. When a thread wants to access a memory location it simply checks that the variable is in the unlocked state, `0`, locks it by setting it to `1`, then accessing the memory location. At the end of it's access, it simply sets the variable back to `0` to "unlock" the memory location.

Simple, but not robust. It suffers from three main flaws that a `mutex` solves. The first is a second thread could lock the memory location after the first thread checks that it's unlocked. Thread 1 `t1` checks the value of `lock` and sees that it's `0`, thread 2 `t2` then locks `lock`. Both `t1` and `t2` think they both hold the lock and both access the memory at the same time.

The second problem is, in order to implement this, any given thread would have to enter a `loop` `sleep` cycle to keep checking the lock value. This could use valuable CPU time.

The third issue is compiler optimization (future blog coming regarding that hot mess). When a compiler is optimizing a loop, it may only read a given variable once, if there is no indication that it will change. Since it's being changed in another thread in another part of the program, the compiler may cause the variable to never change, thus causing a *deadlock*. The other thing compilers like to do to optimize things, is rearrange the order of operations if it thinks it doesn't matter. This can lead to other forms of *read before writes* or *deadlocks*.

The third issue *can* be solved through compiler directives, but that still doesn't solve the first two issues.

### `mutex`

Fundamentally, a `mutex` isn't much different than a shared variable. The `mutex` itself is shared among all threads. The biggest difference is, it doesn't suffer from either of the three issues. The `threading` library handles things properly such that a "check" on the `mutex` and a "lock" happen atomically (meaning that nothing can happen in between). This handles the issue of reading the variable before another thread writes and the compiler trying to optimize things. `mutex`es also handle waiting a little different thus need less CPU to wait.

The only drawback to the `mutex` is that it can still cause a *deadlock* when not used properly. If a `mutex` isn't properly unlocked (either due to programmer error, or improper error handling) then the `mutex` might not be released, thus locking up other threads. It can also lock other threads if it keeps it open for a "long time" even if it will eventually close the `mutex`.

To solve the possible *deadlock* of not unlocking the `mutex`, `automic` operations were added.

### Atomic Operations

Atomic operations attempt to solve the issue of forgetting to unlock the `mutex`. An atomic operation is a single function call that perform multiple actions on a single shared variable. These operations can be checking and setting (thus making them semi useful as a shared locking variable), swapping values, or writing values.

Atomic operations are very limited in their use case, since there is only so many built in methods. If they work for your use case there really isn't much down side to using them. However since they are limited and use a `mutex` in the background anyway, a `mutex` with proper error handling and releasing is probably the best way to go.

### Other Solutions

Lock Guard:
- C++ object that handles a `mutex`, useful for not having to worry about unlocking the `mutex`, only real downside is it's C++ only

Fences:
- An attempt to tell the compiler not to re-arrange operations, can still lead to data races. Even if the compiler doesn't optimize things, just how and when the operations get scheduled on the CPU can mess with memory operations

Semaphore:
- `mutex` with a counter. Can have good specific use cases, but just uses a `mutex` in the background. Unless needed, just use a `mutex`

## Obvious bias is Obvious

Just use a `mutex`. Most of the additional solutions either are simply a `mutex` in the background or cause other issues. A `mutex` will just work. Just be sure to properly unlock when done and possibly have timeouts in case another thread gets stuck.

With a `mutex` you have way more control over the code and way more flexibility in how it's used. An arbitrary amount of code can be put in between without having to finagle a use case into a limited number of function calls.

## Keep it Sane

There is one additional tip for concurrency, lock as little code as possible. By having as few operations as possible between a `mutex` lock and unlock it reduces possibilities of timeouts, gridlocks, and crashing. It also helps to reduce the possibility of forgetting to unlock. Do not surround an entire method (or methods) with locks, rather just the read and write operations.

### The Dreaded `GOTO`

When it comes to locking, `goto` is your friend. Have an error or exception inside a lock, `goto` the unlock. This also works for clearing memory, have an error `goto` the `free` and memory cleanup. Keep the `goto` sane by only jumping to within the current method.

## Conclusion

Just use a `mutex`, everything else is either more prone to errors, more limiting, or just uses a `mutex` in the background anyway. Keep things sane by locking as little code as possible. And always make sure to throw locks around accessing common memory space

