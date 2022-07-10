# Unreliable network

Our [definition](./introduction/overview.html) of a distributed system mentions a network as a medium for communication. This medium is a very interesting and complex subject per se but here we only review some core concepts relevant to our study.

A network of interconnected computers is based on some physical layer (Ethernet cables or radio in case of wireless networks)[^osi]. The physical layer itself is subject to different disruptions, noisiness, and other problems. So we always have to assume that **the network is unreliable** and implement different *retry, ordering, and healthcheck policies* in our services.

Moreover, network capacity is limited and we should estimate ahead what an amount of data the system under development is going to send through the network.

> **Network throughput** is an *amount of data that can be transferred through a network within a certain time period*
[Oxford Dictionary of the Internet (4 ed.)](https://www.oxfordreference.com/search?q=network+throughput&searchBtn=Search&isQuickSearch=true).

We can measure a network throughput in bits per second (bit/s or bps) or even in data packets per a unit of time.

> *Stop&Think*: How do you measure a network throughput? How do you estimate a maximum throughput (also called *bandwidth* or *channel capacity*) of a network?[^goodput]

Typical interservice communication within a corporate network often causes to think that the communication is almost instanteneous. But in case of world wide internet connection spanning several networks the time to deliver data significantly increases.

> **Network latency** is *the time taken for a given signal or data packet to propagate through a circuit or network from its source point to its destination*
[Oxford Dictionary of Electronics and Electrical Engineering (5 ed.)](https://www.oxfordreference.com/search?q=network+latency&searchBtn=Search&isQuickSearch=true)

Network latency usually measured from source to destination and back constituting the so called *round trip time (RTT)* which is rougly speaking a double latency.[^ping]

Caching, keep-alive connections, geoghraphical proximity to the users are among usual options to decrease latency.




[^osi]: Most of us have heard about [OSI model](https://en.wikipedia.org/wiki/OSI_model) as a theoretical framework to discuss networking stack.

[^goodput]: Also note that the maximum throughput is not actually an upper limit for *application specific data* throughput as there is always an overhead of a latency, protocols, encryption, compression, packet retransmission etc. A **useful** data throughput is sometimes called a *goodput* (see also [Wikipedia Goodput](https://en.wikipedia.org/wiki/Goodput)).

[^ping]: An actual latency measurement is complicated by the presence of several nodes in the packet way, queuing (several packets from different sources to the same destination are put in a waiting list to send) on the gateway, processing delays. Tools like a famous *ping* can use special control protocols (such as ICMP) which differ from those protocols that you actually use for data (such as TCP) so measurements are biased. 