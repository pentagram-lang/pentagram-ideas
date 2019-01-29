# Tacit ideas / Plans / Memory management

Everything in Tacit is an object. Objects can contain other objects. Every object has a place where it's stored.

## Object lifecycle

### Creation

When an object is first created, it needs a location. That location is allocated from one or more of the locations below.

### Initialization

Before an object can be used, all of its fields need to be initialized. For large objects, this process can be optimized if all of the object's properties (and contained objects' properties) are initialized to zero.

### Write ownership

Under the hood, only one variable referring to an object can have write access to an object at a time. This ownership is transferred automatically through variable assignment and method calls.

When a variable has write ownership, property writes go directly to the object's location. Without write ownership, property writes happen to a copy of the object. Property reads always go directly to the object's location.

### Deletion

When an object has no more variables referring to it, it will be deleted and its location deallocated.

## Object locations

### Register

If an object is being actively used, it'll be stored in a CPU register. Also, if the object is small enough (less than or equal to 32 bytes) and isn't needed elsewhere, it may _only_ be located in a register.

### Coroutine stack

All execution Tacit code happens via coroutines, and each coroutine has its own stack. (When a coroutine needs to do I/O, it yields to another coroutine to use the CPU.)

If an object has a fixed size and can't only be stored in a register (e.g. its lifecycle extends to a parent method call), it may be stored in the coroutine's stack. The stack grows with each method call and shrinks with each method return.

### Heap

On the other hand, if an object has a dynamic size (e.g. an array that resizes) or it's too big to go in the stack or its lifecycle doesn't align with method calls, it must be stored in the heap. Dedicated memory will be allocated for the object for its entire lifecycle.

Every object that uses heap allocation needs a pointer to that heap location (8 bytes). If the object has a dynamic size, it also needs a size field and capacity field (stored together in another 8 bytes).

Within the heap, there are 4 potential allocation schemes: tiny, small, medium, and large. The scheme depends on the size of the object to allocate.

### Static

Some objects are created and initialized at compile time -- they'll have a static, read-only location just like program code.

## Heap size ranges

### Tiny heap

If the object size is small enough to fit in the heap pointer (8 bytes), that's where it'll be stored instead of allocating heap memory. This case includes zero-size objects.

### Small heap

Every small heap allocation uses at least one cache line (64 bytes), even if the object size is less than that. The reason is that multiple CPU threads writing to the same cache line causes an inefficient "false sharing" conflict. Even though memory allocated by different threads will go into their own blocks, that memory ownership can still be transferred to another thread.

### Medium heap

One an object's size reaches 64 KiB, it gets allocated directly from the medium heap.

### Large heap

For large objects starting at 2 MiB, the object's memory is split into 2 MiB blocks, and an intermediate "index" block is also introduced.

There are three reasons for this: 

1. To prevent copying the whole object when its size grows

2. To avoid fragmenting virtual memory with very large allocations (an unfragmented 48-bit address space supports allocating 134 million 2 MiB blocks)

3. To enable support for transparent huge pages

The index block is fixed at 64 KiB, which supports object growth without any data copied for up to objects of size 16 GiB.

## Thread initialization

Every thread manages its own allocation independently from other threads (to avoid synchronization), so when a thread starts it needs to prepare for allocation.

### Regions

1. Tacit code runs using coroutines, so a 1 GiB region of virtual stack space is reserved, aligned at 8 MiB (using 16 MiB of extra padding)

2. Large heap allocation which always uses 2 MiB blocks gets its own 1 GiB region reserved, with transparent huge pages enabled for the whole region, aligned at 2 MiB (using 4 MiB of extra padding)

3. Medium heap allocation gets a third 1 GiB reserved region, also aligned at 2 MiB (using 4 MiB of extra padding)

### State

1. Coroutine stacks are managed using one region address, one next address (set to the start of the stack region) plus one dynamic-sized free stack plus a blocks-returned counter

2. Large heap allocation is managed using one region address, one next address (set to the start of the large heap region) plus one free stack plus a return counter

3. Medium heap allocation gets a region address and a single next address (set to the start of the medium heap region), and every block size from 64 KiB to 1 MiB (5 sizes) gets its own free stack and return counter

4. Every small heap allocation block size from 64 bytes to 32 KiB (10 sizes) gets its own next address (set to the start of the medium heap region), free stack, and return counter

In total, roughly a quarter of a KiB.

## Allocation

### Coroutine stack

#### Coroutine start

1. Mark the first 64 KiB of the next address as readable and writable

2. Increment the next address by 8 MiB

3. Check the coroutine next address, if it's at the end of the region, reserve a new one

#### Coroutine execute

1. Catch a segmentation fault

2. Compute the stack base by rounding the stack pointer up to the nearest 8 MiB

3. Compare the address that caused the fault to the stack base

4. If the address is within the stack, extend the stack size (by making memory readable and writable) to the power-of-two that will cover the address

5. If this extends the stack to 2 MiB, turn on transparent huge pages for the first 6 MiB of the stack (before doing the stack extension)

6. If this is the final stack extension, instead of extending up to exactly 8 MiB, leave 64 KiB inaccessible to protect against stack overflow

### Small heap

1. If the next address is an even multiple of 64 KiB, allocate a new 64 KiB block via medium heap allocation

2. Return the next address

3. Increment the next address by the object size

### Medium heap

1. If there isn't room between the next address and the following/current 2 MiB boundary, mark the next 2 MiB as readable

2. If that exhausts the current region, reserve a new region (ignoring any leftover unallocated memory from the current region)

3. Return the next address

4. Increment the next address by the object size

### Large heap

1. Allocate a 64 KiB index block from the medium heap

2. Mark the next addresses up to the object size as readable and writeable

3. Put those addresses into the index block

4. Return the index block

5. Reserve a new region if needed

## Deallocation

### Free stack

When memory is deallocated, it goes to a free stack first before getting returned to the operating system. Then, instead of allocating memory via a next address, the free stack can be used instead (first in, first out).

In this manner, an application with a stable behaviour will reach a generally stable state where the next addresses are no longer used and allocations only happen from the free stack.

For some free stacks, returning memory to the operating system is helpful when there's a major reduction in the program's resource usage. For this case a return count is used. If the return count gets too high, unreturned items at the bottom of the free stack are _lazily_ returned to the operating system, and the return count is incremented for each item. If a previously-returned item from the free stack gets popped, decrement the return count.

Each free stack is a dynamically-sized array with a maximum size of 1 MiB. When it doubles in size, it gets copied to a new location and the old address gets deallocated (gets put into a different free stack). This can trigger a recursive, but limited chain reaction.

### Coroutine stack

1. Turn off transparent huge pages for the coroutine's stack

2. Mark the entire stack as not readable and not writable

3. Push the address onto the free stack

4. Return the next 32 items in the free stack if it contains 64 unreturned items (up to 256 MiB returned depending on actual stack usage)

### Small heap

1. Push the address onto the free stack

2. Don't return anything from the free stack

 Full 64 KiB blocks used by the small heap are never returned to the operating system, because it's to expensive to find them, too unlikely they'll appear, and too likely they'll be needed again by a stable program. (For programs that want to ensure that memory gets returned to the operating system, object size or capacity must trigger medium or large allocation.)

### Medium heap

1. Push the address onto the free stack

2. Return the next 32 MiB of items in the free stack if it contains 64 MiB unreturned items

### Large heap

1. Push the address onto the free stack

2. Return the next 32 MiB of items in the free stack if it contains 64 MiB unreturned items

## Capacity and size comparisons

For object that are powers of two and a maximum size of 16 GiB, there are only 36 object capacities, which can be encoded using 7 bits. The object size needs to be exact, and can use 35 bits. Both size and capacity can be encoded in the same 64-bits of memory.

When running as an x64 program, the capacity required for an object size can be computed using the BSR (bit scan reverse) instruction. When running as a WebAssembly program, iclz (count leading zeroes) will be used instead.

Based on the capacity, the current heap allocation scheme can be looked up (tiny, small, medium, or large) together with the exact parameters for the scheme at that capacity.

## Analysis layer

Any single object that's dynamic in size with no upper limit is an availability security vulnerability. Some condition could cause extreme page swapping or out-of-memory, making the program effectively unavailable.

To help developers with this, Tacit will provide an analysis layer that will flag any unbounded, dynamic size objects in the program. This analysis layer may also be able to calculate the total theoretical maximum memory usage of the whole program.

## Future benchmarking/extensions

- Populating memory when marking it readable and writable, to avoid later soft page faults
- Shifting memory management system calls to a background thread
- Disabling transparent huge pages
- Searching for returnable small heap blocks
- Making free stack returns time-based
- Use a lower minimum coroutine stack size for Linux
- Convert some small heap sizes to medium for Linux
- Use a dynamic index block size for large objects
- Use finer-grained capacity for large objects
