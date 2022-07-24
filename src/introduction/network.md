# Unreliable network

Our [definition](./introduction/overview.html) of a distributed system mentions a network as a medium for communication. This medium is a very interesting and complex subject per se but here we only review some core concepts relevant to our study.

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

Physically nodes (think, computers) in the network are connected by means of *network interface* controllers (NIC) - a special hardware allowing a computer to connect to the network via a cable ([Ethernet](https://en.wikipedia.org/wiki/Ethernet)) or a radio (WiFi). This physical layer is called a *link* in TCP/IP stack[^tcpip]. Nodes at the link layer are identified by Media Access Control Address (MAC)

So we have a network of nodes (also called LAN - local area network). How do we **inter**connect **net**works so that we can send a packet with data (called **datagram**) between two hosts from different networks? Here a *routing* problem arises. Computers called *routers*[^bridge] a relays between networks and are relaying datagrams from source to destination. But how do we distinguish a host in one network from a host from another network? We need some *addressing* at an *internet layer*. IPv4 addressing assigns each host an IP address of the form xx.xx.xx.xx . But with 2^32 unique IP addresses we cannot mark all hosts so IPv6 was developed. Anyway today we still use IPv4 and IPv4 address exaustion was mitigated by so called Network Address Translation (NAT) and private IPs. NAT - explore practically, p2p networks obstacles, violation of end-to-end principle, STUN, private network ips 10.xx.. and 192.168...., 172...
Internet layer is independent of the network topology and physical way of hosts connection.

After we identified a route between to hosts, we can use a *transport* protocol to, at least, specify **ports** on hosts to connect specific *application* processes running on hosts involved. Because internet layer is only responsible for routing and for reliability of communication, transport layers protocols can also offer some reliability mechanisms like congestion control, preserves data ordering, eliminate packet loss, provides packet deduplication. Among 

So we have a connection between to hosts and we can exchange 

TCP/IP processing can also be done at NIC coupled with some user-space library offloading CPU and OS kernel.
https://github.com/Xilinx-CNS/onload.


Network interface
MTU
NAT
Subnet 
Network mask
Broadcasting



So for a distributed system it is absolutely necessary to handle different network failures (which are quite common):
* meet bandwith requirements and handle network congestion on scaling;
* deal with increased latencies;
* have policies for network partitions: if a network partition creates two and more subclusters and such subclusters can behave independently (situation known as a *split-brain*[^rabbitmq]), how should the system evolve and finally merge conflicting states? 

[^osi]: Most of us have heard about [OSI model](https://en.wikipedia.org/wiki/OSI_model) as a theoretical framework to discuss networking stack.

[^goodput]: Also note that the maximum throughput is not actually an upper limit for *application specific data* throughput as there is always an overhead of a latency, protocols, encryption, compression, packet retransmission etc. A **useful** data throughput is sometimes called a *goodput* (see also [Wikipedia Goodput](https://en.wikipedia.org/wiki/Goodput)).

[^ping]: An actual latency measurement is complicated by the presence of several nodes in the packet way, queuing (several packets from different sources to the same destination are put in a waiting list to send) on the gateway, processing delays. Tools like a famous *ping* can use special control protocols (such as ICMP) which differ from those protocols that you actually use for data (such as TCP) so measurements are biased.

[^os]: Analyzing your application stack, CPU/IO profile can also help to choose an appropriate operating system if it is possible - see benchmarks [Benchmarks: FreeBSD 13 vs. NetBSD 9.2 vs. OpenBSD 7 vs. DragonFlyBSD 6 vs. Linux](https://www.phoronix.com/scan.php?page=article&item=bsd-linux-eo2021&num=1)

[^rabbitmq]: [Clustering and Network Partitions](https://www.rabbitmq.com/partitions.html)

[^tcpip]: Note that common reference to TCP/IP stack (also called **Internet protocol suite**) includes not only Internet Protocol and Transmission Control Protocol (TCP) but also other protocols such as a User Datagram Protocol (UDP) and QUIC at a transport layer and Internet Protocol Security (IPSec) at an internet layer. TCP/IP stack as a practical network stack predates the OSI theoretical model for general networks.
Since the adoption of TCP/IP in ARPANET in 1983 several proprietary implementations of the stack for different operating systems emerged. But the TCP/IP popularity increased when the University of California at Berkley had open sourced its implementation for BSD Unix  BSD sockets?? (see [Wikipedia Internet protocol suite](https://en.wikipedia.org/wiki/Internet_protocol_suite)).

[^private_ips]: Private IPs are [defined](https://datatracker.ietf.org/doc/html/rfc1918#section-3) to belong to the following subnets: 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16. Number after the slash is the *network prefix* ([CIDR notation](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)) and denotes number of bits which is common for all hosts in the network. So, for example, 192.168.0.0/16 means that the first 16 bits are the same for all hosts and 192.168 in decimal and the rest 16 bits of total 32 bits define the host (2^16 possible addresses in the network). To be correct, the total number of possible addresses should be decreased by two as all binary zeroes hosts denotes the network itself, while all binary one host is the broadcast address. So when you encounter an ip 192.168.12.134, you already know that this ip is not reachable publicly (from the Internet), it is some internal private host.
For IPv6 private ip address (called [*unique local address*](https://datatracker.ietf.org/doc/html/rfc4193) starts with a prefix `fd` (8 bits), then 40 bits of global id (choosen randomly by the operator of the network), then 16 bits for a subnet and the rest 64 bits define the host. So with IPv6 local privates ips are essentially globally unique if 40 bits of global id indeed are random.
Routing protocol experienced revisions 1-4 until becoming well known IPv4 

[^bridge]: Do not confuse with [network bridges](https://en.wikipedia.org/wiki/Network_bridge). Routers allow spearate networks to communicate while bridges join networks making them a single network.