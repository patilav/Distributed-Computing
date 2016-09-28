# Orleans: Cloud Computing for Everyone

## Summary

## Analysis

### Introduction

Orleans is a simultaneously a programming model and a runtime designed for writing scalable, elastic, fault-tolerant applications; this framework is targeted at a cloud environment. The main consideration when designing Orleans was the ease-of-use of the model by the programmer. The authors wanted to make it possible for non-expert developers to write applications that could take advantage of the resourses in the cloud, while abstracting the problems of scalability, partial failure, and consistency from the developer. These problems are handled by the runtime.

At the base level, Orleans relies on Actors, which they call "Grains." Grains adhere to a single address space, single threaded model. There can be multiple instances of a grain (called "activiations"), but the single-threaded, single-address-space model allows these instances to be spread across multiple nodes.

Grains communicate through RPC-style calls. These calls return promises, which can either be applied to other computations or waited on explicitly. The paper states that a grain handles these asynchronous requests one-at-a-time; presumably the runtime queues requests made to grains.

On top of grains, Orleans has a notion of a "transaction." A transaction is a request to the application, and each transaction involves a known set of grains. The activations of the grains that will handle the request are isolated from the activations of grains handling different requests. If any of the activations fail, Orleans will handle cleanup and re-execution of the request.

## Limitations and Extensions

## Editorial
