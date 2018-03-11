---
title: "tracestar Concept"
description: "conceptualizing tracestar"
date: 2018-03-10
draft: false
author: "Louis Vichy"
type: "post"


featured: "tracestar-icon.png"
featuredalt: "tracestar"
featuredpath: "date"

tags: ["game"]
categories : ["tracestar"]

linktitle: ""

---

# Abstracts

Revisiting `A*`, its seems there are not that many interactive experience that can immerse knowledge seeker into the algorithm. Game is a great device for this. In this spirit, the lab decided to concept `tracestar`, a casual game with the main goal of letting the user experience A*.

# Introduction

A* is a path finding algorithm optimized to find shortest and cheapest path between two points. The algorithm is a generalized case of Dijkstra's path finding algorithm, using a heuristic, or an estimated guess as to how far away a node is from the goal.

An excellent [introduction to A*](https://www.redblobgames.com/pathfinding/a-star/introduction.html) was created by [@redblobgame](https://twitter.com/redblobgames).

The idea behind `tracestar` is heavily inspired by Sebastian Lague's [explanation of the algorithm]( https://www.youtube.com/watch?v=-L-WgKMFuhE).

# Architecture

`tracestar` follows the same design as [vize](https://louisgv.me/vize) and [pattar](https://louisgv.me/pattar). It will be a modular ES6 project that uses the browser canvas to draw a grid.

The pallet defined in Sebanstian's video is as follow:

- white: open nodes
- black: wall nodes
- red: visited nodes
- blue: star and end nodes
- green: queued nodes

Each node are presented as a square box. The side of the box has a length following this formula:

> side = Math.min(window.innerWidth, window.innerHeight) * 0.18;

The screen are divided into a grid of 5 x `(Math.max(window.innerWidth, window.innerHeight) / side)`. It will be padded based on window.innerWidth and window.innerHeight so that the grid remains centered regardless of screen size and orientation.

# Implementation

The implementation can be found [here](https://github.com/louisgv/tracestar) and you can test out the initial version of the game [here](https://louisgv.me/tracestar/).
