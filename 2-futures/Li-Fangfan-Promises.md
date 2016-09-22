#Promises: Linguistic Support for Efficient Asychronous Procedure Calls in Distributed Systems

##Summary

This paper introduces a new data type called a *promise* that was designed to support asynchronous calls, which allows a caller to run in parallel with a call and to pick up the results of the call, including any exceptions it raises, in a convenient and type-safe manner.

##Main Contributions
There are several contributions made by this paper.

First, the paper describes the mechanism *promise*, which can retain the benefits of procedural interaction without sacrificing performance. In addition to this, *promise* does not require explicit send and receive primitives and is type-safe.

Second, the performance can be further optimized by forking new processes, while still obtain two most important property of *promise*, concurrency of caller and callee, caller control of claiming and putting promises in data structure.

Third, the paper introduces the notion of *coenter*, which can be used to group processes together or even subprocesses within a process together. *Coenter* can then guarantee that all (sub)processes in a group should be terminated together, which avoids the possibility of non-determination.

##Limitations
The first concern is the performance, since there is no evaluation, it is hard to convince the readers that the stream can actually improve the performance with all the atomic transactions, groups and guarantees made by the system.

The second concern is the waste of communication. In the example about recording and printing out grades, wouldn't it be a better idea if the recording machine can directly contact the printing machine, in other words, propogating the promises could significantly improve the performance since we reduce the number of communications made.

The third concern is about *coenter*, the linguistic mechanism to overcome the termination difficulties. It does help to group processes together, but the programmer has to be explicit about that, which requires the programmer to anticipate which processes should be grouped together in advance. This to me, can cause significant security issue since the fate of one process is somehow in the hand of another process, which requires the programmers to be extremely careful when actually implementing this.

##My opinions
The paper is very structured, and the ideas are naturally followed by each other along with the plenty of examples in each section, which gives a very easy read. Personally I believe if one task has to be finished in a distributed way, it is better done in a homogeneous environment, where propagating filters to the callee and pipelining the different stream could be more useful.