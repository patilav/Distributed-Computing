# Review on Implementing Remote Procedure Calls
Date : Sept. 15th, 2016  
Created by: Jingjing Ren  
Course: [CS7680 Fall 2016](http://heather.miller.am/teaching/cs7680/)  

## Summary  
The paper presents the implementation of remote procedure calls within the Cedar programming environment, with a focus on binding mechanics and transport level communication protocol. 



## Outline  

**Aims and problems**  
The idea of RPC has been around for years and yet there are rarer implementations than designs. The paper focuses on their choices made to achieve these goals:   
	1. easy semantics  
	2. efficiency   
	3. security(less details)  

**Assumptions**  
The work is specific to the Cedar programming system, where they have control over:  
	- network topology  
	- network load and capacity - not overloaded and reasonable latency  
	- the architectures are homogeneous   

**Choices they made**  

- Powerful binding mechanics:   
	**Naming** There are two parts of the name of an interface: the *type* and the *instance*. The former defines the abstraction of interface and the latter specify the desired implemntor.   
	**Locating** RPC package maintains two entries:  *individual* and *group* in Grapevine distribute database (reliable). RPCRuntime uses `ExportInterface` to make interface available and `ImportInterface` to bind a internface. 
	
- Packet-level transport protocol  
	The section is to design a transport protocol specific for RPC. For simplicity reason, the goal is to make the semantics of RPC identical to local procedure calls - no timeout for deadlocks or loops. For efficiency, the protocol needs to be not only reliable, but also  light-weighted, so the design does not include connection establishment or tear-down, instead it remains stateless, has a *call packet* and a *result packet* and ways to deal with retransmission and  duplication. The design is specifically for RPC and they have not explored how it performs in the case of bulk data transfer. For optimization reason, a stock of idle processes are maintained in each machine. 


## Limitations or extensions
**Assumption** the work is very specific to Cedar project and the assumption that you have full control of the network or the network topology is almost certain is not true or pratical in real world. 

**The evaluation is minimal.** There are many other questions to answer: e.g. how does it perform in a congested network? As earlier mentioned in the aim to get security, what are the overhead of encryption? The evaluation is done in only two Dorados, how does that scale out?




## My Opinion
**Importance of easy semantics** The goal is to make distributed application fit into existing general programming language, but does the simplicity worth sacraficing performance or even correctness of the program. 

**Unansered questions**  
- How does this scale in a world with lots of middleboxes, like caching, filtering, optimizating and logging?   
- Now TCP/IP dominates Internet, it's important to understand whether it is possible to use the same idea.    
- Internet topology changes a lot, maybe this is better for Data center where the administrator has full control. 