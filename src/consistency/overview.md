Martin Kleppman Critique CAP

latency-sensitivity framework

availability as a metric

operational and network latency

algorithm sensitivity to latency

communication patterns
- unicast (receiver - sender)
- broadcast (sender to all possible receivers)
- multicast (sender to a selected group of receivers)
- convergecast (a group of senders to a receiver)

Supermajority (2/3) vs simple majority

Network partitions *can be modelled as large packet delays (up to the duration of the partition), provided that the duration of the partition is finite and lost packets are retransmitted an unbounded number of times*[^kleppmann].


[^kleppmann]: Martin Kleppmann [A Critique of the CAP Theorem](https://arxiv.org/abs/1509.05393)