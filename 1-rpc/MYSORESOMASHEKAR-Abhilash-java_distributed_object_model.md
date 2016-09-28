<b>Distributed Object model for Java system</b>

This paper provides a profound insight into the Distributed Object model for the Java system which features remote method invocation (RMI). It explains the differences between the local object model and remote object model, the semantics behind their implementation. Further, the paper describes the abstraction and seamless integration in RMI system achieved using pickling, dynamic loading of classes and efficient garbage collection.

One of the prime focuses of this paper is the RMI system architecture built on the assumptions of a homogenous platform (limited to JVM client and servers) and language dependence. It involves the server first registering/binding its services with the RMI registry which is a map of bind names and the host names on which the remote objects reside. The client then fetches the reference/stub to the remote object by looking up for a bind name in RMI registry. The remote procedure call is made on the fetched remote object reference. This idea of calling a remote procedure just like any local method is perceived as a seamless integration of client and server. 

Both the client and the server have two common layers - 
Remote Reference layer which deals with invocation strategies - unicast/multicast and persisting the remote objects.
Transport layer sets up connections, manages requests and listens to the incoming calls.

On the client end, a special layer called stub interfaces between Remote reference layer and the client. The stub acts as a proxy for the remote implementation of the object and its procedure. On the server end, the skeleton interfaces between the the Remote reference layer and the server. The skeleton dispatches the request to the remote object implementation. Both stub and skeleton are responsible for marshaling and un marshaling the objects exchanged between client and server.

The paper proposes the following arguments which play a major role in achieving the goal :
Dynamic Stub loading: RMI heavily relies on dynamic stubs than static ones. The missing client stub is downloaded from the server when required.
Garbage collection: The remote objects having no reference on the client end are collected with the help of reference-counting mechanism. Also, it is ensured that no objects are pre maturely collected.
Pickling: Java based mechanism to transmit the objects between client and the server address spaces.

We can conclude that the paper discusses about a Java based RPC implementation leveraging the functionalities provided by JVM and the language itself. The language dependency is a significant advantage which makes it simple, highly seamless and adaptable for distributed programming.

<i>Limitations</i>:
The paper doesn’t explain how RMI registry could be configured in a distributed system which makes us concerned about the availability of RMI registry. RMI registry could be a single point of failure in the distributed system.
The paper assumes that the network is always reliable. In an unreliable network with very high latency there could be chances of premature garbage collection leading to inconsistency.[2] Encryption/Authorization/Authentication is not built into the protocol.[5]

<i>Extensions</i>:
In high volume distributed RMI applications, the pickled data could be compressed before being sent across the network. This mechanism involves creating a new socket type which can compress the data.[3] Many a round trips to the server could be reduced by employing caching.
For example, Object caching can be implemented using smart proxy mechanism. This mechanism caches the desired object state at the client end to avoid the network overhead on every method call.[1] Both RMI registry and server availability can be improved by employing replication. To ensure the availability of RMI registry, the system could include a fallback mirror of the main RMI registry. Server availability could be ensured by placing multiple instances for a single bind name in the RMI registry and returning the one which is available. Load balancing mechanisms could be extended to the RMI registry.

The paper explains its goal and ideas crystal clear. It also has given good amount of implementation details. The impact of this paper was only temporary and limited to the Java world for the obvious reasons. It certainly laid foundations for the Java object oriented distributed model. Many systems like EJBs, Hadoop IPC, JMX monitoring were built over Java RMI system.

<b>References :<b>

[1] Wilson, By M. Jeff. "Get Smart with Proxies and RMI." JavaWorld. Accessed September 14, 2016. http://www.javaworld.com/article/2076234/soa/get-smart-with-proxies-and-rmi.html.

[2] "Java Remote Method Invocation: 3 - RMI System Overview." Java Remote Method Invocation: 3 - RMI System Overview. Accessed September 15, 2016. https://docs.oracle.com/javase/8/docs/platform/rmi/spec/rmi-arch4.html.

[3] Sintes, By Tony. "Compress Your Data." JavaWorld. Accessed September 15, 2016. http://www.javaworld.com/article/2077363/soa/compress-your-data.html.

[4] Accessed September 15, 2016. https://www.cs.umn.edu/sites/cs.umn.edu/files/tech_reports/01-036.pdf

[5] Securing server side Java. https://www.sans.org/reading-room/whitepapers/application/securing-server-side-java-844
