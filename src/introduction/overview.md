# Introduction

> *The big idea is "messaging" <..> The key in making great and growable systems is much more to design how its modules communicate rather than what their internal properties and behaviors should be.* Alan Kay, creator of Smalltalk[^note]

### What is distributed?

> *Distributed system is any system in which a number of independent interconnected computers can cooperate in order to achieve a common goal.*
[Oxford Dictionary of Computer Science](https://www.oxfordreference.com/search?q=distributed&searchBtn=Search&isQuickSearch=true)

If we have two virtual machines (VM) with a web server on the first VM and a database server on the second one, do we consider such a system distributed? If VMs are on the same physical node, is it a distributed system?
If the web server and the database are running in separate containers and these containers are on different physical nodes, then this system is distributed. But a container orchestrator then moves both containers on the same node. Does the system of two connected containers lose its distributed flavor?

So there are many questions like above and *for the purpose of the book* we will follow the next definition of a distributed system.

**Distributed system is a software which has at least two components interacting with each other only by a network.**

Even if a network used for communication of the components for the present moment is a loopback inteface ("localhost"), it can become real network for the next moment.


### Distributed vs Decentralized

> Decentralized network is a dispersed network having several or multiple large hubs <..> and no dominant central hub.
[Oxford Dictionary of Media and Communication](https://www.oxfordreference.com/search?q=decentralized&searchBtn=Search&isQuickSearch=true)

Consider [master-slave replication](https://github.com/donnemartin/system-design-primer#master-slave-replication) of database. We have some slave databases running only read requests and a master database server executing read and writes queries and replicating all changes to slaves. Clearly this system is distributed due to the replication link. If we had no mechanism of promoting slave to master in case of the master's failure, then our system is not decentralized.

So decentralization insreases fault-tolerance removing single point of failure. But there is also a catch. Consider two network topologies: Mesh (in case of full decentralization) and Star (with central master).

![Mesh vs Star](images/star_mesh.svg)

So in the case of `n` nodes of mesh network topoly we have `n*(n-1)/2` connections[^graph] while in the case of the star network we have only `n-1` connections. So heavily meshed distributed systems produce increasing pressure on a network degrading its throughput.

### Why we need make our software distributed?

At least, to make it scalable and fault-tolerant at cost of increased complexity and managing effort.

[^note]: [Re: prototypes vs classes was: Re: Sun's HotSpot](http://wiki.c2.com/?AlanKayOnMessaging)

[^graph]: In this case we have a complete graph, so every node has connections with other `n-1` nodes. That's we have `n*(n-1)` edges but as we count every edge twice, total number of edges is `n*(n-1)/2`.