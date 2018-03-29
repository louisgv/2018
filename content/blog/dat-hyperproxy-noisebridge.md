---
title: "DAT hyperproxy at NoiseBridge"
description: "Engaging with the privacy hackers at NoiseBridge wit the hyperproxy project"
date: 2018-03-25T00:09:41.619Z
draft: false
author: "Louis Vichy"
type: "post"

featured: "hyperproxy-diagram.jpg"
featuredalt: "Hyperproxy"
featuredpath: "date"

tags: ["technical"]
categories : ["DAT"]

linktitle: ""

---

# Abstracts

This is the first week of Sprint 3. Louis got a scholarship from Unity to attend GDC, and thus he flew to San Francisco for 10 days. During GDC, he talked to many many interesting people. After GDC, he went to Noise Bridge, and he got into an interesting discussion with several privacy advocate hackers that would turned out to be super beneficial to [hyperproxy](https://github.com/goonism/hyperproxy).

# Introduction

Louis met up with group of privacy hackers at Noise Bridge, a San Francisco based hackerspace during his trip to GDC. The group was led by Steve Phillips, a privacy advocate whose philosophy is to "Developing free, open source software to defend human rights, primarily with Pursuance."

During this meetup, Louis presented to the hacker about hyperproxy, the project he and [@stayfun_](https://twitter.com/stayfun_) has been working on since the beginning of the year. Steve was very enthusiastic about this project, saying that "the community needs more project like this, project that bridge network together, while still maintaining the decentralized nature of the Internet."

Going through with Steve and the group of privacy hacker with the doodle graph shown in the header, Steve went through with Louis the projected technical milestone.

![milestones](/img/2018/03/hyperproxy-milestone.jpg)

# First technical milestone

In order to get used to WebRTC and establish a playground for peers, it is necessary to create a sandbox WebRTC swarm environment. This is the first milestone, which is projected to be done within 2-3 days.

We go through the RFC, and Louis proposed that this milestone will be the initialization of the `hybridproxy-hub`, a `signalhubws` server deployed for the purpose of maintaining this `WebRTC` swarm playground.

# Second technical milestone

The second milestone we discussed is to implement the Hyperproxy Hybrid Node that given a DAT hash, can communicate with the TCP/UDP node network. This is essentially the `hyperproxy-node` module.

# Third technical milestone

The last milestone is to integrate the `hyperproxy-node` as a peer into the swarm created by `hybridproxy-hub`, and create logic for the hub to ensure that there is always enough `hyperproxy-node` in the swarm to serve the other peers.

# Conclusion

After the discussion with Steve, the group soon disbanded. Steve said that when we need more peer to test the network, we can always ping him. He can share our project to his network of privacy advocates who would love to join and test the software. The `hyperproxy` project is grateful to have him as a contact!
