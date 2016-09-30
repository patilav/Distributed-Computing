Orleans describes a software framework for developing large-scale, reliable cloud applications. 
It’s provides a mostly-declarative interface to developer, allowing for elasticity and scalability of the application and is developed using Microsoft .NET programming language. 
Orleans is built using grains, an isolated, single-threaded process programming unit.
  
These grains are programming abstractions and only activate during an application runtime, allowing the programmer to deal with the data-flow instead of control flow of the program. 
Grains uphold the ACID(atomicity, consistency, Isolation, durability) properties, allowing for error handling and persistent state. 
This makes them ideal for scalable transaction systems(like banks and stock exchange). 
They implement a futures and promises paradigm similar to that of Finagle(unresolved, fulfilled and broken). 
Grains use futures and promises to communicate with each other, and unlike RPCs, grains they can perform both synchronous (explicit wait for futures to return) and asynchronous returns depending on the nature of the required program.  

The runtime scalability of the grains, allow for elasticity in terms of dealing with variable application loads. 
The flexible nature of grains allows Orleans to be suitable for a large variety of applications. 
This has applications in stateful Services Oriented systems. It also makes them ideal for serving HTTP requests, as well as RESTful services. 
Leaving the granularity control to the programmer, allows the developer more control over the size of grain and hence the parallelism that might be needed for the application. 

Orleans’s linearly scalable throughput for a simple messaging application shows promise of a speedup in the system with scalability, however, this result cannot guarantee a similar speedup for all applications. 
Moreover, the single threaded nature for will force the developer to go for a modular design, but not all applications might agree with this design. 
They’ll have to be reprogrammed for this type of system. 
The single-threaded nature with no shared state limits the applications of this system for use in large-scale distributed shared computations.

