
A several nodes in a distributed system should agree on a value. More formally, a set of replicated state machines computes the same state by applying commands from a replicated log. And the consensus goal is to synchronize the log for every node.

Distributed systems can be classified by different criteria which have both theoretical (as a constraints for models) and practical (as foundations for design decisions) importance. Let's list mostly used:

1. *By a communication time bound*.

    **A synchronous system** assumes that there is some bound (known in advance) on the delivery time of messages between nodes. 

    **An asynchronous system** assumes that there is no such a bound (it is impossible to say either a node has failed or is slowly processing). An asynchrounous system seems to be more suitable for nodes interacting via networks but there is no known consensus for such a model (see below).

2. *By a fault mode*. 

    **Non-faulty** nodes do not crash or misbehave at all.

    **Fail-stop** nodes stop when fails/crashes and do not restart.

    **Fail-recover** nodes recover after crashes and behave correctly after the restart.

    **Byzantine-faulty** nodes may crash intentionally and moreover misbehave to hamper non-byzantine nodes to achieve consensus.

3. *By reliability of communication (transport)*.

    Systems with **a reliable transport** where sent messages are finally delivered (e.g. stream-oriented protocols like TCP, HTTP2, QUICK)

    Systems with **an unreliable transport** where some sent messages can be dropped while in transit (e.g. UDP)


All type under each criteria are listed from the most restrictive to the most general. As a consequence, if something holds for a more resrictive subtype, then it holds for a more general one. Why is this reasoning important? Let's illustrate with some impossibility results.

Some impossibility results (when consensus is impossible to achieve):
* **FLP**: *in an asynchronous disributed system with a reliable communication and fail-stop nodes a consensus is impossible*. As a consequence, the statement holds also for asynchronous systems with fail-recover or byzantine-faulty nodes with a reliable communication (or with an unreliable communication).
* In a presence of an unreliable transport a consensus is impossible even for a synchronous system (TODO link?)
* consensus impossible even in a synchronous distributed system when BFT faults are possible and one third or more processes fail (N <= 3F) Lamport Pease 
* Atomic broadcast and its duality to consensus

How to deal with such impossiblity results?
* in theory - additional constraints are introduced - the network will be periodically synchronous for short intervals of time and in such intervals of synchrony, all undelivered messages will be delivered under some fixed time bound - see [DLS88] C. Dwork, N. A. Lynch, and L. J. Stockmeyer. Consensus in the presence of
partial synchrony.
* in practice - by using *failure detectors*. The most simple one is a periodic healthcheck (when a communicating node checks) and heartbeats (when the node itself emits).

Chandra: accuracy vs liveness of failure detectors
lease and leased clocks

TODO Consider 2PC (2-phase commit) for synchronous non-faulty system with a reliable transport as a first step
If introduce fail-stop nodes (e.g. coordinator itself) than consensus will halt with 2PC.


Desired properties of consensus
* safety - never return an incorrect result under non-Byzantinne conditions (network problems,), all nodes agree on the order of events
* liveness - the consensus makes progress
* support cluster changes in cluster membership

Consensus protocols
* with elected leader (Raft, Paxos, Tendermint, pBFT, VR, Nakamoto consensus - POW, POS, POA)
* leaderless (EPaxos, Avalanche, IOTA)

* bft-tolerant (e.g. nodes never assume the leader is correct or honest) PBFT, Nakamoto, Tendermint
* non-bft tolerant (require trusted setup - Raft, VR, Paxos)

CHandra - atomic broadcast is dual to consensus