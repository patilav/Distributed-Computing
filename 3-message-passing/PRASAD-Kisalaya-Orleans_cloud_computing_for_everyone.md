The paper introduces the Orleans cloud computing framework, which is a software framework for _"building reliable, scalable and elastic distributed systems."_

Orleans constrains the programming model for the developers to a path of best practices.The framework places a focus on using simple concurrency models using actors which communicate through asynchronous messages and promises. Actors are called grains and they run in isolation from each other. Orleans takes over the part of persisting, migrating replicating and reconcilation of grain states along with a lightweight transaction model. This provides for automatic error handling and failure recovery.  Declarative mechanism is encouraged where applicable, so that Orleans runtime can optimize for performance.

#### GRAINS
Orleans provides grains, which are aynchronous, distributed and isolated actors. These grains can be replicated and persisted to disk by the runtime. Orleans provides a reconciliation mechanism through which the actors can merge their states and provide a consistent system view to other actors.
A system can run many grains at the same time. But they don't share memory, and can be thought of as a single threaded isolated process. They handle one request at a time. This avoids synchronisation and multithreading models which are complicated. 

Grains can be fine or coarse to balance between concurrency and efficiency of state management. A fine grain will be, for example a grain returning user information. This is logically independent and a lot of grains should be able to work on it in parallel. An example of coarse grain will be search index, with a complex data structure making it more efficient to be processed be smaller number of grains working together. Orleans has no recommendation for granularity and is pretty flexible in its support for different permutations.

#### Activations
Activations are to grains what objects are to classes. Orleans can handle the creation of grains and can scale up or down the number of activations for a grain according to the requirements. Each activation is independent of other activations and cannot share memory. The activations need to reconcile the changes to a grain's persistent state.

#### Persistance and Reconciliation
Grains in Orleans have a persistant state stored somewhere. A state change in one activation of a grain is available to subsequent activations of the grain by default. If a grain modifies it's persistant state, Orleans runtime updates persistant storage with the updates at end of request. Since many activations of a grain can be active at the same time, Orleans provides a mechanism to reconcile the conflicting changes in a multi-master branch and merge model. 

#### Consistency and Failure Handling
Orleans has a simple consistency model based on lightweight transactions. A request might involve more than one grains, and they are all processed as one transaction, ensuring atomicity. Activation handling one request is seperated from other activations. The data is persisted to disk and is available to other activations after the transaction is completed. Transactions can automatically rolled back and tried again in case of failure, leaving developers free from handling most failures due to the distributed nature of the system.

#### Automated Scalability
The three common techniques for scalability are asynchrony, partition and replication. 
The grains in Orleans are asynchronous in nature by default. The activations of a grain can be replicated by the runtime to handle the load. But it's also a part of application semantics to design the system using the mechanisms provided by Orleans to ensure the scalability.

## Programming Model

#### Promises
Orleans grains are asynchronous in nature and use Promises to communicate. Promises are implemented as .NET types. Orleans promises can be treated as Futures, and can be blocking until it is resolved. Multiple promises can be joined together using Join method.

#### Grain Execution Model
All the code execution happens in a discrete unit of work called turns. A turn is atomic and is executed sequentially by the activations. This makes execution inside a activation effectively single-threaded. Actual thread-to-activation mapping is handled by Orleans runtime using a scheduler which maps activations to a thread pool. Due to a single threaded nature, we don't need locks or other synchronizing mechanisms which come with their own set of issues like race conditions. This leads to easier development cycle.

There can still be some nature of indeterminacy introduced from the unpredictable order of resolution of promises. Although this never leads to race conditions, this cause some problems with the state of activation being different at different times.

Orleans by default makes sure that an activation handles one request completely before taking on another. A activation is complete when all the promises associated with have been resolved or all associated delegates have completed, and can then handle next request.

#### Error handling

Promises propogate errors and hence it should be easy to handle failures in cases where promises fail to resolve. This is a simplified error handling mechanism for distributed systems.

#### Grain Interefaces and References

Standard .NET interfaces are used to define an interface to a grain. A grain must inherit from IGrain marker interface and should return Promises from the methods and setters. They should take in another Grain interface of serializable types as arguments.

A grain reference is a proxy object to a grain. It is the only way to access some grain, and are first class values that can be passed as arguments or stored as part of persistant state. They are somewhat like promises in a sense that they can be in three states: unresolved, resolved or error. The requests on a unresolved grain is queued up and executed in order when it gets resolved.

#### Creating and Using Grains

Orleans creates a static factory and a proxy class for all the grains. Factory can be used to keep track of grains and proxy class is used to convert method calls to messages.

## Orleans Runtime

Orleans is a framework for .NET runtime and supports any of the .NET languages and can run anywhere from desktop to cloud platorms.

With Orleans, developer need to only figure out the persistant state, and Orleans takes care of persisting it to disk.

#### Persistance

Each grain has some parts of it's state which should be persistant. These are marked using .NET annotations. Orleans runtime, when it activates a grain, loads the persistant properties and uses it to initialize the transient state. This is done on a grain's _ACTIVATE_ method. When the request ends, grain's _DEACTIVATE_ method is called, and the state is written to persistant storage. 

#### Replication

Each grain acts as effectivly, a single threaded process and hence limits scalability. Orleans tackles this by grain replication. It creates multiple activations of the grain to adapt to the load. A new activation can either branch off from some existing activation or from the persistant storage. The number of activations can also be scaled down to reclaim resources if needed. These new activations go on the same server initially, but if that is busy, it is then replicated on other servers. The location of activations is kept track of using a directory based on distributed one-hop hash table with caching mechanism.


#### Isolation

All activations run independent of each other and do not share memory even if they are on the same system. They only communicate using asynchronous message passing and using grain references. .NET memory safety feature guarentees isolation.

#### Resource Management

Orleans manages the computational resources it runs on, scaling up or down the resource allocation depending on needs. It uses a simple load balancing and shedding policy. This simple greedy strategy works in most of scenarios, but Orleans is pretty extensible when it comes to implementing other policies.

#### State reconciliation

Even though there are multiple activations of a grain, there can only be one persistant state and it needs to be resolved. It can simply be a last-writer mechanism which works okay in a few scenarios. Orleans also provides reconcilable data structures like records, lists dictonaries that track and recocile the conflicts automatically. Orleans is extensible here as well, and lets developers write their own mechanisms.

#### Transactions

Orleans provides atomic consistent isolated and durable transactions.  Transactions helps Orleans achive three goals:
- Isolating the operations
- Ensure consistent application state
- Reduce the need for error handling explicitly

Transactions work by controlling flow of operations through the activations. A transaction is created at the arrival of request and covers all the activations which are needed to process the request unless otherwise specified. It is completed when the request finishes execution and Commited when the state is persisted to disk. Orleans supports both optimistic and pessimistic transactions and it depends on the needs of the application. In case of failures, a transaction is aborted and re-executed. 

Since an activation joins a transaction and remains attached to it until it finishes, it ensures that only one transaction operates on one copy of grain's state. This ensures consistent view of state inside a transaction.


## Related Work

#### Actors

Orleans adds to the actor model support for replication, transaction and consistency. Actors were already present in languages like Erlang.
The paper compares Orleans actors with Erlang's actors. Orleans is built on top of an inperative language. The communication model in Orleans is based on promises which are asynchronous unlike synchronous RPC in Erlang. 

Erlang also supports transactions and failure replications but is less scalable compared to Orleans. It also requires exlicit error handling. Orleans also has a better recovery mechanism from system failures.

E is one of the languages for oo programming language for distributed computing. It has a concurrency model similar to Orleans but is based off event loops and promises. It also doesn't have runtime support for persistance, transaction and replication.

Thorn is another language which was discussed breifly in the paper. It provides two different communication abstractions : synchronous ROC and erlang-style send and recieve. But it lacks promises and also doesn't come with the runtime mechanisms of replication, migration, persistence etc which are there in Orleans.

#### Transactions

Orleans provides lightweight transactions for isolation and ensuring consistency in the state. It marks a middle ground in strong,full consistency vs weak eventual consistency models. It provides sufficient guarentee for most of the scenarios. Strong consistency comes in at a cost for performance. Weak consistency models have better performance but more complex programming models.

In Orleans, each transaction sees a state consistent with itself. Independent transactions run in isolation and therefore all of them have consistencies. The states are merged back using well defined strategies. It provides branch-and-merge model and conflict resolution strategies can be defined by the application.


#### Distributed Object Models

The paper takes up example of CORBA, Java Beans and Microsoft's COM which are all examples of object-oriented frameworks for building distributed systems. All are based on distributed object, sync. RPC, transparency and transactions. All of the above intend to reach the same goals as Orleans, and are same in some repects. Orleans differs from them in manner of adopting asynchronous APIs as part of its framework. Also, Orleans' activation model for scalability adds more capabilites. And also, Orleans transactions are different from strict ACID semantics offered in these systems.

### Future Work

There's an ongoing research about resource management. All the decisions in Orleans are around activations like when should it be created, reused or discarded. With activations, server resources behind it also need to be managed, like requesting new server instances. 

Another research area is on extending Orleans to run on clients as well, making it a ubiquitous model across clients and server. Client applications have some special issues like intermittent connectivity, partially trusted systems, migrating between clients and servers.



## Analysis of the Paper

This paper does a fairly good job on introducing the Orleans framework and how it works. It also presents what is good about it pretty well, but it doesn't present any limitations, or rather hides it very well. I could see that the branch-and-merge model typically causes some problems, like it does in GIT. 

Orleans does a solid job of providing actors and messages in a language that doesn't natively support actors or message passing. It focuses on providing a runtime which frees developers from thinking about load-balancing, actor-placement and scalability. Erlang provides most of these basic blocks but higher level abstractions are generally missing. Akka is an alternative for JVM which intends provide the same abstractions as Orleans. Akka provides the same high level abstractions for most parts, but the load-balancing and actor-placement part is left to users more in Akka.

