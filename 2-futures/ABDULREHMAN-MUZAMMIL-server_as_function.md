This paper discusses Twitter’s large-scale, high-level, runtime library, called Finagle. 
Finagle uses asynchronous operations, services and high level filter to service Twitter’s entire service stack-both front and backend server. 
It primarily focuses on the design of Finagle, its components, and the practical limitations of the system.

The paper discuss Scala examples of implementation of three main components of Finagle: Futures, Services and Filters. 
Futures are containers of asynchronous operation results. 
They form dependencies between multiple operations as well as provide an option to handle failures of the system.
Composing multiple dependencies also allows a system to easily perform “fan-out” operations like MapReduce. 
Services are remote endpoint functions that accept futures as the return value.
Filters, on the other hand,  implement application independent functions, like timeouts, sanitizing inputs, logging information, parsing, health checks, etc. 
It also talks about composite filters in a declarative manner that allow users to easily add functionality without concerning themselves with the low-level code. 
It also increases the modularity of the system.

The authors describe the semantics of the Finagle in Scala programming language, instead of a pseudo code or a widely used language. 
One has to first understand the language to fully grasp the contents of the paper. 
The authors vaguely talk about the performance issues of Finagle, however, they don’t share any details or experimental results regarding performance degradation.  
The authors only talk about design and not the cost of composition of filters and services in Finagle.

Overall, this is a well written paper to present a design of a large-scale library for production, without disclosing any specific details about the company itself.
Altough, their approach of using asynchronous operations or Futures is not novel, however, they do present the application of the system in a large-scale distributed environment.
This data-flow approach, combined with interrupts, allows users to not only hide the underlying abstraction but will also allow them to cancel any request.
This high-level declarative language will allow twitter developers with the modularity and flexibility to combine different operations to perform a complex task and handling  large volumes of data without a large overhead.
