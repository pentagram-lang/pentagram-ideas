# Tacit ideas / Plans / Optimizations

## Instructions (CPU/GPU)

* Function calls as jumps without stack manipulation
* Function calls inlined
* Register usage
  * Reduce to avoid call overhead
    * Including not loading from stack to register for input operands
  * Increase to enable instruction parallelism
* Fixed point
* SIMD
* Loop unrolling
* Antibranching
* Memory lookaheads
* Branch targets calculated in registers

## Memory

* Contiguous memory
* Column-orieinted memory
* Shared symbol references
* Decompress into symbol-referenced binary
* Parse into symbol-referenced structures
* Compile-time allocation
* 32 and 16 bit pointers (offsets?)
* No GC overhead

## File I/O

* Memory mapped files (how to handle errors and network files?)
* Async coroutines

## Network I/O

* Event based
* Async coroutines

## CPU parallelism

* Map-reduce
* Microservices
* Channels

## GPU parallelism

* Map-reduce
* Screen buffer output

## Social/ecosystem
* After compile, show bytes per package included
* During CPU profiling, show percent time spent in each package
