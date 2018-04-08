---
title: "Hyperproxy - shared config and state protocol"
description: "Technical article about designing a config for both browser and server, as well as internal state protocol for Hyperproxy"
date: 2018-04-15T17:50:31.902Z
draft: false
author: "Louis Vichy"
type: "post"

featured: "hyperproxy-config.png"
featuredalt: "Hyperproxy Config"
featuredpath: "date"

tags: ["technical"]
categories : ["DAT"]

linktitle: ""

---

# Abstracts

A modular approach to a project often has a common trade-off, namely how to share a single configuration across different module? Similar to this problem, Hyperproxy also have an internal problem within itself. Due to its distributed network nature, it might be a challenge to determine the current state of any peer at a given moment. This article discuss these two problem in a sequential order, and seeks to show the reader how we at [GSM](https://github.com/goonism/) solved this challenge using awesome open source tools.

# Problem no.1

As discussed in a [previous blog post](/blog/dat-hyperproxy-lerna-setup), Hyperproxy is adopting `monorepo` as its codebase architecture. There are many valuable benefit associated with this architecture, however, how do we share configuration across modules? This question is especially challenging for Hyperproxy as one of its module - `hyperproxy-browser` - is a module with the most distinct Implementation compared to the other. Since it is a browser application, a standalone one that can be served over a static server, integrating a server configuration, especially using environment variable, is a challenge.

# Solution no.1

Since horizontal scaling is quite trivial for a monorepo, our solution is to create a module called `hyperproxy-config` that would export the configuration we wanted to share amongst all of our module. However, we got stuck with integrating this config module with our browser module, as the config is trying to read from the environment variable which the browser does not have.

Our first attempt at solving this was to do a simple constant declaration that we can toggle on and off inside the configuration. We soon realized however, that this won't scale, as we want to be able to tweak this valuable as we run the entire project. Tweaking a source code as if it is environment variable is not a good solution.

Digging deeper, we found that we are lucky to have used the versatile utility browserify for building our front-end application, as there are many many plugins implemented for it. One of them - [envify](https://github.com/hughsk/envify) - solves this exact problem we are having by parsing the environment variable from the parent process into the child process. This allows us to consume the exact same configuration between browser and server!

With this, we close with the first problem

# Problem no.2

Hyperproxy is a project running on top of a decentralized P2P network. Thus, it is often sometime confusing when it comes to know where did a message come from, and what is the purpose of the message. Other challenges such as reduce broadcast and design such that we have efficient one-to-one communication between related party is also a challenge which we will discuss later on. But for now, the main question is, how does a `hub` knows if a `browser` has joined a certain dat-token channel, how does a `node` knows if a message is from a `browser` requesting resource from TCP/UDP, and how does a `browser` knows if a message is a reply from a `node`?

Another question, how would a `hub` knows when a `browser` has left a channel, so that it can delete the subscribed `node` as necessary to save memory?

# Solution no.2

The solution we designed is a set of message type, each associated with a certain event described above. They are:

## JOIN
Peer notifies the hub that it is joining the channel. The hub then check to spawn appropriate hyperproxy-node to watch for potential request in this channel.
## LEAVE
Peer notifies the hub that it will leave the channel.
## REQUEST
Peer notifies other peer that it wants certain resource
## RESPONSE
Peer responses to requested peer that it has the requested resource

Together with our design for an internal message structure for hyperproxy that is formated like the json below:

```
{
    from: <string>,
    type: HUB_MSG_TYPE,
    body: <object | string | blob>
}
```

# Discussion

The two problem proposed is related because they are both about sharing information between modules. The difference is the first happens during compile time (building the hyperproxy suite), the second occurs during run time! 

# Conclusion

As our development environment evolve, we become more and more productive in our quest to contribute to the Dat project with Hyperproxy. The beta release of Hyperproxy-browser will be hosted on github soon for everyone to test. And we hope you will enjoy browsing the dat network from your normal browser as much as we did enjoy working on hyperproxy!
