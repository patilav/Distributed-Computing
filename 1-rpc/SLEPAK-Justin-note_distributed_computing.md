The authors argue that the ideal typically summarized as "everything is an object"
(*i.e.*, structures containing private state and public behavior)
must be modified in order to be compatible with distributed computing.
The four problems identified as inherent differences between local and distributed computation are:

* Communication latency:
Design of software typically relies on the ability to quickly transfer control from one piece of code to another via procedure calls.
A latency difference of several orders of magnitude between local and remote calls means
programmers must be aware of which calls are remote,
even if this difference is not marked explicitly within the language.

* Address space separation:
Programs that work entirely locally are able to refer to all of their data by memory location if needed,
whereas a component in a distributed program may refer to an object that is located on a different machine or "owned" by a different process.

* Pervasive concurrency:
In a distributed setting, execution of one process is unavoidably interleaved or simultaneous with that of others.
With the communication happening through an unpredictable network,
timing of events triggered by that communication also becomes unpredictable.

* Partial failure:
When a local component fails, the programmer can rely on a runtime system, operating system, etc.
to give notification of at failure to other components that rely on it.
Remote components, by contrast, may fail for unknown reasons.
Exception handling and similar linguistic tools for error detection and recovery are not directly usable.

The first three are arguably less inherently different between local and distributed computing.

### Communication latency

The authors note that computation speed has increased over time
and suggest that it is not out of the question for communication speed to increase in the future too.
Over twenty years after the paper was written,
we can see that computation speed has been increasing faster than communication speed the whole time,
so latency is more of a cost problem than it used to be.
Even in less obviously "distributed" systems, such as a machine with a GPU,
invoking vast "remote" computing resources may not bring enough compute power to bear
to justify the time needed for getting data to device memory and back.
In many cases, it's faster to just perform computation on the CPU instead.

Latency still could be discounted as an inherent difference
because it does not really affect the *result* of a computation
in the way that the other differences might.
The "cost model" of a programming system is often thought of as a second-class characteristic,
dependent on implementation details to provide useful concrete knowledge about a particular piece of software.
Careful observance of the "everything is an object" doctrine can facilitate gradually transforming a slow, correct system into a faster, still correct system
by reimplementing individual components.
High-level design of a system can still be driven primarily by
what behavior the system will act out
rather than how fast it will act.

### Address space separation

Distributed computing introduces the possibility of scattering objects among several separate address spaces,
while a pointer (*i.e.*, a memory address) is still only valid in one particular address space.
The authors point out that hiding the distinction between local and remote addresses
requires a system that can interpose on all memory accesses.
Programmers would only be unable to get their hands on raw memory.
The restrictions described by the authors might have shocked many programmers at the time,
but twenty years after the release of Java,
programmers are probably more used to living under such rules.
Using a more abstract notion of "location" is also a conventional way to formally specify the semantics of mutable state in a programming language.
The mapping from logical addresses to machines may even be quite simple,
such as with a partitioned global address space.
With PGAS, the programmer is still aware of the difference between local and remote addresses
but is free to temporarily forget it when not dealing with performance-sensitive work.

### Pervasive concurrency

Concurrency arises from different objects across a network having their methods invoked without any possibility of centralized coordination.
Concurrency commonly appears in local programming today,
such as with a GUI relaying user input to some actively-running thread in the background
or a group of worker threads mediating providing access to some data structure they all share[1].
Even though local programmers now expect to deal with concurrency,
the distributed case is still distinguished from the local case by a lack of central control.
A handful of machines in contact with each other can coordinate their actions,
but only as long as all of those machines stay active and connected to each other.
Handling extreme situations with drastic measures such as a synchronization barrier
can be done in the local case,
but a global barrier in a distributed setting risks locking the entire system
if one machine should fall off the network at the wrong time.

### Partial failure

The more complex nature of distributed concurrency could be attributed to the possibility of partial failures in the system.
In strictly local programs, when a component fails, it visibly affects the other components that interact with it.
A process which tries to write to a filesystem on a storage device which has been physically removed
or to send a message to a thread that has crashed
can expect to be notified that the operation failed.
In the distributed case, there is no runtime or operating system able to watch the entire network for failing components.
One machine cannot tell whether another machine has fallen off the network,
or the connection to that machine is temporarily disrupted,
or a process on that machine is merely taking a long time to service a request.
There is no universally appropriate rule for when the runtime should report an error to a program without knowing for sure that an error has happened,
nor is there a universally appropriate rule for how best to recover from a possible error situation[2].
The distinguishing feature of the local case is that there is one entity capable of seeing the state of everything.

### What do we do about it?

"Everything is an object" is still a viable design philosophy in the sense of organizing software into separate components,
each responsible for some internal state of their own and exporting some well-defined behavior.
The programmer *does* need to be made aware of the difference between local and remote objects.
No one solution can make the network appear perfectly reliable,
ensure that machines never crash, etc.,
so the communication protocol used between machines must be chosen according to the particular system's needs
(*e.g.*, guaranteeing "at least once" delivery, or "at most once" delivery).
Once failure modes are taken into account, local and remote objects that nominally offer the same service
cannot truly provide the same interface.
Instead, a programmer who is aware of the difference can design an interface and mode of interaction that is informed by these constraints.


1: One of our students, Tony Garnock-Jones, is working on making this concurrency and communication more apparent to the programmer (and ideally more manageable) by making it an explicit part of the programming language itself.

2: Beyond detecting error situations, Birrell and Nelson even describe some measures they took to try to keep non-error situations from looking like an error happened.
