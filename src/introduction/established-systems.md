# Established systems

> *Successful software is composed not built.*
 Roger Johansson, creator of ProtoActor

Implementing distributed systems right way is hard. In most cases we can compose our software within well established distributed systems. Of course, the goal of the book is to learn how to implement such systems from scratch. But we should be familiar with other options. Some are listed below in the order of decreasing technology lock-in.


### Battle-tested Erlang/OTP

> *Any sufficiently complicated concurrent program in another language contains an ad hoc informally-specified bug-ridden slow implementation of half of Erlang.*
[Robert Virding](https://rvirding.blogspot.com/2008/01/virdings-first-rule-of-programming.html), one of the creators of Erlang

[Erlang](https://www.erlang.org) still remains to be a unique fusion of practical engineering and clever architectural vision. Created in 1980s, it still has such a great potential to evolve in a modern web[^klarna][^my_experience].

We won't dive in the language per se and pragmatic ideas of "let it fail", [supervision trees](https://adoptingerlang.org/docs/development/supervision_trees/), and fault tolerance. You can read very consice and full of practical wisdom [thesis](https://erlang.org/download/armstrong_thesis_2003.pdf) (highly recommended) of Joe Armstrong, one of the Erlang creators.

Let's focus more on Erlang distribution capabilities. Erlang is compiled to run in a virtual machine called BEAM[^lumen]. [Elixir](https://elixir-lang.org/) is also a BEAM-runnable language with interoperability with Erlang.

Erlang lightweight processes, while executing application logic, communicate with each other using signals. Most used signal type is a message. Processes are scheduled for execution by BEAM. You can run millions of processes[^erlang_limits]. OTP is a collection of reusable components (abstractions for concurrent and distributed patterns such as client-server). So Erlang/OTP is a framework allowing you to quickly create complex concurrent and distributed systems.

BEAM (written in C) can be extended with dynamically loaded compiled modules with Native Implemented Functions ([NIFs](https://www.erlang.org/doc/tutorial/nif.html)). So you can write this shared module in a language you prefer (supporting C ABI, of course)[^rustler].  
Erlang is primarily well suited for a massive IO-bound load so for CPU-bound tasks you should use, for example, above mentioned NIFs[^erlang_io_bound].

Every node to form a cluster should share the same cookie file with a secret. It is rather a basic "security" with the aim to differentiate two or more clusters.
Every Erlang VM is started with a predefined name and then it's enough to register the rest `n-1` nodes on the first node. All nodes sharing the same cookie will propagate connections to each other creating a fully connected mesh network. This default transitive connection propagation can be configured -- a node called *hidden node* without transitive connections can be used to create clusters with less connections[^erlang_distribution].

By default, communication between nodes of a cluster is done over TCP.  Separate daemon process (called EPMD - [Erlang Port Mapper Daemon](https://www.erlang.org/doc/man/epmd.html)) starts (if not already running) on every host machine with Erlang VMs. EPMD uses DNS to resolve node names to ip addresses and maps node names to ip+port (tcp socket). EPMD serves as a name resolver for nodes on the host machine.

You can also implement your own procedures for [nodes discovery](https://www.erlang.org/doc/apps/erts/alt_disco.html) and discovery under [container](https://contactchanaka.medium.com/erlang-cluster-peer-discovery-on-kubernetes-aa2ed15663f9) orchestrator.

Default Erlang distribution with cookies assumes trusted network so you should change default communication mechanism in case of untrusted network[^epmdless]. Moreover, large number of nodes with fully connected mesh communicating over large and uncontrolled network can be prohibitatively costly. This break point may range from 40 to 140 nodes[^erlang_scale] depending on load and amount of global state required to sync over cluster (such as a process namespace). In such cases [federated](https://www.infoq.com/presentations/erland-scale-10000-nodes/) clusters and storage of processes PIDs in data structures is a way to go.

**Erlang lessons to go distributed**:
* separation of concerns and modularity - you can configure your communication transport, algorithm of node discovery;
* communication is asynchronous so no node has to wait any acknowledgement that its message was received by another one;
* message passing is location transparent (the code to send message to local Erlang process is the same as for sending to a process on another node in the cluster);
* maintaining global data (namespace of lightweight processes in case of Erlang) and full connectivity severely limits scalability.

### Meta-language actor frameworks

Actors are units of computation communicating with each other via asynchronous messaging. 
Hewitt Bishop. Why Erlang not actor

### Reverse proxy

Nginx and modern alternatives like 
Patroni and others for DBs
Single point of failure. Or several points of failure.


### Container orchestration systems

Highly standardised way to ship software. Even most of the software written with a single node in mind can be turn into distributed.

### Unikernels under hypervisor

Such systems as Firecracker



#### Cluster OS

 Apache Mesos

[^klarna]: For example, Sweden fintech Klarna uses Erlang as it's core platform handling 2 million transactions per day - look at their [job descriptions](https://jobs.lever.co/klarna?team=Engineering).

[^my_experience]: At one of my work places I almost convinced my CTO to write IO-bound service for proxying media streams and unary requests in Erlang. For two weeks I read everything on Erlang and finally presented a workable version which was deployed to production. It had worked under production for about 3 weeks till the CTO had a look at the code. He was rather afraid of completely non-imperative style of code and inability to scale his Erlang team (consisted of only me). So he gently asked me to rewrite the app in Go. For those brave of you [Adopting Erlang](https://adoptingerlang.org/).

[^lumen]: Alternative execution environments (including not only VMs) emerge periodically but I haven't heard about any production-ready. BEAM is really [complicated](https://blog.stenmans.org/theBeamBook/) to reinvent. See also [Lumen](https://github.com/lumen/lumen).

[^erlang_limits]: See also [http://erlang.org/documentation/doc-5.8.4/doc/efficiency_guide/advanced.html](http://erlang.org/documentation/doc-5.8.4/doc/efficiency_guide/advanced.html)

[^rustler]: See [Rustler](https://github.com/rusterlium/rustler)

[^erlang_io_bound]: [Why we used Pony to write Wallaroo](https://news.ycombinator.com/item?id=15558051), [](https://stackoverflow.com/questions/32846615/what-is-the-best-way-of-doing-computationally-intensive-tasks-in-erlang-w-o-scal)

[^erlang_distribution]: [Distributed Erlang](https://www.erlang.org/doc/reference_manual/distributed.html)

[^epmdless]: See also [epmdless](https://github.com/tsloughter/epmdless), [Using TLS for Erlang Distribution](https://www.erlang.org/doc/apps/ssl/ssl_distribution.html)

[^erlang_scale]: See [Scaling Reliably: Improving the Scalability of the Erlang Distributed Actor Platform](https://arxiv.org/pdf/1704.07234.pdf) and [Stackoverflow question](https://stackoverflow.com/questions/5044574/how-scalable-is-distributed-erlang)
