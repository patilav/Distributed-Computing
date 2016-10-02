### Design intent

Erlang is a language designed to make heavy use of concurrency.
The original, concrete motivation for Erlang's design was building a telephone exchange,
which was specified to be down for no more than four minutes per year
(a bit over 5 nines of uptime).
The stringent uptime requirement meant that updates to system software could not require stopping the exchange.
The key design idea that enables both fault tolerance and hot-swapping new code in
is splitting the system into isolated processes.
Each process has its own local data, which is inaccessible to all other processes.
Rather than modifying shared data objects,
Erlang processes are only able to communicate by sending each other messages.
Isolating part of a system as its own process allows replacing it promptly when it crashes
(whether due to their own logic errors, failures in underlying system software, or in the hardware itself),
rather than letting the failure propagate to related parts of the system.

### Programming style

The conventional technique for handling processes that fail is
for each worker process to be observed by a supervisor process.
The supervisor "links" itself to the worker so that it will be sent a message if and when the worker dies.
A process can link to a process on another machine[0],
so they can watch for hardware failure as well as logic errors.

To encourage the programmer to split the system as finely as possible into processes,
process creation and cleanup is made to have low syntactic and performance cost.
Performance is handled by not relying on the underlying operating system's multithread or multiprocess support.
Thousands of short-lived Erlang processes can coexist without running into any system-imposed limit on the number of OS processes
(which must be limited because they are far more expensive to create and maintain).
Under this cost model, the programmer is encouraged to treat spawning a new process with about as much gravity as calling a function or constructing an object.

Erlang offers tuples and lists, which can be used to build more complex data structures.
All data is immutable.
A process is a sequence of instructions,
typically retrieving messages which match a certain pattern from the mailbox
and then acting on those messages.
If a process tries to receive a message that has not yet arrived,
it will block until it does.
A process may also do some computation on local data or spawn new processes.
Looping is done by spawning a new process to execute the next loop iteration
(which itself ends with spawning the *next* loop iteration's process, and so on).
This pattern is effectively a tail call,
which can be compiled to just a jump back to the beginning of the loop.

The programmer is also encouraged to simply let processes crash whenever they run into any sort of trouble.
Doing so is no more drastic an act than a function raising an exception:
some entity was already designated as the handler for this error case,
and we simply transfer control of the situation to that handler.

### Practical use

The article demonstrates upgrading code while the system is running.
A process acting out an event loop is created with an event handler function.
On receiving notification of an event, it invokes the handler and then starts the next loop iteration.
If it instead receives a "new handler code" message,
it immediately spawns the next loop iteration's process using the new handler instead of the old.

It is also possible to implement transactions,
where a sequence of updates to some data must either all occur or all get canceled.
The lack of mutable data means the pre-update version of a data structure can easily be kept around to handle transaction failure.
Since all data is private and owned by a specific individual process,
that process can serve as a monitor to enforce synchronization (such as mutual exclusion)[1].

Most of the article's example code is too simplistic for actual use in a high-availability system.
Instead, the Open Telecoms Platform, a collection of libraries distributed with Erlang,
package up canonical solutions for many situations.

The article also describes several products built with Erlang.
These include a network switch, instant messaging applications, and databases.
The common thread in these use cases is that the core task is transmitting serialized data over a network in response to requests from many remote users.
Schema-free databases in also benefit from combining Erlang's comparatively flexible data model with its facilities for fault tolerance.


0: This promise implicitly assumes that the Erlang runtime itself is capable of delivering messages about remote failures.
The article does not describe how this is implemented.

1: A mailbox is still a data structure that everyone is free to write to.
The article does not discuss synchronizing the sending of messages to a particular process,
but this problem may not have come up in practice.
