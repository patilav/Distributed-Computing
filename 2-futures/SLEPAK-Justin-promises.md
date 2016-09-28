### Promises

Prior work developed "futures" as a datatype representing a possibly-incomplete asynchronous procedure call.
Futures allow roughly uniform treatment of data we have and data we *will* have.
This makes it easy for a programmer to act like it was a synchronous call, until the data is actually needed for something.
This paper develops the remote/distributed case of futures, called "promises."
A promise wraps a remote call like a future wraps a local call.
It can change transparently from "blocked," meaning the result is still being computed, to "ready," meaning we have a result.
A program that has a promise can "claim" it, blocking until it becomes ready.
Claiming is idempotent: once a promise changes state to ready, its state will always be ready.
A ready promise may produce an exception instead of a proper value
Asynchrony means we can generalize from traditional remote procedure calls to "call streams."
Generalizing from local-only futures to remote promises needs to add new "failure" possibilities.
The implementation of promises must also respect address space separation.
These differences aside, both futures and promises are fundamentally about enabling a certain program organization that RPC prohibits.

### Call streams

Call streams generalize RPC by allowing one entity to issue many remote calls and do some local computation before actually collecting the result from any remote call.
The underlying system ensures that one entity's calls are delivered to the remote callee in the order they were made,
so that their side effects are ordered as the client would expect had the calls been local.
There is no such ordering guarantee about separate entities' calls to the same callee
or about one entity's calls to different callees.
Doing so would require global coordination across the entire network,
which is very expensive under ideal conditions and impossible in the face of networking failures.
The system also guarantees that the results of one entity's calls are returned from the callee in order.
The authors point out that this guarantee is not strictly necessary
(a programmer could design services to package enough extra information to associate a result with a particular call),
but it is in keeping with their goal of usability.

The program interface for making streamed calls, referred to as a "port," has type information
explaining what data may be sent through it by the caller and callee and what exceptions may be raised by calls.
Several ports can be gathered together as a "group,"
and their remote endpoints will be considered a single callee entity for call ordering purposes.
On the server side, each remote-callable service is associated with a "handler,"
which has a port to receive calls and a procedure which processes them.
The runtime system will not invoke the handler until all earlier calls on that port are done,
so that the call ordering guarantee is satisfied.

When a program which makes a stream call, control is immediately returned to the caller,
with a promise as the returned value.
The promise functions as an IOU:
the caller has a token that other functions can accept and `claim` as needed.
If the stream call is made as a statement rather than an expression
(*i.e.*, the result is not bound or assigned to anything),
the promise need not actually be constructed.
Maximising utilization of the network calls for issuing many remote calls in succession,
which is made possible by the immediate return of control.

A stream can also "break" under certain error conditions.
If there are communication problems, they can bubble up to the user program level as an asynchronous break.
No specific stream call is identifiable as the cause,
but calls which have already been issued do not have guaranteed outcomes.
A sender or receiver can also induce a synchronous break on a call.
This is useful if a service is being intentionally taken down
or if the data being passed to a remote entity is not transmittable
(this may be due to buggy marshalling code).
A broken stream can be recreated later.

The programmer can exert a bit more control over the networking logic by explicitly flushing a stream.
Doing so forces all of the local runtime's buffered stream calls and remote runtime's buffered returns to be sent.
Beyond that, the programmer can synch a stream,
which flushes it and then blocks the caller until all already-made calls on the stream have completed.

### Programming with call streams

The authors step through a sequence of design revisions on an example program
to demonstrate the flexibility promises add over RPC
and the current limitations of promises as a programming construct.
In the running example, the goal is to update a list of remotely-held objects (issue grades on a new assignment to students)
and then perform some computation on the updated objects (print the students' new average grades).

The initial version of the program first loops over the local assignment grades array,
issuing a new grade creation remote call on each one.
The local averages array is now populated with promises, in the same order as the calls were made.
Then the grade recording stream is flushed, ensuring all requests have actually been sent before continuing on to printing.
The printing loop traverses the averages array, constructing a string for each average grade
and sending it off in a stream call to the print port.
Finally, the print stream is synched to ensure that everything actually has printed before the program exits.

Frustratingly, the order in which calls are made by this program affects the opportunity for overlapping the servicing of those calls.
The server cannot begin printing any grade until it is finished updating all of them.

Promises are also usable like futures in that they can wrap a not-yet-completed *local* call.
A local forking mechanism allows non-remote procedures to run asynchronously,
and a promise is still a convenient way to handle the result of such a call.

### Composition

Ideally, we would like to chain call streams together like a Unix shell pipeline,
with one stream's results fed immediately into calls on the next stream.
This would expose all of the available parallelism by invoking a task as soon as its input data is available.
Stream composition differs slightly from Unix piping in that the result is given back to the initial caller
so it can be prepared for the next stream by some local computation.
Requiring immediate forwarding of results to the next stream would force that intermediate processing to be done via unnecessarily expensive remote calls.
The job of routing data from one stream to the next and scheduling these calls can be simplified by having a local fork to manage each stream.
Then calls can be issued concurrently on each stream, as soon as their data is ready.
The resulting promises are handed off to the fork responsible for the next stream.

An updated version of the grade recording program using local forks is able to issue print calls interspersed with grade update calls.
The two forks themselves communicate via a queue of grade update promises,
which keeps the printing fork from outrunning the updating fork.
Since both forks synch their respective streams before exiting,
the parent can be certain that all calls have completed successfully after it successfully claims the forks' promises.
Unfortunately, in an error situation, the printing fork may wait forever trying to dequeue the next promise from the updating fork.

A collection of processes responsible for collaboratively using a collection of streams ought to be terminated as a group if there is a serious error,
rather than allowing processes downstream of the error to try and fail forever to do useful work.
A "coenter" form creates several subprocesses which have their status linked
so that one which encounters an error and exits the coenter also terminates its siblings.
Siblings that are performing interruption-sensitive operations on some shared data
can declare that it is in a critical section.
The runtime will allow the process to finish its critical section before terminating it,
but the process is prohibited from making any further remote calls
to "encourage" it to exit the critical section.
If a sequence of remote calls absolutely must be made all at once or not at all,
they can be grouped into an atomic transaction.
Critical sections and atomic transactions are useful for ensuring safety regarding abnormal termination of a coenter,
but as language constructs they are not specific to coenters.

The grade recording example is rewritten using a coenter,
where linking the two forks' lifetimes ensures that if the updating fork does trigger an error,
the printing fork will be terminated appropriately.

### Limitations on composability

Forcing a pipeline to relay every intermediate result back to the client brings an efficiency penalty.
However, there is no clear way to serialize and send instructions for processing an intermediate result before handing it to the next server.
Similarly, a promise representing an obligation from one remote call cannot be transfered to another remote procedure.
Using a promise as a "bearer instrument" does not work in a system where the protocol says that an entity obliged to fulfill a promise sends its data to the entity that originally held the promise.
Routing returned data to whichever entity happens to hold the promise at the moment would be too difficult.
The authors mention the possibility of making stream composition a direct operation in the language,
but they believe the more general coenter gets close enough
without incurring the cost of adding yet another language construct.

### Further thoughts on local fork

The initial sample program overspecifies the ordering of events:
All new grade updates must be processed before anything can be printed,
and grade updates themselves must be carried out in a particular order even though they operate on separate data.
Rather than relaxing the delivery order guarantees provided by the networking system,
something like the authors' local forking mechanism might allow clients to relax the order of their own calls as they see fit.
If separate local forks could be viewed as separate entities by the network manager,
their calls could be reordered in transport in whatever way is convenient under current network conditions.
Otherwise, even though individual stream calls would themselves become reorderable,
only the local runtime would have the freedom to reorder them.
The network would be required to respect the order arbitrarily chosen by the local runtime.
