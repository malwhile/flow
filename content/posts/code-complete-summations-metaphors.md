---
title: "Code Complete Summations: Metaphors"
date: 2023-11-13
summary: |
  A light‑hearted look at Steve McConnell’s “Code Complete” metaphors—Penmanship, Farming, and Oyster Farming—and how each style can make your code more or less secure. Think of Penmanship as scribbling a quick note (great for tiny scripts, terrible for big projects), Farming as planting seeds with a bit of watering (testing each piece, but still lacking a master plan), and Oyster Farming as building a sturdy oyster bed (design first, then grow securely). Spoiler: the oyster wins the security contest.
author: "Halvo (Human)"
tags:
  - code-complete
  - software-design
  - security
  - summation
slug: code-complete-summations-metaphors
draft: false
---

## Introduction

This is the first entry in a new set of summations. Previously we looked at "Secure Coding in C and C++", this current set of summations are going to go over "Code Complete 2" by Steve McConnell. These summations will have a focus on security.

"Code Complete" uses a set of "metaphors" for describing software development styles. We will look at Penmanship, Farming, and Oyster Farming. We'll look at these and how they could affect the security of the final product.

## Introduction Part II: Summarizing the Metaphors

### Penmanship: Writing Code

Writing a program is like writing a letter. Just sit down, start programming, start to finish.

### Farming: Growing Code

Similar to planting one seed at a time, design and develop one piece at a time. Test each piece before moving on.

### Oyster Farming: Code Accretion

Code accretion is the slow growth of the whole system. Start with designing the system as a whole, create empty objects and methods, then start adding content and tests.

## Security Implications

### Penmanship: Writing Code

Writing code start to finish, like writing a letter, works great for a small program that only needs to complete one task. However, this quickly doesn't make sense as the complexity of the program grows. When the complexity grows, it's easy to miss pieces or have the need for re-writes as more complexity is added. Testing is also not built into this method.

Two security concerns are immediately apparent with this method: testing, intra code communication. With no testing built in, it becomes much more difficult to find problems early. Without finding the problems early, they can become hidden by the complexity and harder to find. Also without an overall design, it becomes much harder to have the different pieces of code communicate with each other. Without the coherent design, more bugs could be introduced through the interaction. Parameter limits and return values could get messed up and any changes can cause cascading issues.

### Farming: Growing Code

Here we have testing built in, as each piece is not finished until testing is complete. This will alleviate bugs (or as much as possible) in individual parts of the code. However the issue of overall code complexity is still a problem. Interaction between pieces isn't thoroughly tested and any changes can cause cascading issues.

#### Oyster Farming: Code Accretion

This metaphor provides the best option for creating secure large scale projects. Starting with the overall design can quickly show how each piece needs to interact, come up with (mostly) stable interfaces into each part, and reveal overall problems that might occur. By writing a skeleton of the code, any piece can be worked on, since it isn't fully reliant on the other parts being complete.

## Conclusion

These metaphors can help show the way code being built can affect the final product. They can each have their own security implications, by attempting to stop bugs before they form.

Opinion time: Farming/Growing Code seems kinda pointless. It's a good way to show that testing is needed, but without an overall design, it's effectively Penmanship with testing. Penmanship is worthwhile when writing small scripts for personal work and Oyster Farming is the best for anything more complex. Code Accretion allows for a coherent design and testing reducing bugs and thus reducing security holes.


