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

### Stack

If an object has a fixed size and can't only be stored in a register (e.g. its lifecycle extends to a parent method call), it may be stored in the stack. The stack grows with each method call and shrinks with each method return.

### Heap

On the other hand, if an object has a dynamic size (e.g. an array that resizes) or it's too big to go in the stack or its lifecycle doesn't align with method calls, it must be stored in the heap. Dedicated memory will be allocated for the object for its entire lifecycle.

Every object that uses heap allocation needs a pointer to that heap location (8 bytes). If the object has a dynamic size, it also needs a size field and capacity field (stored together in another 8 bytes).

### Static

Some objects are created and initialized at compile time -- they'll have a static, read-only location just like program code.

## Stack growth

All execution Tacit code happens via coroutines, and each coroutine has its own stack. (When a coroutine needs to do I/O, it yields to another coroutine to use the CPU.)

The maximum stack size for a Tacit coroutine is 8 MiB. When a coroutine starts, a contiguous block of virtual memory for its stack is allocated, but only the first 64 KiB is allocated using readable and writable memory. The rest of the block is marked inaccessible, and any access will trigger a segmentation fault. When that happens, the coroutine will automatically double the size of its stack by allocating more readable and writable memory.

## Heap allocation

Because the heap can be used for objects of any size, it supports different allocation scheme.

### Tiny allocation scheme

If the object size is small enough to fit in the heap pointer (8 bytes), that's where it'll be stored instead of allocating heap memory. This case covers zero-size objects.

### Small allocation scheme

#### Cache lines

Every small heap allocation uses at least one cache line (64 bytes), even if the object size is less than that. The reason is that multiple CPU threads writing to the same cache line causes an inefficient "false sharing" conflict. Even though memory allocated for different threads will go into their own pages, that memory ownership can still be transferred to another thread.

#### Current page

Every small allocation size must be a power of two, and each size gets a separate 64 KiB page where new allocations are taken from. When one slot gets allocated, the slot after will be up next, until the page is full and a new page gets allocated.

To avoid thread coordination, each thread has its own current page for each small allocation size.

#### Free stack

If memory is deallocated (when a small object is deleted), that object's slot will get pushed to a "free stack" for that object size. Then the next time an allocation happens, if the free stack has an item, that item will get popped and used.

In this manner, an application with a stable behaviour will reach a generally stable state where the current page is no longer used and allocations only happen from the free stack. Full pages are never returned to the operating system, because it's to expensive to find them, too unlikely they'll appear, and too likely they'll be needed again by a stable program. (For deallocated memory that gets returned to the operating system, ensure object's size or capacity triggers medium or large allocation.)

Again, to avoid thread coordination, each thread has its own free stack for each small allocation size.

#### Creating initial current pages and free stacks

The first current page and initial free stack for each small allocation size are allocated for the main thread at startup together with code and static data, and are allocated for new threads on thread creation.

Each free stack has a minimum capacity of 64 KiB and a maximum capacity of 4 MiB, which is able to support up to 524,288 deallocations not matched by allocations. Any deallocations beyond that are lost and not recoverable (a warning will be generated). Free stacks use only the medium allocation type.

For 16 allocation sizes (i.e. 16 current pages and 16 free stacks), the memory usage per thread at minimum capacity is 2 MiB.

### Medium allocation scheme

One an object's size reaches 64 KiB, it is allocated directly from the operating system as a contiguous region of virtual memory. When the object is deleted, that memory is returned to the operating system.

### Large allocation scheme

For large objects over 4 MiB, the object's memory is split into 4 MiB pages, and an intermediate "index" page is also introduced.

There are two reasons for this: first is to prevent copying the whole object when its size grows, and second is to avoid fragmenting virtual memory with very large allocations. (A 48-bit address space supports allocating 67 million 4 MiB pages.)

The index page starts at 64 KiB, which supports object growth without any data copied up to objects of size 64 GiB. Start at that size, object growth needs copying and doubling the size of the index page.

### Capacity and size comparisons

For a 48-bit address space and object capacities that are powers of two, there are only 48 object capacities, which can be encoded using 7 bits. The object size needs to be exact, and can use 47 bits. Both size and capacity can be encoded in the same 64-bits of memory.

When running as an x64 program, the capacity required for an object size can be computed using the BSR (bit scan reverse) instruction. When running as a WebAssembly program, [`iclz`](https://webassembly.github.io/spec/core/exec/numerics.html#op-iclz) (count leading zeroes) will be used instead.

Based on the capacity, the current heap allocation scheme can be looked up (tiny, small, medium, or large) together with the exact parameters for the scheme at that capacity.
