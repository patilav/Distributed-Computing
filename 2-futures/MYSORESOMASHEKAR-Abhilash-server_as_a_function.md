                                      <bold> Your server as a function </bold>

This paper describes a model which aims at solving many of the prominent issues in distributed systems - connection pooling, load balancing, efficient throughput, distributed tracing, concurrency etc. However, the notable feature / the main goal is that all of the issue handling is done under the hood without any complex programming implementation. This “proven successful” distributed system is named Finagle and embraces abstraction and modularity through functional programming.

The paper emphasizes on three fundamentals of Finagle which helps it achieve its goals of abstraction -
<b>Future</b?> - Future is an asynchronous operation which can be in three states - empty, success and failure. The succeeded future container has a value of a service computation and a failed one an exception-faulty remote servers/service processing returning an exception.Future enables concurrency of operations and also handles dependency between operations. The flatMap resolves any data dependency/sequential composition between multiple operations, rescue resolves any exception during an operation and collect reduces results of all the requests which were fanned out by the client. Future simplifies the execution of asynchronous operations by forcing programmers to visualize it as a data flow rather than control flow. 

<b>Services</b> - Service is a simple asynchronous function which is symmetric - a client consumes a service while a server publishes one. Service is an object which takes in a request and returns a Future. Service on the client end handles several functionalities like - retries, time outs, load balancing, connection pooling etc. Service on the server end handles metrics and tracing, request timeout, concurrency limit etc.

<b>Filters</b> - Filters wrap over the services to provide a pipeline of transformations to inbound requests and outgoing responses. It decouples generic functionalities like logging, request parsing, gathering metrics of request/response from service and thereby promotes modularity. Services being symmetric, filters also exist on both client and server.

In practice futures are accompanied with issues like timeouts which can result in unnecessary operations on server and usage of resources. Interrupt mechanism in Finagle ensures the operations are ended appropriately at the producer end when connection timeouts happen. 

We can conclude that the model as described in the paper employs a developer friendly approach towards distributed systems. It leverages the best parts of functional programming to produce a highly abstracted, modular and a loosely coupled system. Above all, its a proven system which addresses the major fallacies of distributed system and used by many a industry leaders like Box, Tumblr, Pinterest etc.

<i>Limitations</i>: One of the limitations as mentioned in the paper is the latency caused by garbage collection. When a “fan out” of requests happens over hundreds of systems, the garbage collection happening on even one of the systems can potentially delay the calculation on the client end if its a composition future. The system is confined to only networks with in an organization since the client and server are in a way hard coupled owing to the symmetric nature of Finagle. The abstraction itself can be costly since it doesn’t let users to decide on a custom threading or scheduling mechanisms.

<i>Extensions</i>: The load balancers can be improved to track the systems undergoing garbage collection and exclude them from the set of servers to which fan out requests happen. The abstraction can be extended to include the custom logic of threading and task scheduling.

The paper has done a good job in explaining the abstraction provided by Finagle and its ease of use. It mentions the distributed system issues it tries to address, however, the paper hasn’t done justice to the reasoning behind it. The paper overtly stresses on the abstraction/semantics of the API and completely disregards very important features of Finagle - load balancing, connection pooling, service discovery and circuit breaking mechanisms.

