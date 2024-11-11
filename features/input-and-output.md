# Pentagram ideas / Features / Input and output

## Async I/O

Pentagram unifies all file, network, pipe, etc. I/O into a single async I/O API: the stream. You request bytes, you get bytes. You send bytes, they get written. There is no overhead of multiple copying of the data or special memory regions just for I/O. While one task is reading or writing, another task can start running in its place.

### Read operations

- You can read a whole file or whole stream to its end
- You can read up to a delimiter
- You can read up to a timeout
- You can read up to a maximum length

If the expected size of the data requested isn't known beforehand, Pentagram will dynamically allocate just the exact size needed.

### Write operations

- You can append to a file or stream
- You can replace part or all of a file

## I/O priorities and queueing

Not all I/O is equal. Sometimes you have more important things to read or write, and no device has unlimited I/O bandwidth. For these cases, you can configure priority classes for different I/O operations, and max parallelism and queue depths for those classes.

## Why no memory-mapped file I/O?

At a fundamental level, OS-provided memory-mapped file solutions are incompatible with Pentagram's cooperative multithreading and there is no workaround. If the OS is handling a hard page fault, all of the Pentagram tasks for that thread will be blocked, not just the task that triggered the fault. It would be possible for Pentagram to implement custom asynchronous page faulting logic, but it would not be possible for Pentagram to transparently implement the same page eviction logic that makes memory-mapped file I/O attractive in the first place.

From a performance perspective, Pentagram's general async I/O should be sufficient because it manages its own queues to prevent I/O oversaturation (e.g. "write storming"), it can read blocks smaller than OS memory pages, and it does not require MMU/TLB management.

For programming a cache that can grow and still avoid swapping to disk see [Bounded cache](bounded-cache.md).

## Analysis layer

Any I/O without a timeout error condition is an availability security vulnerability. A condition that makes I/O take longer than usual could cause resource locking or above-limit latency.

To help developers with this, Pentagram will provide an analysis layer that will flag any I/O operations that don't specify timeouts and that aren't covered by higher-level latency-check timeouts.
