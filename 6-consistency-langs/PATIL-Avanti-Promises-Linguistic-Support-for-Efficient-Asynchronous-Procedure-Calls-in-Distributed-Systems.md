<meta charset=utf8>
# “Lasp: A Language for Distributed, Coordination-Free Programming”, Christopher Meiklejohn, Peter Van Roy; 2015
Avanti Patil <patil.av@husky.neu.edu>

## Synopsis
World is moving away from synchrony. Why? Dependencies are slowing things down and in 20'th century, 
world can't accept that. So we move towards more asynchronous options.  `Lasp` is a new programming model
designed for distributed, Coordination-Free Programming for large-scale distributed programming environments.
Lasp provides support for computations where not all participants are online together at a given moment.
 It is build as an `Erlang` library on top of the Riak Core distributed systems framework. 

## Summary
Lasp model provides the ability to use operations from functional programming to deterministically compose CRDTs into larger computations that observe the SEC property; these applications support programming with data structures whose values appear non-monotonic externally, while computing internally with the objects’ monotonic metadata.  Lasp is built on Derflow and DerflowL to provide a distributed, fault-tolerant variable store powering a deterministic concurrency programming model.

Motivation?
1.	Coordination of shared state, such as Paxos and state-machine replication, grow in complexity with partial replication, dynamic membership, and unreliable networks. 
2.	Allowing offline editing of data puts burden of ensuring consistency for a programmer
3.	Conflict-Free Replicated Data Types (CRDTs), pro- vide a property formalized as Strong Eventual Consistency. Strong Eventual Consistency (SEC) compliant distributed systems no longer place the conflict resolution logic in the hands of the programmer, but arbitrary compositions of these data types is non trivial according to paper reviewed this week `Consistency without borders`. 

`Observed-Remove Set CRDT` (OR-Set), which will be used as the basis for the formalisms in this paper.

Future: We plan to extend our current prototype into a general-purpose language in which synchronization is 
used as little as possible.


## Analysis




## References



