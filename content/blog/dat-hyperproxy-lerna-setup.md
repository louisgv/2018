---
title: "Hyperproxy - monorepo"
description: "Why, what and how to work with lerna based monorepo"
date: 2018-04-08T17:45:00.880Z
draft: false
author: "Louis Vichy"
type: "post"

featured: "hyperproxy-lerna.png"
featuredalt: "Hyperproxy Lenra"
featuredpath: "date"

tags: ["technical"]
categories : ["DAT"]

linktitle: ""

---

# Abstracts

Monorepo is a code base architecture where every module associated with a single software is placed under a single repository. This architecture might be an overhead cost to setup if the project is being developed by a single person. However, when a project has several modules and is authored by a team of people, it is extremely beneficial to team productivity as proven by its usage in large company such as [Facebook](https://www.youtube.com/watch?v=X0VH78ye4yY) and [Google](https://www.youtube.com/watch?v=W71BTkUbdqE), while the trade-off for adopting this practice is actually quite minimal.

# Introduction

Previously, we talked about how we arrived at our [3 technical milestones for hyperproxy](/blog/dat-hyperproxy-noisebridge). Our current implementation requires constant server and browser restart in order to test our code. Furthermore, we're not scaling our module fast enough. In order to achieve modular implementation so that our team can works on milestone independently, as well as creating tooling to automate app refresh pipeline, for we need to catch up on our delivery promise with the Dat project, @stayfun_ and @lgvichy decided to adopt the monorepo architect for Hyperproxy.

In our [RFC](https://docs.google.com/document/d/1zvGN7hmeOVHOaQjCUr3XuNLZxZxvOn1u0GwVhR_ucB4/edit?usp=sharing), we defined 3 main concepts: `hyperproxy-browser`, `hyperproxy-node` and `hyperproxy-hub`. Let's take an indepth look at the main functionality of each module.

## Browser

The `hyperproxy-browser` module acts as a demonstration for a web application that can interact with the TCP/UDP dat network via our proxy. Thus, this module would be a simple front-end web application that can be run from pure static server without any backend. This means that the design of the module must allow it to be a standalone piece when it comes to deployment.

## Node

The `hyperproxy-node` module is the conceptualized "hybrid" client that acts like a browser module in term of joining the swarm. It listens to request from browser module, and respond back with data it query from the TCP/UDP dat network.

## Hub

The `hyperproxy-hub` module serves as a meeting station for the P2P network, where it starts a websocket lobby server for peer to see and initiate connection with each other. The server also keep track of available `hyperproxy-node` instances that is serving a certain DAT archive address.

# Implementation

We used [lerna](https://lernajs.io/) to initialize our monorepo. This also mean that we will have to nuke our previously created repository, and embed what we have into this single repository. The structure of the project looks like the following:

```
.
├── documentation
│   ├── CODE_OF_CONDUCT.md
│   ├── CONTRIBUTING.md
│   ├── notes
│   └── rfcs
├── icon.png
├── lerna.json
├── LICENSE
├── package.json
├── packages
│   ├── hyperproxy-browser
│   ├── hyperproxy-hub
│   └── hyperproxy-node
├── README.md
└── yarn.lock
```

# Trade-offs

The main trade offs that @stayfun_ have found with our current structure is that, since we tries to have each and every module adhere to the same architecture, it is often confusing which files developer might be looking at without examining the name of th directory it is under. For example, for each package in `packages`, there is an `index.js` file inside.

# Benefits

The most beneficial aspect of this setup in our opinion is that, we only have to clone once, and all the change to a single feature that would impact two or more modules, can be referenced in the same pull request.

In order to automate the running pipeline of every single modules, we created a run script in the `package.json` file that looks like this:

```
    "watch": "lerna run watch --parallel"
```

This allows us to run all our module in parallel. By also adding change watching capability into each module runner, we have a developer environment setup where, upon applying a change on a certain module, that module will get reloaded immediately, be it on the browser or on the server. This is highly beneficial and allows us to focus on implementing the milestone.

Another benefit of this is that now we can avoid cross-repo merge conflict. I.e, instead of resolving conflict due to our independent implementation of the 1st and 2nd milestone, we can collaboratively fix it within a single repo instead of having to juggle two or three.

Lastly, this allows us to scale our module and further embrace the modular pattern. If we ever need to create a new module, it will be just a simple directory under `packages`, no need to hussle through github to setup an entirely new repo. This also helps to avoid namespace plaguing, forcing us to have all related package under the same name space hyperproxy. This is quite beneficial in its own way, as there is already a package named `hyperproxy` on npm! Had we not go with monorepo, we might have been blocked from ever publishing it.

# Conclusion

Having lerna has been a tremendous boost to our productivity with the hyperproxy project. We deeply thank those who have created lerna, the babel project. The future of hyperproxy seems as bright as ever.
