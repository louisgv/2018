---
title: "DAT hyperproxy conceptualize"
description: "Conceptualizing the hyperproxy"
date: 2018-02-25T13:21:41-05:00
draft: false
author: "Louis Vichy"
type: "post"


featured: "hyperproxy-sd-0.png"
featuredalt: "Hyperproxy"
featuredpath: "date"

tags: ["technical"]
categories : ["DAT"]

linktitle: ""

---

# Abstracts

This blog entails the discussion between [@stayfun_](https://twitter.com/stayfun_) and I this Sunday on our brainstorming session for `Hyperproxy`.

# Introduction

We had a discussion with [@joeahand](https://twitter.com/joeahand) 7 days ago regarding what we can do to most effectively contribute to the Dat project. The project we decided to work on is related to this [comment](https://github.com/datproject/dat-js/issues/9#issuecomment-349718517) that Joe left on an issue in dat-js, the original project we planned to work on.

![dat-js issue 9](dat-js-issue-9.png)

@stayfun_ and I named the new project `Hyperproxy`. The goal of `Hyperproxy` is to be a bridge between Dat browser node (running on WebRTC) and the Dat native node (running on TCP/UDP).

# First iteration

Our first thought was to bootstrap an MVP that can route WebRTC request into a proxy server that then send out that request to other node via [discovery-swarm](https://github.com/mafintosh/discovery-swarm). The main concern that @stayfun_ brought up was, why are we making `WebRTC` connection instead of a `WebSocket` connection? In our server-client model, they are essentially identical. `WebSocket` has a way nicer interface as well as being independent from handshake problems that are very common with WebRTC connection.

The vague idea we had behind sticking with WebRTC, is to avoid centralizing the proxy. We booth understood however, that this proxy is prone to be centralized. It must be hosted. Only the browser connecting to it would be distributed. It is essentially useless to even consider between `WebRTC` or `WebSocket`.

On the other hand, the value of embracing WebRTC, is that a node can request resource from nearby WebRTC nodes online. That is essentially the core reason. We think the value of Dat is in the way data is distributed, and thus we dig deeper and deeper into the dependency of [webrtc-swarm](https://github.com/mafintosh/webrtc-swarm)

# Second iteration

The main module that webrtc-swarm depends on is a [signalhub](https://github.com/mafintosh/signalhub) server. This server exposes a WebRTC hub where online nodes can discover each other. At its core, signalhub has two modules: the [client](https://github.com/mafintosh/signalhub/blob/master/index.js) side javascript that handles all connection tokening, and the [server](https://github.com/mafintosh/signalhub/blob/master/server.js) side which is just a barebone `http` server that pipe events between online nodes.

Our idea at this moment, was to create a `webrtc-swarm` server that would capture requests from other WebRTC
nodes online that it can observe from the hub, and pipe its request - the DAT archive key - to the native nodes.

> Concern: The socket server must subscribe to each and every possible Dat public key, which is impossible.

We quickly figured that the second iteration is pointless, unless our server itself is the signalhub that can inspect and read the request archive key. We decided to dig deeper into `signalhub`.

# Third iteration

Digging deeper into `signalhub`, we found this [interesting issue](https://github.com/mafintosh/signalhub/issues/33):

![signalhub issue 33](signalhub-issue-33.png)

This issue leads us to [signalhubws](https://github.com/soyuka/signalhubws), a very interesting rewrite/reimplementation of signalhub that uses a websocket server instead of a plain http. We found this to be interesting and might be a great experimentation for our proxy server. Props to [@s0yuka](https://twitter.com/s0yuka) the author of the library.

Further digging into `webrtc-swarm` lead us to [simple-peer](https://github.com/feross/simple-peer), an awesome WebRTC abstraction by [@feross](https://twitter.com/feross). The interesting thing about this finding is that it in turn lead us to the [web-torrent project](https://github.com/webtorrent/webtorrent), where we found this diagram:

![webtorrent diagram](webtorrent-diagram.png)

Apparently, the torrent protocol suffered the same problem as Dat before webtorrent. The bittorent native node and webtorrent nodes (webrtc) nodes could not be on the same network due to protocol mismatching. The solution was to create a hybrid node with [web-torrent-hybrid](https://github.com/webtorrent/webtorrent-hybrid). The hybrid torrent node was the missing link that can communicate with both native or webtorrent node, allowing for data arbitrage between the other two type of nodes.

Learning from webtorrent, our third concept of `Hyperproxy` composes of two modules. The first is a portable hybrid node that can be run either as a server node or be embedded into any electron process. This hybrid node arbitrages connection between WebRTC and TCP/UDP native nodes. The second module is a dedicated signalhub server that tracks the hybrid node and make sure that for every connected foreign WebRTC client, there is at least one capable hybrid node within that behave just like any webRTC node that will serve back response from TCP/UDP nodes.

# Conclusion

In this article, we briefly walk through some our conceptualization of `Hyperproxy`. We seek to implement an MVP for `Hyperproxy` to validate our concept soon. Follow along at [https://github.com/goonism/hyperproxy](https://github.com/goonism/hyperproxy)
