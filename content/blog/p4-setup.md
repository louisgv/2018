---
title: "Setup P4 on Vultr from a Linux Machine"
description: "How to setup remote Perforce server for Unreal from a Linux development machine"
date: 2018-04-29T18:45:07.147Z
draft: false
author: "Louis Vichy"
type: "post"


featured: ""
featuredalt: ""
featuredpath: "date"

tags: ["technical"]
categories : ["P4"]

linktitle: ""

---

# Abstracts



# Introduction

Setup Vultr and p4 server: http://shootertutorial.com/2015/11/14/setting-up-perforce-in-one-minute/

Quick and dirty server, but it works.

Grabbing P4V: http://answers.perforce.com/articles/KB/1137

Create and config a new user as super user. Then:

Grabbing P4 command line client:

```
sudo apt install helix-cli
```

Add the following environment variable to .bashrc or .profile or .zshrc:

```
export P4PORT="<VULTR_IP_ADDRESS>:1666"
export P4USER="<USERNAME>"
```

For example:

```
export P4PORT="0.0.0.0:1666"
export P4USER="mynameisjeff"
```

git to perforce resource: https://www.perforce.com/perforce/r15.1/manuals/dvcs/_git_perforce_command_mappings.html

# Conclusion
