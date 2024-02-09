# Distributed vs Decentralized

> Decentralized network is a dispersed network having several or multiple large hubs <..> and no dominant central hub.
[Oxford Dictionary of Media and Communication](https://www.oxfordreference.com/search?q=decentralized&searchBtn=Search&isQuickSearch=true)

Consider [master-slave replication](https://github.com/donnemartin/system-design-primer#master-slave-replication) of database. We have some slave databases running only read requests and a master database server executing read and writes queries and replicating all changes to slaves. Clearly this system is distributed due to the replication link. If we had no mechanism of promoting slave to master in case of the master's failure, then our system is not decentralized.

So decentralization insreases fault-tolerance removing single point of failure. But there is also a catch. Consider two network topologies: Mesh (in case of a full decentralization) and Star (with a central master).

![Mesh vs Star](images/star_mesh.svg)

So in the case of `n` nodes of mesh network topology we have `n*(n-1)/2` connections[^graph] while in the case of the star network we have only `n-1` connections. So heavily meshed distributed systems produce increasing network contention.
