---
author: "Halvo (Human)"
title: "Pre-Requisites (Part II) Initial Design: Code Complete Summations"
date: 2023-12-26
tags:
  - pre-requisites
  - insecure-design
  - owasp-top-10
  - architecture
  - communication-protocols
  - data-design
  - ui-separation
  - error‑logging
  - security
draft: false
summary: |
  A light‑hearted deep‑dive into architectural prerequisites—communication, class skeletons, data design, UI separation, and error/log handling. Think of it as laying a solid blueprint before the code construction crew arrives, because a wobbly foundation makes for a lot of late‑night debugging (and security headaches).
---

## Introduction

Prerequisites are incredibly important to any development project and was the [OWASP Top 10, Number 4, Insecure Design](https://owasp.org/Top10/A04_2021-Insecure_Design/). For the purpose of this document we will talk about it in context of *security implications*.

This ended up being too big of a topic for just one post, so here is part 2. In [Pre Requisets Part 1](/posts/code-complete-summations-pre-requisets-part-1), we looked at why pre-reqs are needed in general and how they apply to types of projects. In part 2 we'll look at architectural pre-reqs.

## Planning Comes First

As the saying goes a failure to plan is a plan to fail. Without a solid foundation, similar to building a house, the entire program can fall. With no plan in place, code can end up being added in a half-hazard way, causing code paths to become unknown or unintentionally created. These code paths then become more difficult to maintain.

Another saying that can apply is an ounce of preparation is worth a pound of cure. This can become even more poignant in software development, from a security perspective. From just a development perspective, this could mean saving time later trying to rewrite code that no longer fits or trying to shoehorn in code that doesn't quite fit. For security this can become a major issue if large security flaws are found in the software.

## Why Architectural Prerequisites

General pre-reqs need to be generic enough that the customer and users will be able to understand what is required. Architectural requirements are for the developers themselves. This is hugely important as it keeps the code base consistent and easy to maintain. From a security perspective this is vital as the less chaos in the code the less mistakes there will be. Also, by making it more maintainable, if bugs or security flaws are found, it will require less time and effort to fix.

By having a solid architectural foundation, this will allow the developers to break up work where appropriate. See [Data Design](#data-design) for more detail.

## Architectural Features

Architectural designs can be broken down into multiple pieces, each with their own considerations.

### Communication

How will this software communicate between components in the project and external to the project, both protocol and data structure. 

Protocols are vital here as they will help determine how secure communications between programs or across networks will be. You'll want to pick something that either has encryption by default or can be easily added. Authentication is also a must. Some protocols or services have built in authentication methods, while others will need to be worked into the initial connection. These things need to be thought about ahead of time before diving in.

The data structure is critical to have coordinated between each piece involved. See [section](#data-design) for more detail.

### Major Classes

Creating a skeleton of all the major classes will go a long way to ensuring good design, which in turn helps with keeping the project secure. By creating the skeleton it becomes more obvious what is missing and where each component will live. By having an experienced engineer design and build the skeleton, it also becomes easier to have junior devs take over the actual implementation.

### Data Design

The way the data is designed can have a major impact on security. There are different types of data to consider when designing a secure system. Any data that is considered sensitive, such as PII, should be encrypted both at rest and in transit. Any data that should not be able to be altered by a user should probably also be encrypted both at rest and in transit.

Data that can/should be readable or editable by the user does not need to be encrypted at rest.

All data should be encrypted in transit to reduce the possibility of man-in-the-middle reading or altering the content.

The data design needs to also be agreed upon by all parties using the data. If the design and restrictions are being used differently on both ends, this will cause read/processing errors if both expect different things.

### User Interface

The UI needs to be considered a separate component that uses an API to communicate with the backend. This moduler approach will allow flexibility, as well as naturally leads to security in depth. If the UI is treated separate, then any user input should be sanitized at the UI side. Since it's considered a separate component, then all user input should be sanitized on the backend as well.

### Error Processing and Logging

Here is a big one. Error handling needs to be designed from the beginning. If errors are handled through exceptions, integer returns, parameters passed by reference, etc, this will lead to confusion in the code base and errors will be missed. There should be one design for errors used universally across the project, so all developers know how to handle the errors.

As for logging, this needs to be taken into account for two big reasons. The first is to have the appropriate amount to diagnose and fix errors. The second is how much data and what data will be displayed, as this could lead to unintentionally leaking secure information.

## Conclusion

Having a good design from the beginning can help prevent problems before they even arise. Then if bugs and security issues are found, having a good architecture will help to locate those issues faster.
