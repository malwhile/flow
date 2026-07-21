---
title: "Summations of Secure Coding in C and C++: Always null Terminate (Part 2)"
date: 2022-08-13
summary: |
  The sequel to the null‑termination saga, now tackling `strcat`, `strncat`, `strlcat`, and friends—plus a quick table to keep your concatenations from turning into catastrophes.
author: "Halvo (Human)"
tags:
  - secure-coding
  - c-cpp
  - string-analysis
  - security
  - summation
slug: summations-of-secure-coding-in-c-and-cpp-always-null-terminate-part-2
draft: false
---

## Introduction

Series on summarizing themes in "Secure Coding in C and C++" by Robert C. Seacord, part 2. Find part 1 here [Always null Terminate (Part 1)](/posts/secure-coding-in-c-summations-null-terminate). We are currently going through this book in our work book club and there are a lot of good themes that seem to be threaded through the book. These are my notes, thoughts, and summaries on some of what I've read and our book club have discussed.

This is written for an audience that has a broad overview of security concepts. Not much time is spent explaining each concept, and I encourage everyone to read the book.

The first theme to discuss is always `null` terminating `char *` or `char array` buffers (unless you have a *very* specific reason for not). This is very important to help prevent buffer overflows, reading arbitrary memory, accessing 'inaccessible' memory. This is part 2 where we will discuss string cat and length. For a brief discussion on string copy see [part 1](posts/secure-coding-in-c-summations-null-terminate.md).

## Functions Needing null

One of the important reasons to `null` terminate is there are several very common functions that require `null` termination. Even some that you wouldn't necessarily think of. Without having `null` at the end of the buffer, it creates a situation where things could go wrong.

### String Cat

The next set of functions to look at are concatenating strings. These not only need to be `null` terminated, but they also need to be properly allocated. If they are not a concatenation could overwrite `null` terminators, and the resulting string could cause errors further in the code. Memory allocation will be discussed further in another post. First I'm going to throw a table at you, it gives a summary of string concat functions and how they handle some of the issues. We will discuss further after the table.

|| Buffer Overflow Protection | Guarantees Null Termination | May Truncate String | Allocates Dynamic Memory | 
| --- | --- | --- | --- | --- |
| strcat() | No | No | No | No |
| strncat() | Yes | No | Yes | No |
| strlcat() | Yes | Yes | Yes | No |
| strcat\_s() | Yes | Yes | No | No |

Lets go over each function:

#### strcat

```c
char *strcat(char *dest, char *src)
```

This function is basic and needs careful programming. The destination **must** be at least the total length of both strings plus the `null` terminator. If it is smaller, it **will** overflow. It's also best to null the memory ahead of time, guaranteeing the last character is `null`. Proper memory allocation will be in a future post.

`strcat` copies the source until it hits the first `null` character, into destination, starting at the first `null` character. This means there are two things to watch out for:

1. This could lead to reading arbitrary memory
1. Binary buffers may be corrupted since they can contain `null` characters within the string (use `memcpy` instead)

Arbitrary memory reads can be a problem since it could mean revealing data meant to be secret. Depending on where memory is allocated, sensitive data could be revealed to the user.

Be sure to set the last character to `null` after the `strcat` is completed.

#### strncat

```c
strncat(char *dest, char *src, size_t src_len)
```

`strncat` attempts to solve some of the issues with `strcat` but still requires careful programming. For one the `src` does not need to be `null` terminated as long as it is *at least as long as `src_len`*. `strncat` will copy `src_len` characters into the `dest`, or until it hits a `null` byte. In this case `dest` needs to be **at least** as long as the original string plus `src_len`. If it is not, it can still lead to buffer overflows.

In addition if `src` is not `null` terminated and `src_len` is longer than the length of `src` then `strncat` will still copy arbitrary memory.

`strncat` helps the developer watch for these issues but doesn't actually solve them.


#### strlcat

```c
size_t strlcat(char *dst, const char *src, size_t size)
```

`strlcat` is pretty much identical to `strncat` so it has many of the same issues. Since `size` is the size of the destination, it is an improvement for two reasons:

1. Destination is guaranteed to be `null` terminated (so long as `size` is one less than the total length of the destination).
1. It returns the attempted length copied (so the length of source).

Point one is great so you don't need to worry as much about pre setting the memory of the destination, or setting the last byte after the copy.

Point two is good so you can compare `size` to the return value to see if the source was truncated.

### Sensing a Theme

There are two themes for string concatenating, one is **`null` terminate all character buffers**, the second is proper memory allocation. This will be discussed in a future post.

Every one of these functions require the source and destination to be `null` terminated. If they are not, or if there is a `null` in the middle, it will cause issues!

## Conclusion

`null` termination is important so that we don't accidentally read or write to arbitrary memory. This concludes the discussion on `null` termination, the next post will cover proper memory allocation.

