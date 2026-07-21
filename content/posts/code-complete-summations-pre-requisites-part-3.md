---
title: "Code Complete Summations: Pre-Requisites (Part III) Initial Design"
date: 2024-03-05
summary: |
  A breezy look at the nitty‑gritty of resource and error management—databases, threading, file handles, and error‑handling philosophies. It’s the “don’t forget to tighten the bolts” chapter, reminding us that unmanaged resources and sloppy error handling are the secret doors that attackers love to sneak through.
author: "Halvo (Human)"
tags:
  - code-complete
  - software-design
  - security
  - resource-management
  - summation
slug: code-complete-summations-pre-requisites-part-3-initial-design
draft: false
---

## Introduction

Prerequisites are incredibly important to any development project and was the [OWASP Top 10, Number 4, Insecure Design](https://owasp.org/Top10/A04_2021-Insecure_Design/). For the purpose of this document we will talk about it in context of *security implications*.

This ended up being too big of a topic for just two posts, so here is part 3. In [Pre Requisets Part 1](/posts/code-complete-summations-pre-requisites-part-1-initial-design), we looked at why pre-reqs are needed in general and how they apply to types of projects. In [Pre Requisets Part 2](/posts/code-complete-summations-pre-requisites-part-2-initial-design) we looked at how various pre-reqs for architecture helps with security. In part 3 we'll look at resource and error management pre-reqs.

## Planning Comes First

As the saying goes a failure to plan is a plan to fail. Without a solid foundation, similar to building a house, the entire program can fall. With no plan in place, code can end up being added in a half-hazard way, causing code paths to become unknown or unintentionally created. These code paths then become more difficult to maintain.

Another saying that can apply is an ounce of preparation is worth a pound of cure. This can become even more poignant in software development, from a security perspective. From just a development perspective, this could mean saving time later trying to rewrite code that no longer fits or trying to shoehorn in code that doesn't quite fit. For security this can become a major issue if large security flaws are found in the software.

## Resource Management

Resource management includes not just how much memory or processing power is used, but also data base connections, threading, and file handles. These are vital to security as they are dealing with how data is accessed and processed.

By planning ahead with resource management a lot of the issues can be avoided from the beginning. If there is a failure to take this into account, the code will need to be retrofitted to fix any security issues. This could lead to inconsistent handling of resources or old code being left behind. Planning ahead is the best way to combat these security problems.

### Databases

Databases require a thoughtful setup. Using a secure password is only the start, encrypting the database (particularly file based DB, such as SQLite) should be considered. If this database is remote, having a secure connection is necessary.

In addition, how the data is accessed needs to be considered as well. This includes sanitizing using input, when and how to update data, and read and write sequences. Messing up these could cause leaks or corruption of data.

### Threading

Threading and data access is relevant for corrupted data. The data could be corrupted if read/write sequences are off. If two writes occur at the same time or data is read as a write is happening, the data could be corrupted. Shared variables across threads could also cause security issues sucha as use-after-free, double free, or access data outside of scope.

### File Handles

In a previous post [File IO](/posts/summations-of-secure-coding-in-c-and-cpp-file-io) we discussed in detail why file access is a security issue. By designing out from the beginning how files are going to be accessed will greatly reduce those security issues.

## Error Processing

Being able to handle errors properly is critical for security. There are a few questions that need to be answered that will have impact on how the software is architected:

- Corrective vs Detective: Essentially are you going to try to fix whatever error happens. If so, you need to make sure to handle *all* issues, since  missing one could allow a hole through the program
- Active vs Passive: Active detection could allow for correction, but requires all paths to be checked, whereas passive could cause crashes
- How to propagate errors: Is each method going to check it's own errors or push them up. When do these errors get checked or pushed up?
- What is the convention for handling errors: Output to a log, correct the error, try and return useful data even when there is an error?

These questions shouldn't be taken lightly. Each one should be considered since they could cause security issues. In addition just keeping things consistent is good for security since all developers will know what to expect from another developers code.

## Conclusion

Having a good design from the beginning can help prevent problems before they even arise. Then if bugs and security issues are found, having a good architecture will help to locate those issues faster.
