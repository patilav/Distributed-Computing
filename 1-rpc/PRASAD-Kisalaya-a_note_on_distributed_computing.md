# A Note on Distributed Computing
Jim Waldo, Geoff Wyant, Ann Wollrath, Sam Kendall

## Summary
This paper discusses the drawbacks of the current (at that time) object oriented distributed systems that were built as an extension of local object oriented systems, without special considerations for some of the issues specifically pertaining to distributed systems, like latency and partial failures. The paper discusses some of these issues in details, gives an example of a system currently in production, NFS, and discusses what works and what doesn't in it, and closes with a discussion on what is needed at systems level and application level to make the distributed systems work.

## Assumptions
The paper is from early 90's and is a comment on the state of distributed object oriented systems then. Also, the paper only restricts itself to object oriented systems.

## Outline
The paper starts with stating that it is wrong to construct distributed systems by assuming that all objects are alike if they support a common interface. We then discuss about the difference between what the paper considers local computing versus distributed computing. The difference being the number of address spaces the system needs to access. The vision of unified objects is one where an object is defined in terms of interfaces in an Interface definition language, like CORBA. The programmer need not worry about the methods used to make the calls to the object, and it is the job of the system to do the locate where the object is, and marshaling - unmarshaling processes. This view assumes that the systems are constructed in an object oriented manner all the way down right to the system calls. 

However, according to the paper, historically this has never worked. The problem, according to the paper, is not getting things on and off the wire, but dealing with problems like latency, partial failures, concurrency and different memory access paradigms. 

### Latency
Latency is an obvious suspect, as the rates at which processor speed and network speed are changing is very unequal, and can not be ignored. This is the most obvious difference between a local and a distributed call, and looks to only get worse in the future.

### Memory access
The problems with the memory access comes down to the pointers, and how the pointers in one address space is not valid in some other address space. There needs to be a mechanism to control all memory access if the unified vison needs to be implemented. The enforcement and adoption of such a mechanism are the major issues.

### Partial Failure and Conncurrency
Partial failures in distributed computing are difficult to detect if only one of the participants fail and there is no common entity. Also, there is no way to determine the type of failure, as a network failure will look the same as a processor failure. The state of whole system needs to be consistant at all times. Partial failures leave the systems in a state of indeterminacy. 

We can design the interfaces in such a way that all the operations are deterministic when there is a failure, guaranteeing they can be used remotely. But this defeats the purpose of unified vision which states that there shouldn't be a difference between a remote and local invocation.

Cocurrency also poses similar set of challenges as a multi threaded system with an added weight of indeterminacy. 

### NFS
In the paper, NFS was presented as an example of a non distributed API (file system) re-implemented in a distributed environment. 
NFS introduced the concept of partial failure in a file system by introducing two modes for an inaccessible file server. Of these, soft and hard mounts, hard mounts are used more commonly where the applications wait for server indefinitely until it comes back up. This can lead to unexpected hanging of nodes in the network, unlike soft mounts where partial failures are exposed to users. Hard mounts are easier to deal with and hence are more popular.

### Conclusion
The paper proposes that instead of trying to merge the differences between local and remote objects, systems should be engineered in a way which acknowledges the differences between the two. This doesn't mean they cannot be defined using a IDL, but should include some additional info on the intended usage. The compiler for IDL can then take care of the generated objects. The application needs to identify the calls to local and remote objects, and handle them differently. 

## Limitations/Extensions
The paper is lacking in more real-world examples on how the unified vision is failing the distributed systems. This is understandable given the paper is from the early ages of distributed systems and is only dealing with systems which are modelled to be object oriented. Also, the solutions presented to current problems, and new alternative approach presented are a little abstract in nature.

## My Opinion

The paper does a good job at presenting the issues around the early object oriented distributed systems and proposing some solutions. It is easy to understand the drawbacks of unified vision of objects. However, the solutions presented are a little abstract in nature, and it is unclear on how to implement it in practice.


