---
title: "Stateless Detection of Malicious Traffic"
date: 2019-08-23
summary: |
  A recap of my master’s thesis that proves you can sniff out nasty traffic using only one‑way packet metadata (TTL, ports, timing)—no payload inspection required.
author: "Halvo (Human)"
tags:
  - network-security
  - malware-analysis
  - machine-learning
  - research-paper
slug: stateless-detection-of-malicious-traffic
draft: false
---

## Introduction

In order to allow flexibility in deployment location and to preserve user privacy we have performed research into stateless classification of network traffic. Because traffic does not always follow the same path through a network, by not worrying about state, we can deploy anywhere. We also use only one direction of traffic as replies could also follow a different path through the network. And by not requiring data within the packet, we can perform analysis on encrypted traffic as well.

Our research shows that it is possible to determine if traffic is malicious by using packets traveling in a single direction and without the data contained in the packet. Our research also shows that with the use of timing, time to live (TTL) value, source IPs, destination IPs, and ports, it is possible to determine if the traffic is malicious. Through our research we have shown it is possible to show, with some confidence, if traffic is malicious regardless of location, and while preserving user privacy.

This post serves as an introduction to my master's thesis of the same title. [Full paper for those interested.](/security/StatelessDetectionOfMaliciousTraffic.pdf)

## What Was Done

The system we developed for this research was an intrusion detection system (IDS), thus does not block any traffic. Most IDS's use specific signatures for traffic. These are inflexible and will only detect the specific attack. If the traffic is modified in any way, it will no longer be detected. Instead of signatures, our system looks at ongoing traffic patterns.

Signatures work great for intrusion prevention systems (IPS), since if you want to block traffic, you want to be sure it is malicious. However, malicious actors regularly change signatures of attacks in order to work around IPSs.

Our system differs since it uses patterns. Because of this, we cannot say for certain if traffic is malicious, but rather provide a confidence value. This does not work for an IDS, but will detect traffic even when a signature changes. Using this confidence value, a security researcher could investigate the traffic further. Determine if it is malicious and a signature if necessary.

We used three primary data points to determine if traffic was malicious: destination port, TTL, and packet frequency. To actually perform the classification, we used a software package called WEKA (an open source trainable algorithm) and focused on bayesnet classification. 

## Conclusions

While performing the research, we observed that port only usage provided the least confidence. This isn't surprising, since it will only be useful for network scans. Packet frequency proved to be a better data point for classification. It appeared that benign traffic had a burst at the beginning, with fairly regular communication for the rest of a session. Malicious traffic would have a large burst of traffic followed by nothing, or very little traffic. TTL proved to be one of the best signatures. This is due to the fact that most benign traffic is to a few locations, which are usually physically close. TTL for malicious traffic is usually smaller, either due to further physical locations, as part of the attack, or for the attacker to gain further information about the victim network.

Frequency, TTL, and ports could each provide some level of confidence, but with their powers combine we can achieve a fairly high level of confidence, with a low false positive rate (see paper for full details).

Our research shows that it is possible to provide a level of confidence without requiring deep packet inspection and without keeping a copy of the traffic. It can be used to initiate further investigation on how traffic is malicious.
