---
title: "Summations of Secure Coding in C and C++: Always null Terminate"
date: 2021-09-01
summary: |
  A light‑hearted reminder that every character buffer deserves a `'\0'`—otherwise you’re inviting buffer overflows, stray reads, and a lot of debugging grief.
author: "Halvo (Human)"
tags:
  - secure-coding
  - c-cpp
  - string-analysis
  - security
  - summation
slug: summations-of-secure-coding-in-c-and-cpp-always-null-terminate-part-1
draft: false
---

## Introduction

Welcome to the next series, summarizing themes in "Secure Coding in C and C++" by Robert C. Seacord. We are currently going through this book in our work book club and there are a lot of good themes that seem to be threaded through the book. These are my notes, thoughts, and summaries on some of what I've read and our book club have discussed.

This is written for an audience that has a broad overview of security concepts. Not much time is spent explaining each concept, and I encourage everyone to read the book.

The first theme to discuss is always `null` terminating `char *` or `char array` buffers (unless you have a *very* specific reason for not). This is very important to help prevent buffer overflows, reading arbitrary memory, accessing 'inaccessible' memory.

This was just Part 1, find [Always null Terminate (Part 2)](/posts/summations-of-secure-coding-in-c-and-cpp-always-null-terminate-part-2)

## Functions Needing null

One of the important reasons to `null` terminate is there are several very common functions that require `null` termination. Even some that you wouldn't necessarily think of. Without having `null` at the end of the buffer, it creates a situation where things could go wrong.

### String Copy

The first set of functions to look at are copying strings. These not only need to be `null` terminated, but they also need to be properly allocated. Memory allocation will be discussed further in another post. First I'm going to throw a table at you, it gives a summary of string copy functions and how they handle some of the issues. We will discuss further after the table.

|| Buffer Overflow Protection | Guarantees Null Termination | May Truncate String | Allocates Dynamic Memory |
| --- | --- | --- | --- | --- |
| strcpy() | No | No | No | No |
| strncpy() | Yes | No | Yes | No |
| strlcpy() | Yes | Yes | Yes | No |
| strdup() | Yes | Yes | No | Yes |

Lets go over each function:

#### strcpy

```c
strcpy(char *dest, char *src)
```

This function is super basic and needs a lot of careful programming. The destination **must** be at least the length of the source plus the `null` terminator. If it is smaller, it **will** overflow. If destination is the correct size, but not initialized, destination is not guaranteed to be `null` terminated. Proper memory allocation will be in a future post.

`strcpy` also copies the source until it hits the first `null` character. This means there are two things to watch out for:

1. This could lead to reading arbitrary memory
1. Binary buffers may not copy the entire thing, since there could be null bytes inside of the buffer

Arbitrary memory reads can be a problem since it could mean revealing data meant to be secret. Depending on where memory is allocated, sensitive data could be revealed to the user.

#### strncpy

```c
strncpy(char *dest, char *src, size_t dest_len)
```

This supposedly solves some of the issues with `strcpy`, but it doesn't really. `strncpy` **is not considered a secure alternative**. Careful coding is still very necessary.

The problem with `strncpy` is it still doesn't verify anything. It still reads source until `null`, so the two issues above still apply. It also doesn't guarantee `null` termination in destination, just like `strcpy`.

The only thing it does is *helps* with buffer overflows. However, if the `dest_len` is larger than `dest`, it will still buffer overflow.

So `strncpy` can still read arbitrary memory and can still buffer overflow (tho overflows are more difficult).

#### strlcpy

```c
size_t strlcpy(char *dst, const char *src, size_t size)
```

`strlcpy` is pretty much identical to `strncpy` so it has many of the same issues. Since `size` is the size of the destination, it is an improvement for two reasons:

1. Destination is guaranteed to be `null` terminated (so long as `size` is one less than the total length of the destination).
1. It returns the attempted length copied (so the length of source).

Point one is great so you don't need to worry as much about pre setting the memory of the destination, or setting the last byte after the copy.

Point two is good so you can compare `size` to the return value to see if the source was truncated.

#### strdup

```c
char *strdup(const char *s);
```

`strdup` is super basic, but because of that it's probably the hardest to mess up. It dynamically allocates the correct amount of memory and performs the copy, putting a `null` terminator at the end.

The only thing to note is that it reads until the `null` terminator.

One important thing to note, the returned value must be `free`'d

### Sensing a Theme

See the theme yet ... **`null` terminate all character buffers**

Every one of these functions require the source to be `null` terminated. If they are not, or if there is a `null` in the middle, it will cause issues!

## Conclusion

`null` terminating is very important to prevent accessing or writing to memory locations that should not be accessed. In this post we discussed copying strings. In the next post, we will continue this theme with concatenating strings.
