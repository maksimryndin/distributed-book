# Time

Ordering of events and linearizability

LOgical clocks (events are concurrent just because two processes do not know about each other till some messages exchange occurs even the events have some *physical time* ordering)
wall clocks are never perfectly synchronized

Async vs sync system
Reliable vs unreliable (fault models) system

a distributed system is asynchronous if there is no bound on message delay, clock drift, or the time
necessary to execute a step. Thus, to say that a system is asynchronous is to
make no timing assumptions whatsoever (Chandra)

Full connectivity (all nodes are connected) vs partial
With Byzantine faults vs without

Fault modes
Time synchronization
Fault detection

Framework with pluggable fault modes, fault detection algorithms, consensus protocols?


[^lamport] See the seminal paper [Time, Clocks, and the Ordering of Events in a Distributed System](https://amturing.acm.org/p558-lamport.pdf) by Leslie Lamport 