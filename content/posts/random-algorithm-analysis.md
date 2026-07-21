---
title: "Random Algorithm Analysis"
date: 2020-04-17
summary: |
  A playful replay of Zalewski’s “Silence on the Wire” experiment: plotting 3‑D scattergrams of various RNGs (Python, shuf, urandom, online services) to see if any have secretly upgraded their magic. Spoiler: they all look suspiciously alike.
author: "Halvo (Human)"
tags:
  - cryptography
  - prng
  - analysis
  - research-paper
slug: random-algorithm-analysis
draft: false
---

## Introduction

After reading through "Silence on the Wire" by Michal Zalewski for the 8th time, I decided I wanted to try the random algorithm analysis he did in Chapter 10. He looked at the relationship between sequential numbers by graphing them in a 3D scatter plot. My idea was to see if any of the algorithms had been updated to make them more secure.

There was a problem with that however. I only own one computer and it's too low power to run VMs. So I was stuck with the python algorithm, shuf, urandom, and two online random number generators. This was a big limitation and I hope to update this whenever I get a new computer.

## The Importance

Random algorithms cannot be predictable for security reasons. All encryption algorithms use random digits to generate keys. If the keys are predictable, than encryption can be broken. In "Silence on the Wire" it showed some random algorithms having limited range or predictable patterns to reduce the search space. Luckily the new algorithms seem to be doing better.

## The Math

Using the math in "Silence on the Wire" to create the graphs allows me to compare more directly to Mr Zalewski's. Of course this ended up not really mattering, since I was so limited. For a better explanation see the book Chapter 10, but here is a quick run down. Using data samples S0, S1, S2, being a randomly generated sequence, then calculate the deltas and graph those.

    D2 = S2 - S1
    D3 = S3 - S2
    .
    .
    .
    DN = DN - DN-1

Then we graph the deltas in a 3D scatter plot using the following points:

    P2 = (D4, D5, D6)
    P3 = (D7, D8, D9)
    .
    .
    .

## The Samples

The data came from the following locations; JS Math, Python's numby package, random.org, Bash shuf, and urandom. Here are the graphs that were produced ... don't get excited, they are all basically the same:

Unfortunetally my blog server crashed, so I've lost the images for now, I'll add them in later. The long and short is they all look basically the same.

## Conclusion

Why are these all basically the same ... probably because they all use the same exact algorithm. I was hoping Python had it's own built in PRNG, but it appears to use whatever the host uses. The shuf command and urandom make sense that they are the same. Shuf is kinda just a wrapper around urandom to give the user more control.

I was also hoping that these couple of web sites would use their own, but it appears they just run on top of Linux servers using urandom.

The positive to this is it hopefully means more eyes on the random algorithm, making sure they are unpredictable. The problem is if a flaw is found in urandom, it will affect almost everything.

If anyone wants the code just message me ... it's not complicated.

