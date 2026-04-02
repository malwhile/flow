---
author: "Halvo (Human)"
title: "Pseudo Random Number generators"
date: 2024-03-22
tags:
  - prng
  - randomness
  - cryptography
  - entropy
  - hardware
  - security
draft: false
summary: |
  A light‑hearted tour of the quirky ways we coax randomness out of lava‑lamps, Geiger counters, ambient noise, and good‑old motherboard sensors, because good cryptography needs a little chaos (and a lot of fun).
---

## Introduction

Pseudo-random number generators (PRNGs) play a crucial role in modern cryptography and information security. These algorithms generate seemingly random sequences of numbers, which are essential for tasks like encryption, secure key generation, and digital signatures. PRNGs in the past have had many issues with predictability. Looking at the current and future research requires a look at how predictable the numbers really are.

## External Techniques

Several techniques have arisen to generate random numbers, both on local machines and using real world chaos. There are a few ways to integrate physical phenomenon in the real world to generate random numbers. 

### Lava-lamps

[Lavarland](https://en.wikipedia.org/wiki/Lavarand) uses a video of a wall of lavalamps to generate random numbers. It does show by taking a high definition screenshot of the video feed. It then hashes that image to generate a seed for a PRNG. The more random the seeds the more random the number that will be generated. Since the lavalamps, particularly accumulated over all lamps, is unpredictable, the seed is also unpredictable.

### Radioactive Decay

Using Geiger Meters to detect background decay of radioactive material allows the generation of random seeds as well. As far as we currently know, radioactive decay has no distinct pattern and thus, unpredictable. Using this to generate seeds for PRNGs will generate random numbers.

### Background Sound

Another physical phenomenon that is difficult to predict is background noise. It's almost impossible to predict not just what will be making sound at any given moment, but also the direction, intensity, and frequency of that sound. By hashing background noise a random seed can be generated, making it almost impossible to predict the output of a PRNG.

## Internal Techniques

Not all personal computers have access to these physical phenomenon. If they don't have access to a camera, microphone, network connection, or Geiger counters, there are sensors that most computers have that can be used. Most motherboards and graphics cards have both power meters and temperature sensors. By taking as accurate a measurement as possible on temperature, electrical pull, fan speeds, and time can produce fairly unpredictable values. Some of these values can be correlated (i.e. higher electrical pull, will lead to higher temperatures, which leads to higher fan speed), but should produce numbers that are unpredictable enough. Using all these values together is a good way to generate a seed to use.

Another way is to track user movements. By having a user move around the mouse pointer or type on the keyboard, can help generate a random seed. By tracking pointer position, acceleration, and speed, keyboard keys pressed and speed they are pressed, a fairly random seed can be generated.

These internal techniques do not require permissions to external resources.

## Conclusion

Most PRNGs used simple seeds in the past, usually just time of run. Current new techniques create a more random number by using real world conditions. By using these conditions to generate seeds, it provides a better pseudo random number.

