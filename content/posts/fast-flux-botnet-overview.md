---
title: "Fast Flux Botnet Overview"
date: 2019-09-26
summary: |
  A breezy, tour of fast‑flux botnets, those sneaky DNS tricks that let malicious actors hop around like digital grasshoppers. We’ll peek at how dynamic DNS and round‑robin magic keep the bad guys’ command‑and‑control servers slippery, and glance at the cat‑and‑mouse game of detection (TTL tricks, activity indexes, and the occasional semantic sleuthing). Spoiler: it’s a wild ride, but the good news is there are ways to shine a flashlight on the flux.
author: "Halvo (Human)"
tags:
  - fast-flux
  - botnet
  - dns
  - detection
  - mitigation
  - security
slug: fast-flux-botnet-overview
draft: false
---

## Introduction

In this post we will explore a brief overview of the fast-flux (FF) technique used by botnets. [Here is my full paper](/security/FastFluxPaper.pdf) with more detail regarding what a botnet is and how FF works.

## Botnet Overview

Botnets  are  a  major  threat  to  all  those  connected  to  the  Internet.   They are used for distributing spam, hosts for malicious code, sending phishing attacks, and performing a variety of attacks, including denial of service (DOS). Many botnets will use DNS names to control or connect to the botnet. This would seemingly be easy to shutdown, just block the particular domain, however through a technique called fast-flux (FF), botnets are able to evade detection and mitigation.

## Fast Flux Overview

Fast-flux is the process of quickly changing the domain name or IP addresses associated with a domain in order to hide the bot-master, or  command and control (CC), for the botnet. These fast changes are accomplished through two primary technologies, dynamic DNS (DynDNS) and round robin.

To quickly change the names associated with the botnet, fast-flux uses dynamic DNS (DynDNS). DynDNS's original purpose was for those individuals who did not have static IP addresses, allowing them to quickly update the name-address relationship as needed. Botnets will keep a list of names that they cycle through, bringing up new names either as needed or randomly. The bots will then have several locations (including built in lists) to check the new CC domain.

By quickly changing the domain associated with the bot-master, it is effectively impossible to use DNS names to setup a rule in a firewall to block connections to the bot-master. However a savvy admin would then check what IP is being contacted and block all those connections. Enter round robin.

Round robin was a technique developed for load balancing. Sites that see a large amount of traffic need to balance that traffic between several servers. In this way none get bogged down too much. Fast-flux botnets use this technique to hide their CC IP addresses. A botnet will setup a series of front-end proxies that are disposable. Bot-master's will use DynDNS to add and remove IP addresses associated with the domain and round-robin will rotate through them. This way the CC stays hidden and no firewall rules can be created to block on IP address.

In addition to DynDNS and round-robin, some botnets will be double-fluxed. In this technique a botnet will setup its own name servers and rotate through them as well. More detail is in the paper.

## Detection/Mitigation

There are two primary ways of detecting and mitigating fast-fluxing botnets that need to be used in conjunction. The first is to look at the time to live (TTL) for DNS entries to be cashed. Fast-fluxing botnets tend to use very short TTL values compared to legitimate domains. The second is keeping a "FF Activity Index" or how often name-address relationships change. The "FF Activity Index" will hold both how often the IP address for a given domain changes and how often domains change for a single IP address. Even looking at these two indicators still yields a number of false positives. More details in the paper.

## Conclusion

Botnets are getting more sophisticated and more research is needed to detect these techniques. The best way to block these connections is to attempt to stop the CC directly. Most hide behind proxies and many use FF techniques to hide those. FF is an arms race between detection and ever more sophisticated ways of hiding activities.

