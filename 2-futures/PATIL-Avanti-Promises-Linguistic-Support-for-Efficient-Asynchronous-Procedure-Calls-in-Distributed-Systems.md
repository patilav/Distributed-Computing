<meta charset=utf8>

# "Promises: Linguistic Support for Efficient Asynchronous Procedure Calls in Distributed Systems", Liskov, Shrira, Jun 1988

Avanti Patil <patil.av@husky.neu.edu>

## Synopsis
This paper deals with the concept of `Promises`: a new data type designed to support asynchronous remote procedure call mechanism to be used by components of a distributed program. It briefly describes `call-stream`: language-independent communication mechanism and examples of streams integrated in Argus programming language. The paper further goes on to describe how promises can be used with forks of local processes and compare mechanism presented in paper to related ones in other languages. It ends with brief on stream composition and evaluation of presented mechanism.

## Summary
This paper describes `Promise` as a data type which was developed after getting motivated by a communication mechanism called `call-stream` which was invented so that programs can use components over a network in a heterogeneous computing environment. `Call-streams` combine advantages of (1) Remote procedure calls and (2) message passing. `Promises` were introduces to preserve the merits of organizing programs using procedures and procedure calls without sacrificing the performance benefits of call-streams. Some attributes of Promises are (a)Strongly typed (b) allow the result of a call to be picked up later (c) allow exceptions from the called procedure to be propagated in a convenient manner. (d) handle node failures and network partitions using call-stream mechanism. 

Call-streams are a language-independent communication mechanism. `Call-stream` vs `RPC`: (1) Call-streams allows the caller to run in parallel with the sending and processing of the call (2) Call-stream reduce the cost of transmitting the call and reply messages by buffering technique unlike RPC which send messages immediately. In call-stream a receiving entity provides one or more named ports; which are strongly type and identify procedures that can be called from other entities. Exception handling is used as a termination model to avoid abnormal termination. Arguments and results are passed by value. Groups of ports which belong to same entity define the receiving ends of streams which are sequenced. Deadlocks are handled by no concurrent activity within an entity i.e. separate activists should not share the same stream. Agents define the sending ends of streams. An agent and a port group together define a stream. If the system is unable to live up to the guarantees, it breaks the stream. Since streams are based on buffering two important primitives provided to sender are `flush` and `synch` for all calls.

This paper describes the streams within a programming language, Argus. Argus supports RPCs and implements call-streams using similar terminologies. Guardians are active entities which resides entirely at a single node of a network and handlers that can be called by other guardians. Argus computations run as atomic transactions. 



## Analysis

This paper was published in 1988 by the Advanced Research Projects Agency of the Department of Defense.  This shows that distributed computing had undergone quite a few changes from remote procedure call and message passing from 1982 since the first conference Symposium on Principles of Distributed Computing (PODC) took place [1]. As distributed computing continues evolving several problems surfaced like reduced performance in remote procedure calls due to waiting in caller. Call-streams were invented to take advantage of remote procedure calls and message passing.  Design of Promises is based on the concept called futures which was introduced in Multilisp has in 1985. Futures in Multilisp were used to create tasks and synchronization of tasks. 

 `Promise` data type was developed after getting motivated by a communication mechanism called `call-stream` and extended `futures` introduced in Multilisp to be used in a heterogeneous computing environment.

After carefully analyzing the delay introduced in RPC for processing each call buffering messages will save lots of time for call-streams, especially for small calls and replies. 



## References
[1] ACM Symposium on Principles of Distributed Computing: http://www.podc.org/history/

[2] Halstead, R. “Multilisp: A language for concurrent symbolic computation”. ACM Trans. on Programming Languages and Systems 7, 4 (October 1985).


