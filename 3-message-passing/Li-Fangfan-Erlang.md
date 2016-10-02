#Erlang

##Summary

This paper introduces *Erlang*, a concurrent programming language designed for programming fault-tolerant distributed systems. *Erlang* was developed back in the 80s, but we have seen renewed interest in Erlang, as its way of programming maps naturally to multicore computers.

##Main Contributions

*Erlang* distigushed itself from most other languages of being a concurrent programming language where concurrency belongs to the language instead of the operating system. There are few major points that differentiate *Erlang* from others.

First, *Erlang* programs are collections of parallel processes cooperating that can be created quickly and have only limited memory overhead.

Second, fault-tolerance in *Erlang* is guaranteed by redundancy. More specifically, there is nothing being shared by all processes in the program, and when one process crashes, another process can detect the crash or take over the jobs that the died process was doing. Thus, the processes in the programs are naturally divided into worker processes and supervisor processes.

Third, since processes share nothing between each other and they can be distributed on different cores/machines, the only way to communicate with each other is through message.

##Limitations
The first limitation might be the the potential waste of resources. In order to achieve better fault-tolerance, *Erlang* programs need to add more redundancy. 

Second, the pure message passing between processes might add latency and is highly dependend on the network condition.

##My opinions
I like the idea of 'let the process crash and focus on restoring instead of avoiding failure', because in the world of distributed system, nothing is reliable, it is good that the program is already prepared for failure.

I think this is an example that an idea needs to find its best suited application. *Erlang* was developed for telecom communication in the 80s, spreaded slowly for several year after open-sourced, and only experienced the dramatic upturn very recently. 

The author also provided numerous real world applications that are using *Erlang* right now, which are used as evaluation for this concept, personally I think this is a very strong proof.