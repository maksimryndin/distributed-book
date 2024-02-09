# Time

Ordering of events and linearizability

LOgical clocks (events are concurrent just because two processes do not know about each other till some messages exchange occurs even the events have some *physical time* ordering)
wall clocks are never perfectly synchronized

Async vs sync system
Reliable vs unreliable (fault models) system

Synchronous system assume that there is some bound (known in advance) on the delivery time of messages between nodes.
Asynchronous system assumes that there is no such a bound. Asynchrounous system seems to be more suitable for nodes interacting via networks but there is no known consensus for such a model.

So partial synchronous model[[DLS88] C. Dwork, N. A. Lynch, and L. J. Stockmeyer. Consensus in the presence of
partial synchrony.] is used 

a distributed system is asynchronous if there is no bound on message delay, clock drift, or the time
necessary to execute a step. Thus, to say that a system is asynchronous is to
make no timing assumptions whatsoever (Chandra)

Full connectivity (all nodes are connected) vs partial
With Byzantine faults vs without

Liveness property - the consensus makes progress
Safety property - the consensus behaves correctly (all nodes agree on the order of events)

Fault modes
Time synchronization
Fault detection

Framework with pluggable fault modes, fault detection algorithms, consensus protocols?


[^lamport] See the seminal paper [Time, Clocks, and the Ordering of Events in a Distributed System](https://amturing.acm.org/p558-lamport.pdf) by Leslie Lamport 