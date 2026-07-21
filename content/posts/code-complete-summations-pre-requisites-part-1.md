---
title: "Code Complete Summations: Pre-Requisites (Part I) Initial Design"
date: 2023-12-20
summary: |
  A breezy, slightly tongue‑in‑cheek look at why solid planning isn’t just good housekeeping—it’s a frontline defense. We walk through OWASP’s Insecure Design warning, compare personal versus mission‑critical projects, and tease out iterative vs. sequential approaches, all with a sprinkle of humor to keep the security talk from feeling like a lecture.
author: "Halvo (Human)"
tags:
  - pre-requisites
  - insecure-design
  - owasp-top-10
  - security
  - software-design
  - planning
  - requirements
slug: code-complete-summations-pre-requisites-part-1-initial-design
draft: false
---

## Introduction

Prerequisites are incredibly important to any development project and was the [OWASP Top 10, Number 4, Insecure Design](https://owasp.org/Top10/A04_2021-Insecure_Design/). For the purpose of this document we will talk about it in context of *security implications*.

## Planning Comes First

As the saying goes a failure to plan is a plan to fail. Without a solid foundation, similar to building a house, the entire program can fall. With no plan in place, code can end up being added in a half-hazard way, causing code paths to become unknown or unintentionally created. These code paths then become more difficult to maintain.

Another saying that can apply is an ounce of preparation is worth a pound of cure. This can become even more poignant in software development, from a security perspective. From just a development perspective, this could mean saving time later trying to rewrite code that no longer fits or trying to shoehorn in code that doesn't quite fit. For security this can become a major issue if large security flaws are found in the software.

## How Prereqs are Used

### Where is this Software Used

The first thing to determine is where is this software going to be used: personal-local, personal-network, business, mission-critical, embedded. These are all going to involve vastly different life cycles and security footprint.

#### Personal Project

A personal software project is going to have a much smaller security footprint, particularly if it's only going to run locally. Personal projects can also be easily and quickly updated if flaws are found. While an initial plan is still needed, this type of project can be built a little more free form.

#### Business

For business code, software will still be updated, but on longer cycles. As such a good plan and set of requirements is important since any changes will take time to deploy. This is true for security problems as well. Businesses tend to be risk adverse and will not want to update on a regular basis. Be sure to try and set the requirements and plan ahead of time.

#### Mission Critical

Users will be hard pressed to change this if it's working. If it isn't broken, don't touch. As such it will get updated very infrequently. Here requirements and design are critical to make sure everything is developed properly and securely.

#### Embedded

Never will it get updated ... that should be the assumption. These need to have extremely tight requirements set ahead of time with an incredibly secure design. Having a tight plan here would also include using tried and tested external dependencies. The plan should include very granular unit testing as well as integration testing.

### Iterative vs Sequential

#### Iterative (as-you-go)

An initial design is definitely still needed, but the design should be flexible. New requirements will be added all the time.

This can be used for personal projects and some business applications. It's best for applications that will have little or no cost to changing requirements later. This will work best if all the requirements are not known up front, or if there is an expectation of changing requirements.

A good business example of this is a security application that monitors a system of security problems. An initial design can be made for communication and how detection will be done, but what will be detected will change overtime.

#### Sequential

All requirements and design are complete before coding is done. This is a must for mission critical and embedded software projects. This approach is needed when changing things in the future is difficult or will cost a lot.

For this method and these types of projects the requirements need to be stable, the design should be straight forward, and future requirements will be predictable.

Additionally, in my opinion, these should be either small or a series of small projects. Smaller projects tend to be easier to audit and see code paths. This will reduce the possibility for security issues.

## Defining the Problem

The first and most important is defining what is trying to be solved. Everything will stem from this, so create as narrow/specific problem as possible.

The problem should also be easily understandable. Not only should everyone on the development team understand the problem statement, but the customer and users should also understand it. Without a clear problem, requirements will be difficult to define and may include things outside the scope of the project.

## Defining the Requirements

Having official requirements helps the user drive development rather than the programmer. This way the project will actually be useful to those using it. 

### Evaluate the Requirements

STOP and make sure all requirements make sense and are specific enough. If anything doesn't make sense or is too vague, bring those concerns to the customer and have them get more specific. A good design can't happen without good requirements.

### Prep for Change

Using a strong problem statement, create an initial design that can handle some changes. The only thing that stays the same is that everything changes. Having a flexible design can help with those changes. An example could be having different parts of the code run independently, but have a strong stable design for communication between the parts.

### Change Control

Customers are going to want more, have a procedure in place to handle those requests. By having a formal request process, this can help filter vague or bad requests before hitting the developers.

With a strong problem statement, there can be a way to push back against requests as well. Any request that goes outside the scope of the problem statement does not get put into the current project.

## Conclusion

The initial problem statement and set of requirements can make or break a project's security. By having a narrowly defined problem and set of requirements, it's much easier to design a system. With a robust design, security can be included from the beginning.
