# Notes on "dataflow" chapters
list of chapters for reading:  
1.11 Dataflow (1pg)  
2.2 The single-assignment store (7pgs)  
4.93-4.95 Dataflow variables as communication channels + ... (5.5pgs)  

Book: Concepts, Techniques, and Models of Computer Programming, By Peter Van Roy and Seif Haridi.
Date: 2016-09-22  
By: Jingjing Ren  

## Summary
The book introduces dataflow concurrency and using dataflow variables to achieve this in the declarative model.

## Details
The book discusses the need to use waiting for bind to achieve concurrency work, aka, `dataflow concurrency`. Then it introduces `declarative model` is , where the `declarative variable` stays bound throughout computation and is indistinguishable from its value. The `partial value` is a data structure that may contain unbound variables, allowing creating a variable and binding it done separately. When `declarative variables` that cause the program to wait until they are bound are called `dataflow variables`. The nature of it can serve as a communication channels between threads, where threads can be seen as objects.  

Dataflow execution is useful because:  
- It is a powerful primitive for concurrent programming  
- It removes order dependencies  
- It is a powerful primitive for distributed programming  
- It makes it possible to do declarative calculation with partial information  
- It allows the declarative model to support logic programming

### Comparison between other implemntation of dataflow concurrency
Dataflow variables are one of the techniques to implement dataflow execution, other alternatives include `future` and `I-structure`. `Future` were first introduced in Multilisp, it works in the way that it returns a read-only variable or placeholder of a expression E and initiates evaluation immediately; when E is needed, it waits for the value to be available. An `I-structure` is an array of single-assignment variables where individual elements can be accessed before all the elements are computed. When the value is needed, the computation blocks until available. Both of `future` and `I-structure` can only be bound once by the computation that calculates it. The "dataflow variable" however can be bound more than once as long as the bindings are consistent with each other.
