# Unreliable network

A network of interconnected computers is based on some physical layer (Ethernet cables or radio in case of wireless networks)[^osi]. The physical layer itself is subject to different disruptions, noisiness, and other problems. So we always have to assume that **the network is unreliable** and implement different *retry, ordering, and healthcheck policies* in our services.

Moreover, network capacity is limited and we should estimate ahead what an amount of data the system under development is going to send through the network.

> **Network throughput** is an *amount of data that can be transferred through a network within a certain time period*
[Oxford Dictionary of the Internet (4 ed.)](https://www.oxfordreference.com/search?q=network+throughput&searchBtn=Search&isQuickSearch=true).

We can measure a network throughput in bits per second (bit/s or bps) or even in data packets per a unit of time.

> *Stop&Think*: How do you measure a network throughput? How do you estimate a maximum throughput (also called *bandwidth* or *channel capacity*) of a network?[^goodput]

Congestion control algorithms https://cloud.google.com/blog/products/networking/tcp-bbr-congestion-control-comes-to-gcp-your-internet-just-got-faster
https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/net/ipv4/tcp_bbr.c

https://cacm.acm.org/magazines/2017/2/212428-bbr-congestion-based-congestion-control/fulltext

https://dl.acm.org/doi/10.1145/52324.52356

https://github.com/google/bbr

Typical interservice communication within a corporate network often causes to think that the communication is almost instanteneous. But in case of a world wide internet connection spanning several networks the time to deliver data significantly increases.

> **Network latency** is *the time taken for a given signal or data packet to propagate through a circuit or network from its source point to its destination*
[Oxford Dictionary of Electronics and Electrical Engineering (5 ed.)](https://www.oxfordreference.com/search?q=network+latency&searchBtn=Search&isQuickSearch=true)

Network latency usually measured from source to destination and back constituting the so called *round trip time (RTT)* which is rougly speaking a double latency.[^ping]

Caching, keep-alive connections, geoghraphical proximity to the users are among usual options to decrease latency[^os].

**Network partition** happens if some nodes of a cluster in a distributed system cannot communicate due to a network failure but are supposed to.

So for a distributed system it is absolutely necessary to handle different network failures (which are quite common):
* meet bandwith requirements and handle network congestion on scaling;
* deal with increased latencies;
* have policies for network partitions: if a network partition creates two and more subclusters and such subclusters can behave independently (situation known as a *split-brain*[^rabbitmq]), how should the system evolve and finally merge conflicting states? 

[^osi]: Most of us have heard about [OSI model](https://en.wikipedia.org/wiki/OSI_model) as a theoretical framework to discuss networking stack. Just for reference:
* L1 - Physical layer (bits)
* L2 - Data link (frames)
* L3 - Network (packets)
* L4 - Transport (datagrams)
* L5 - Session (data)
* L6 - Presentation (data)
* L7 - Application (data)

[^goodput]: Also note that the maximum throughput is not actually an upper limit for *application specific data* throughput as there is always an overhead of a latency, protocols, encryption, compression, packet retransmission etc. A **useful** data throughput is sometimes called a *goodput* (see also [Wikipedia Goodput](https://en.wikipedia.org/wiki/Goodput)).

[^ping]: An actual latency measurement is complicated by the presence of several nodes in the packet way, queuing (several packets from different sources to the same destination are put in a waiting list to send) on the gateway, processing delays. Tools like a famous *ping* can use special control protocols (such as ICMP) which differ from those protocols that you actually use for data (such as TCP) so measurements are biased.

[^os]: Analyzing your application stack and CPU/IO profile can also help to choose an appropriate operating system if it is possible - see benchmarks [Benchmarks: FreeBSD 13 vs. NetBSD 9.2 vs. OpenBSD 7 vs. DragonFlyBSD 6 vs. Linux](https://www.phoronix.com/scan.php?page=article&item=bsd-linux-eo2021&num=1)

[^rabbitmq]: [Clustering and Network Partitions](https://www.rabbitmq.com/partitions.html)
