# Scala Actors: Unifying thread-based and event-based programming by Philipp Haller and Martin Odersky; COORDINATION '07

## Summary

This report describes the design of a library for Scala providing
event-driven concurrency in the form of actors. Event-driven and
thread-based programming form a classic duality in concurrent
programming; a high-level breakdown is that threads provide a more
intuitive programming model but suffer signifcant performance
overheads while events can scale better but are more difficult to
program with. Actors are a form of event-driven concurrency. An actor
is a concurrent component with a *mailbox* for incoming messages and a
*behavior* which processes messages and in turn sends messages to
other actor's mailboxes.

The library presented in the article is notable in two regards. First,
it lives on top of the JVM, which provides a heavy-weight/OS-backed
threading model for concurrency. Second, that it allows actors to make
blocking calls, which is usually the anithesis of event-driven programming.

The library operates in a fairly standard manner by dispatching
runnable events (actors with a nonempty mailbox) to worker threads.
What is unusual is that actors can block the thread they are running
on. The system responds to blocking by starting a new thread of all of
the current workers are blocked and there are events that are ready to
run.

The capability of handling blocking actors manifests itself in the two
ways the library provides for actors to receive messages. The first,
blocking method is `receive` which takes a message handling function
`f` and runs it on the first applicable message, blocking the actor's
thread until such a message arrives. Control then resumes from after
the `receive` statment. Asynchronous message handling works by
providing the `react` library construct a *continuation* describing
how to handle a message. If no applicable message is available then
instead of blocking the actor and continuation are added to an event
queue. When an appropriate message arrives a worker thread is
dispatched to run the continuation on the message. Crucially, the
*context* of where `react` was used is gone; only the continuation is
run. Several combinators are provided to make non-blocking programming
easier, such as `andThen` for sequential composition.

The performance of the system is evaluated by comparing the
implementation of a simple token-passing system using threads to one
using event-driven actors. The results show that there is some
overhead to actors but not so much as to make them unusable.
Additionally, actors scale significantly better than threads as more
concurrent components are added. Overall the performance is deemed
good enough.

## Analysis

I don't think the report adequately argues the benefit of making
blocking calls from actors; after all, that is the style of
programming we are trying to escape from (and already provided by the
platform). This is important because the ability to make blocking
calls is used several times to distinguish this library from related
work.

Similarly, having to provide an explicit continuation to `react` seems
unfortunate. It is too reminiscent of call-back driven code (aka
javascript hell) and the inversion of control that haunts event-driven
programming. It would be preferable to have a syntax that automated
the CPS transformation, such as the async support in F# (as discussed
last week). I am aware that Scala does not (at least at the time the
library was written) support his kind of extensible syntax. But still.
Macros.

Usually when I hear the words "Scala" and "actor" in the same sentence
they are also accompanied by "Akka". That, however, is about the
extent of my knowledge on the subjects, but now I am curious about
Akka's relationship with the actor library presented here. Judging
from the lack of reference, I am guessing that this library predates
Akka, so I am wondering why Akka superceded it (if it has?).
