*** Summary ***

This paper presents an efforts towards building an Remote Method Invocation (RMI) system to support distributed objects in Java while retaining most of the Java object’s semantics.


*** Contributions ***

1. Author determines main goals for supporting distributed objects in Java as follows:
* Distributed object model should fit well in the Java object model so that programmers don’t deal with 2 different model when writing distributed programs.
* It should preserve Java runtime safety.
* Allow activation of remote objects to service invocation from client, so that objects can be made active as needed (to avoid taking up valuable system resources, for indefinite periods of time)
* Distributed garbage collection mechanism for active objects


2. The Distributed Object Model system design presented in the paper is based on assumptions of platforms homogeneity and language dependency. Author claims that existing RPC/RMI systems such as CORBA are not designed to operate with any particular language model and thus it was necessary to design a brand new RMI model which can follow Java language semantics and fit naturally into the system.


3. Key features of distributed object model specified in the paper:
* Every distributed object should directly/indirectly implement Remote Interface. RemoteServer class takes care of making distributed objects remotely accessible whereas the RemoteObject class overrides Object class methods to preserve semantics for remote objects.
* Invoking a method call on a remote object is same as a method call on a local object. Reference to remote object can be passed as a parameter, or returned as a result from a method call.
* Parameters to a remote call or result of a remote call are passed by copy.
* Model supports basic Java object semantics such as type checking and type casting.


4. RMI System Architecture:
Author claims that the RMI system architecture is loosely coupled with clear division of responsibilities and protocols which define their boundaries. The 3 layers are-
* Stub/Skeleton layer acts as interface between the application layer and the RMI system. It marshals data/objects (using serialization/deserialization technique called Pickling) to Remote Reference layer and also passes on the method invocation details to the Remote Reference layer.
* Remote Reference layer is responsible for carrying out invocation semantics which currently supports only unicast invocations. It also handles reference semantics for the server such as handling persistent/live references to remote objects which are required for object activation.
* Transport layer is responsible for connection setup and management with remote locations and dispatching to remote objects.


5. Dynamic Stub Loading:
This RMI system solves the problem of code mismatch (prevalent with traditional RPC systems) by generating stub code dynamically and shipping it when needed by the clients. This process employs specialized class loader, security manager (to check arbitrary classes being downloaded) and pickling system for transmission of the code.


6. The RMI model features a distributed garbage collection implemented using remote reference count algorithm. This counting algorithm relies on finalization of remote references for garbage collection. However, finalize method is not guaranteed to execute promptly or may not even execute under certain scenarios which may prevent remote objects from being garbage collected.  Also, the algorithm is not completely robust and fault tolerant which may cause premature garbage collection of active objects.


7. The RMI system relies on JDK1.0 security manager to guarantee safety which does not regulate resource consumption. Thus RMI system is incapable of preventing the loaded stub classes from misusing system resources.


*** Limitations ***
* The design goals presented in the paper looks promising at the first glance, however close observation reveals that the author makes no attempt towards solving some of the primary challenges of distributed computing such as fault tolerance, consistency or availability.
* Lack of distributed synchronization mechanism will lead to inconsistent objects in case of concurrent modification requests by multiple clients.
* Current system does not support replication of distributed objects which makes the system fragile. For example - clients may not be able to access remote objects in case of network partition. Thus the programmer has to write code to handle such scenarios gracefully.
* A reliable distributed system usually has a centralized service like Apache ZooKeeper for maintaining/replicating objects, providing distributed synchronization, replications and other group services. It is hard to believe that RMI runtime provides some or any of these features due to lack of explanation in the paper.
* Finally, the idea of passing around code in the system sounds alarming which may introduce many security threats in the system.


*** Opinion ***
Given that the paper was published in 1996 the notion of distributed objects in Java sounds fascinating. Author clearly shows his intent towards building a distributed model from developers standpoint but the proposal lacks many qualities of a modern distributed system. Moreover, there are subtle differences between the proposed distributed object model and the Java object model. Lack of distributed thread model or non-reliable garbage collector does no good the developers. Wide adoption of data oriented approach to distributed computing, tight integration of the RMI model with one programming language and failure to address basic challenges of distributed computing, this distributed object model has gradually lost popularity over the years.
