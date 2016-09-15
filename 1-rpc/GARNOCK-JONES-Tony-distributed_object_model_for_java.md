<meta charset=utf8>

# “A Distributed Object Model for the Java System”, Wollrath, Riggs, Waldo; USENIX June 1996

Tony Garnock-Jones <tonyg@ccs.neu.edu>

## Synopsis

The authors describe a distributed object model (“RMI”, Remote
Method Invocation) and its integration with the Java local object
model. They touch on aspects of the computational model visible
to the programmer as well as deeper aspects of the
implementation.

## Summary

The paper opens with an overview of the motivations for a high-level
approach to interprocess communication (IPC) and of the goals of the
specific design to be presented, “Remote Method Invocation” (RMI).
Briefly, these are to offer “seamless” and “natural” integration of
the distributed model with the existing local model, while
simultaneously (a) making differences between the two models apparent,
(b) minimizing complexity for the programmer, and (c) preserving the
safety properties offered by the Java system.

The paper briefly describes the Java local object model, fairly
new at the time, before moving on to the distributed object
model. The distributed model is introduced by presenting the
mapping of service implementations, protocols and interfaces, and
service references onto Java language features such as classes,
interfaces, and object references.

The paper continues with a discussion of three features of the local
model that have non-obvious interactions with corresponding features
of the distributed model. Firstly, the local model requires that the
built-in methods on class Object be implemented, including the
“equals” and “clone” methods which have no obvious meaning in a
distributed setting. RMI implements “equals” on references to remote
objects as reference equality, and “clone” as cloning the reference,
not the underlying remote service object. Secondly, the local model
uses call-by-value exclusively, with values being either primitive
data or references to (mutable) Java objects. The remote model, by
contrast, copies Java objects across the network when they are not
explicitly marked as RMI server objects; this has severe implications
for notions of equivalence and the scope of mutability. Finally,
Java's local synchronisation and locking mechanisms are not extended
to a distributed locking implementation; uses of the “wait” and
“notify” methods on an RMI object reference affect the local reference
only.

Following discussion of the most immediately apparent aspects of
the distributed model, the paper continues with a description of
four deeper aspects of the architecture and implementation of the
system. Firstly, the layered structure of the RMI system is
presented, including discussion of the Remote Reference Layer
which offers an extension point for mediating interaction between
RMI clients and RMI server objects. Secondly, the distributed
reference-counting garbage-collection algorithm used by RMI is
described. Thirdly, the ability of RMI to load code from across
the network while preserving type safety is discussed. Finally,
the interaction between the distributed model and Java's approach
to wider security questions is touched upon.

## Analysis

The contributions of the work are:

  1. The design of a layered architecture that potentially allows
     “transparent” addition of support for features such as replicated
     service objects and dynamically-activated persistent object
     references.

  2. An indication of the benefits gained from limiting the scope
     of the design to distributed interaction between programs
     written in a single, specific programming language with
     interesting features.

In these ways, the paper goes beyond prior work such as CORBA and
Modula 3's “Network Objects” system. RMI's Remote Reference
Layer, which earlier systems lack, potentially allows interaction
patterns other than the traditional request/response/error. The
assumption of linguistic homogeneity, present in Modula 3 but
very much not in CORBA, allows RMI to exploit Java-specific
features: the ability to load code at runtime gives the ability
for RMI to preserve type safety while not limiting dynamic
addition of new behaviors to a running system; the serialization
facility gives RMI a uniform approach to data transmission across
the network; and support for object finalizers and weak
references gives the necessary hooks for RMI to offer guarantees
about distributed garbage collection.

That said, this paper was written in 1996, just two years after the
influential “note on distributed computing” [1] which shares two of
the present paper's authors. This paper makes occasional nods to the
“note” throughout, going so far as to make it an explicit goal to
“make differences between the distributed object model and the local
Java object model apparent” to the programmer. However, it does not
(and perhaps cannot) fully engage with the issues brought up in the
earlier paper. As such, the paper is more a record of a practical (and
popular) engineering development than a direct contribution to the
theory of distributed language design.

In fact, with hindsight, we might see an implicit contribution: the
paper indirectly highlights the difficulty of `bolting on' a facility
for distributed computation to an existing non-distributed language.
These difficulties can be clearly seen when viewed through the lens of
the “fallacies of distributed computing” [2]:

  *The network is reliable.* RMI's model of failures in a distributed
  system is very simple: it sweeps all non-local failures together
  into a single exception class, “RemoteException”. However,
  programmers need to respond in different ways to different kinds of
  failures, and it is not clear from the paper that RMI takes steps to
  allow, say, a transient failure to be distinguished from a permanent
  failure. In addition, “RemoteException” propagates visibly
  throughout any code that uses remote object features: it becomes
  impossible to write code that is “polymorphic” in terms of whether
  the objects it delegates to are local or remote. The dynamism of the
  object graph in object-oriented languages means that the scope of an
  exception handler is not a good fit for the scope of failure in a
  distributed system.

  *Latency is zero.* The paper does not acknowledge network latency
  at all. A brief look at other sources of information on RMI
  suggests that various approaches exist to introducing
  TCP-level timeouts, for example, but the need for finer-grained
  control over request latency is not recognised by RMI.

  *Bandwidth is infinite/Transport cost is zero.* The paper does
  not acknowledge network bandwidth at all. RMI's approach of
  serializing all objects could potentially consume a large
  amount of bandwidth. No control over serialization decisions is
  offered to the programmer other than the option of marking a
  class as “remote”.

  *The network is secure.* The paper does not mention transport
  security or issues of authentication or authorization. It does
  discuss aspects of the Java local security model relevant to RMI,
  but does not mention issues particular to security in a distributed
  setting. Java itself violates the useful maxim of “only connectivity
  begets connectivity” [3], making the mobile code and lack of trust
  boundaries in RMI challenging to properly secure.

  *Topology doesn't change.* The paper describes no facility for
  working with changing network topologies. While it is possible
  that the described Remote Reference Layer could be used to give
  some control over reacting to topology changes, it is clearly
  future work, at least from the perspective of 1996.

  *There is one administrator.* The paper mentions the use of name
  servers for discovering object references, but does not discuss
  administrative concerns over name allocation and management,
  and does not discuss the possibility of multiple administrative
  zones or trust boundaries in a single distributed system.

  *The network is homogeneous.* While this assumption is named a
  fallacy by Deutsch, it is here taken as a foundation for the
  design of the system: RMI derives many interesting benefits
  from the assumption of a homogeneous network. However, it is
  interesting to note that later iterations of the RMI system
  became increasingly closely integrated with CORBA, weakening
  the benefits available to systems implementors from a Java-only
  design.

RMI is primarily a unicast, request/response/error style system.
That is, to both clients and service implementations, the
interface between local objects and the RMI system is normal
method call, return, and exception signalling. The paper
introduces the Remote Reference Layer which offers extension
points for adding new interpretations to this protocol, giving
the example of the potential to add support for object
replication via multicast communication. However, moving from
unicast to multicast introduces new possibilities for failure
that need to be handled in application-specific ways: if a single
replica, for example, can't be reached, should that cause failure
of a request or not? If a single replica becomes slow, does this
cause the entire system to become slow? If the network develops
split-brain, what are the consequences? How should any of this be
signalled to requesting clients? And so forth. Given these
questions, it is not clear that the Remote Reference Layer is
flexible enough to accommodate such a significant change to the
model.

The semantics of Java are exclusively call-by-value, and its data
types are primitive values and object references. When an object is
given as an actual parameter to a method, a reference to the object is
given to the method executed. Objects in Java are mutable, and
modifications made to an object within the dynamic extent of a method
remain visible to other holders of references to the same object after
the method has finished executing. This property of Java is violated
by RMI, which copies objects across the network unless they are
specially marked. At the time of the copy, the identity of the copied
object is lost: the copy and the original are free to evolve
independently. The programmer has no control over this process (other
than the ability to mark concrete classes as being full-fledged
distributed objects subject to RMI's distributed garbage collection
system). This makes a very visible `seam' at the boundary between
distributed and non-distributed code: programmers must think quite
differently about the notions of equivalence they are relying upon in
each case [4]. In addition, the lack of control over object copying
could lead to unexpected bandwidth use unless applications are
carefully designed for RMI; a corollary of this is that adding RMI to
an existing codebase may not be a simple change.

Finally, a minor remark: the paper notes that “only those methods that
appear in a remote interface [...] can be accessed remotely”. This
effectively introduces a new visibility level to Java. Locally, the
existing `public`, `private`, `protected` and package-scope visibility
levels apply, while remotely, visibility is entirely controlled by
presence or absence of a method in a particular interface.

## References

[1] J. Waldo, G. Wyant, A. Wollrath, and S. Kendall, “A Note on
Distributed Computing,” Technical Report SMLI TR-94-29. Sun
Microsystems Laboratories, Inc, Nov-1994.

[2] <https://blogs.oracle.com/jag/resource/Fallacies.html>

[3] M. S. Miller, “Robust composition: Towards a unified approach
to access control and concurrency control” (PhD dissertation),
Johns Hopkins University, 2006.

[4] H. G. Baker, “Equal rights for functional objects or, the
more things change, the more they are the same,” ACM SIGPLAN OOPS
Messenger, vol. 4, no. 4, pp. 2–27, 1993.
