# Pentagram ideas / Features / Bounded cache

A "bounded cache" is an in-memory object that a Pentagram program uses to speed up its execution. The larger the cache, the potential greater the speedup, but the cache must fit in physical memory together with the memory used by other objects and processes. If physical memory is exceeded and parts of the cache get swapped out to disk, the performance will degrade: it would be best to shrink the cache in this scenario.

In Pentagram, it won't be possible to use memory-mapped file I/O for building bounded caches (see [Input and output](input-and-output.md)). Also, memory-mapped file I/O is not suitable for caches where disk representation does not match memory representation, for example compressed disk storage.

One recommended approach for Pentagram programs is to allow the cache to grow, but also subscribe to a memory pressure event and manually shrink the cache until memory pressure is relieved.

An alternate recommended approach is to augment that strategy with a configuration setting, to allow people to change the maximum ideal cache size of the program. The default setting can be based on total physical memory. (This is the approach picked for the Pentagram compiler.)
