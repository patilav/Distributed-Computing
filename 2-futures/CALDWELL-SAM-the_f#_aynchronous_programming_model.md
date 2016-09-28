# The F# Aynchronous Programming Model by Don Syme, Tomas Petricek, and Dmitry Lomov; PADL 2011

## Summary

Concurrent programming with OS threads has well-understood drawbacks
that can be escaped (possibly traded for a different set of drawbacks)
by switching to event-driven programming. Consequently, languages and
platforms are switching to a more event-based, asynchronous model of
concurrency. In this paper the authors provide a high-level overview
of the linguistic support for asynchronous programming in F#.

F# is extended with asynchronous expressions, `async { aexpr }`. The
inner expression is in the *asynchronous modality*, meaning that
control constructs are given "a different computational
interpretation". In practice what this means is that the syntax of
async expressions includes that of regular expressions plus a few new
expressions and library functions providing a monadic interface. The
"reinterpretation" is given by CPS-conversion. Intuitively the
transformation can be motivated by considering a program about to
block on an event. Instead of halting the thread, it can instead
capture its continuation and then register the continuation and event
with a global registry that knows when the event fires to resume the
continuation.

The design additionally provides methods for dealing with resource
cleanup, failure handling, and cancellation. The `use` construct
(similar to `with` in Python) provides deterministic, lexical resource
teardown. Traditional `try` blocks catch exceptions. A cancellation
token is automatically threaded through asynchronous code and is
checked at certain points, allowing sending a "stop" signal to async
computations.

The paper sketches the operational semantics of the system. First, as
mentioned, async code is CPS-d into functions with three continuations
(success, exception, cancellation) and a cancellation token. The
runtime system has three components: a set of active compuations, a
set of queued computations, and a set of pending reactions. Pending
reactions are event-continuation pairs that will be ready to run when
the event fires. When the event fires, they can move to the queued
compuations which are all ready to run.

The paper presents an evaluation of the design where the same
programmer completes a task using threaded (old method) F# and then
async F#. The programmer completed the task with async support in less
time and the same amount of code. The performance of the system in a
simple ping-pong messaging benchmark is compared to similar systems in
Scala and Erlang and found competitive.

## Analysis

My experience programming on a team with C# is that the design
reported in this paper (which was subsequently ported to C#, I
believe) is useful for programmers. More specifically the ability to
write compositional synchronous-looking code that is executed
asynchronously is quite valuable, as well as side-stepping the issue
of getting bogged down by what might be perceived as "highfalutin"
ideas like continuations and monads.

I found myself wanting more comparison to other language-based support
for event-driven programming in the related work, specifically with
regards to CML. Since I'm not a CML expert I'm having a hard time
trying to figure out what the differences between the two systems are.

The allusions to the corresponding asynchronous support in C#
throughout the paper are distracting and confusing. The correspondence
is never explicitly given. Having done some C# programming my loose
understanding is that the same system has been ported to C# and this
is what the authors mean. It gets hairier in the evaluation section,
though. The programmer mentioned above also completed the task using
C# with threads and C# with asynchronous support. This time, though,
the results were less favorable: asynchronous took considerably more
lines of code and time to complete. Why do the C# evaluation, include
the results, but then not address them?

At times the presentation descends unnecessarily into low level
details of the F# environment. One example is noting that "`Agent<T>`
is a recommended type alias for the type `MailboxProcessor<T>`", which
adds nothing to the example.
