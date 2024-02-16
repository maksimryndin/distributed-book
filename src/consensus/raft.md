# Raft

Raft is now one of the widely used consensus algorithms. For example it is used in Etcd (link to the established systems) and TiKV.

The animated introduction is [here](http://thesecretlivesofdata.com/raft/) should be checked first, and after that the [article](https://raft.github.io/raft.pdf).

Key takeaway
* we should try to merge results from equal peers (like in Paxos?), it is much more simplier to elect a leader and make it responsible for the state changes
* modular design: separate leader election, log replication, safety and membership changes