---
title: "Bad Password Analysis Dictionary Words"
date: 2021-03-11
summary: |
  In this delightfully “bad” dive into password hygiene, we scrape millions of leaked passwords for the first dictionary word they contain. The top ten words (love, baby, password…) barely scratch 5% of the total, and a whopping 21k words appear only once. We also compare happy vs. angry vocab. Turns out love trumps f**k by a healthy margin. The takeaway? Stick to random passphrases; dictionary words are a playground for attackers and a source of endless amusement for analysts.
author: "Halvo (Human)"
tags:
  - password-analysis
  - string-analysis
  - security-research
  - analysis
slug: bad-password-analysis-dictionary-words
draft: false
---

## Introduction

For this episode of bad analysis, we are going to be looking at word frequency in passwords. Overall this isn't terrible analysis, but what makes it bad is I'm just looking for the first occurrence of a dictionary word in each password. This will miss *a lot* of words in passwords.

Additionally we will miss words because:
1. Only the first dictionary word in each password is used
2. Only American English words found in the Linux American English dictionary are used
3. No common replacements are used for numbers or symbols (all of those are just blanked out)
4. No common misspellings are corrected
5. Plurals are considered unique words

We are missing a lot of words in these passwords, but that is why this is bad analysis.

## Data

The passwords come from several leaks. These include honey-net, MySpace, rockyou, hotmail, phpbb, and tuscl lists. All of these lists contain the count of how many times a password was used as well. Total there are 14,584,438 unique passwords.

This took forever to loop through, pulling out the words, then comparing them to the dictionary words. My code is only single threaded and doesn't use any additional efficiencies. It took around 15 hours to complete ... so if anything went wrong, I'm not running it again :) Maybe at some point I'll multi thread it and see if it can run a little faster.

I'm comparing the password list to the American English word list found on Linux. There may be a more complete list somewhere out there, but this worked for me.

## Results

### Raw Data

The word were extracted, counted, and sorted. There were 68,402 unique words, the top 10 words account for around 5% of total words seen, and there were 21,191 unique words only seen in their own password. 

Here are the top 10 words used in the passwords (with the caveats above):

All percentages are approximate

| Word | Percentage |
| ---- | ---------: |
| love | 2.0 %      |
| baby | 0.7 %      |
| password | 0.4 %  |
| angel | 0.4 %     |
| ana  | 0.4 %      |
| princess | 0.3 %  |
| sexy | 0.3 %      |
| girl | 0.2 %      |
| and | 0.2 %       |
| ito | 0.2 %       |

### Additional Fun Stuff

How positive are people's passwords. Using a list of positive words found at [Positive List](https://gist.github.com/mkulakowski2/4289437) and a list of negative words found at [Negative List](https://gist.github.com/mkulakowski2/4289441), I've compared to our word frequency from our list.

Positive words were used 1,172,617 times and negative words were used 1,172,617. As an optimist at heart, this didn't surprise me too much. Let's take a closer look and look at the top 5 words in each category.

| Positive | Number   | Negative  | Number  |
| -------- | -------: | --------: | ------: |
| love     | 442,689  | f\*\*k    | 53,969  |
| angel    |  98,154  | rocky     | 41,655  |
| sexy     |  65,062  | mar       | 38,915  |
| sweet    |  44,192  | bi\*\*h   | 38,262  |
| lover    |  39,794  | crazy     | 21,330  |

Looking at positive and negative occurrences has it's own issues beyond just the word analysis. As you can see there are certain omissions that I would think would be in positive, like "baby." There are also inclusions in negative that I would not have made, such as "mar" which could just be March for someone's birthday. Better lists would need to be found or crafted, or entire passwords would need a language processor to determine if they are negative or positive.

## Conclusion

Not much to conclude here, mostly this was for fun. Don't use dictionary words in your password, it doesn't take long to loop through the dictionary, and if you do, try to use longer random words, rather then meaningful ones.

People tend to be more positive in their passwords which is nice to see.

This was a lot of fun to implement and I may come back to this to see if I can improve upon looking at words.

## Future Work

- Thread all the things, maybe it'll run faster.
- Look for more than just the first word in each password
- Replace numbers with common letters (like 4 becomes a and 3 becomes e)
- Maybe look for plurals as the same

