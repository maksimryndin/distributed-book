

Desired properties of consensus
* safety - never return an incorrect result under non-Byzantinne conditions (network problems,), all nodes agree on the order of events
* liveness - the consensus makes progress
* support cluster changes in cluster membership

Consensus protocols
* with elected leader (Raft, VR)
* leaderless (EPaxos)