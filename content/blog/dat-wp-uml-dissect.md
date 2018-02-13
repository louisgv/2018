---
title: "DAT White Paper Dissection"
description: "Compare and contrast the dat-js implementation's UML to the source white paper."
date: 2018-02-08T11:34:28-05:00
draft: true
author: "Louis Vichy"
type: "post"


featured: "dat-js-uml.png"
featuredalt: "DAT-js UML"
featuredpath: "date"

tags: ["technical"]
categories : ["DAT"]

linktitle: ""

---

# Abstracts

This is a technical log on our progress on the FOSS project. It is the second week of Sprint 1, and the Rochester weather is ever forgiving. My ankles were soaked in snow for at least three days straight, and my shoes had turned into a bleak white shade.

Our initial goal of Sprint 1 week 2 is to research and understand as much as possible about the Dat-js library as well as the DAT protocol as a whole from their white paper.

# UML

The Dat-js library composed of two classes: Dat and Repo.

![Dat class](/2018/img/2018/02/dat-class.png)

The main class Dat implements the EventEmitter API which is implemented within the browser. The constructor takes in an opts Object for potential option handling. The class has has two public fields: opts which is a reference to the option Object, and repos is is an array of Repo which is the second class of the library.

![Repo class](/2018/img/2018/02/repo-class.png)

The second class Repo is an abstraction to interact with the core API of the DAT protocol which is hyperdrive. Directly from the class header, it is "a hyperdrive with some default settings." It relays its transaction via WebRTC. Its cache layer is memdb.

The overall UML graph of the library (disregarding external library) is as follow:

![Dat-js UML](/2018/img/2018/02/dat-js-uml.png)

# White Paper

There are two important sections of the DAT white paper that can be used to correlate with our simple UML.

First is the components and intended functionality of Dat. Most of Dat's implementation of "storage, content integrity, and networking protocols" are condensed into an external module called Hypercore. In order to dissect the Hypercore module, we will do that in another blog post if there is a need.

The encryption scheme used by Dat, namely public private key, is visible within the public field of the Repo class, where it holds a reference to the hex representation of the key of a hyperdrive archive.

```
this.archive = this.drive.createArchive(key, this.opts);
this.key = this.archive.key.toString('hex');
this.privateKey = this.archive.privateKey;
```

Dat paper describes three source discovery actions: `join(key, [port])`, `leave(key, [port])` and `foundpeer(key, ip, port)`. The join and leave actions are explicitly defined as public procedural methods in the `Repo` class:

```
Repo.prototype.join =
Repo.prototype.resume = function () {
  this.swarm.on('peer', this._replicate.bind(this))
}

Repo.prototype.leave =
Repo.prototype.pause = function () {
  this.swarm.removeListener('peer', this._replicate)
}
```

While the foundpeer actions is abstracted into events emitted via the `EventEmitter` API:

```
Repo.prototype._open = function () {
  var self = this
  this.archive.open(function () {
    self.emit('ready')
  })
}
```

Second are the 10 message types used within the protocol: `Feed`, `Handshake`, `Info`, `Have`, `Unhave`, `Want`, `Request`, `Cancel` and `Data`.

Below are the messages handled by Dat-js:

## Feed

The Feed component is visible within the `get` method of the `Dat` class, where it uses a key to search for the location of a piece of data.

```js
Dat.prototype.get = function (key) {
  return this.repos.filter(function (repo) {
    return key.toString('hex') === repo.key.toString('hex')
  })[0]
}
```

## Info

The Info component is implemented within the replicate private method of the `Repo` class. This method essentially tell the network whether the local archive is live (the stream is active or not).

```js
Repo.prototype._replicate = function (conn) {
  var peer = this.archive.replicate({
    upload: true,
    download: true
  })
  pump(conn, peer, conn)
}
```

# Conclusion

In this article, we briefly walk through some bits of the DAT-js deprecated library and contrast it with the white paper specification. The road ahead seems wide open.
