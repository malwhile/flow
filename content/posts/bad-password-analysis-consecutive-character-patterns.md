---
title: "Bad Password Analysis: Consecutive Character Patterns"
date: 2020-09-16
summary: |
  In this delightfully “bad” foray into password cracking, we tally two‑ and three‑character combos from millions of leaked passwords and compare them to a subtitle‑derived English word list. Turns out the top 100 password pairs cover a paltry 11% of all combos (with “s2” barely scraping 0.15%), while the same slice of English captures a whopping 60%. Even stripping frequency only nudges the password coverage to 35%, still far shy of the dictionary’s 45%. The takeaway? Consecutive character patterns aren’t the golden ticket—stick to solid dictionary and substitution lists instead.
author: "Halvo (Human)"
tags:
  - password-analysis
  - string-analysis
  - character-patterns
  - security-research
  - security
  - data-science
  - dictionary-comparison
slug: bad-password-analysis-consecutive-character-patterns
draft: false
---

## Introduction

Continuing from my Bad Malware Analysis, we now take a look at Bad Password Analysis. Mostly this is just for the fun of it, but we'll see if we can learn anything along the way.

In this Bad Malware Analysis post, we'll look at consecutive character frequency. I've done analysis on two and three consecutive characters and compared it to a word frequency list generated from subtitle archives.

## Data

The passwords come from several leaks. These include honeynet, myspace, rockyou, hotmail, phpbb, and tuscl lists. All of these lists contain the count of how many times a password was used as well. Total there are 14,584,438 unique passwords.

For comparison, I'm using an English word frequency list generated from subtitles. There are 1,656,996 unique words.

I wrote a quick script to combine these into a single text file, to remove all duplicates and update all counts. This was the list used for all further analysis.

## Algorithm
Everything is written in python.

A few decisions needed to be made before analyzing the data. The first thing I decided was to not worry about substitutions. So in my analysis @ does not equal a. This is a limitation, since it would provide a more accurate representation of characters in passwords. Second, all passwords and English words where set to lower case. This way patterns would be more apparent. If the goal is cracking, it's incredibly easy to just change cases.

The algorithm loops through each of the word lists grabbing the word/password and their frequency. The word is then split into individual characters and each character pair is counted by adding the frequency.

I.E. The algorithm will take the word 'PaSsAs' with a frequency of 20 and do the following.

    passas -> [p,a,s,s,a,s]
    pa = 20
    as = 40
    ss = 20
    sa = 20

There is an option to turn off the use of frequency. I've analyzed this below as well.

Given a starting character, will this analysis allow us to predict what the next character will be?

## Analysis
For the analysis, I looked at with and without frequency counts. Within that, I did an internal comparison of frequency of each character set seen as well as a comparison with the dictionary values.

### With Frequency
With frequency taken into account, the top 100 password two character combinations only cover 11% of the all combinations. This seems rather low (I know very technical), so intuition says, this is not a good way to predict the password. In addition the top combination is 's2' which only constitutes 0.15% of combinations.

Lets compare this to the dictionary words. The top 100 combinations cover a staggering 60% of all combinations. This would be a good predictor for what would be the next letter in English. The top combination in the dictionary data 'th' covers almost 3% of all combinations.

Comparing the two further we can see 10 character combinations shared in the top 100 password and dictionary characters. I was expecting this to be higher, but this could be due to character substitutions in passwords. Such as 'mo' is in the dictionary top 100 but not for passwords. However 'm0' is in the top 100 password list.

### Without Frequency
Without taking into account the frequency of words and passwords doesn't change munch. The top 100 passwords now accounts for 35% of all combinations, which seems like it could be a better predictor. But this weighs good unique passwords the same as common ones. Dictionary gets worse at only 45% of combinations accounted for in the top 100.

Without taking into account frequency, '08' becomes the top password combination at 0.79% and 'se' becomes the top dictionary combination at 1.13%.

Surprisingly, without taking frequency into account, we see less substitutions in the password data. This means we now see 64 out of 100 duplicates between the data. This is closer to what I would have expected. Most people tend to use dictionary words for their passwords, so it would make sense to see duplicates across the data.

## Conclusion
This is probably not a good way to go about cracking passwords. Mostly this data simply shows to use dictionary word lists and substitution lists.

We could have done a few things better. One of which is look at common substitutions and see how that changes things. In many of the passwords, the standard alpha characters are replaced by numbers and symbols; such as @ or 4 for a, 5 or $ for s, and so on.


