# Compiler as Database: A Philosophy

## Why Compilers Collapse

### The Complexity Trap

Every large compiler eventually reaches a point where the accumulated weight of past decisions makes forward progress nearly impossible. You want to add a new optimization pass, but you can't reason about what it will invalidate. You want to make type checking incremental, but the data dependencies are so tangled through pointer chasing and mutable state that you can't prove anything is safe. Adding any feature requires understanding the entire system because everything reaches into everything else's internal data structures.

This isn't a failure of engineering discipline—it's a failure of architecture. Without a coherent model for how compilation works, each new feature becomes a special case. Each special case adds complexity. The complexity accumulates until the codebase has tremendous mass but no structure to support it.

### Performance Decay

Performance problems compound the architectural ones. Cache behavior becomes chaotic because data layout emerged organically rather than being designed. You have tree-shaped ASTs with pointers scattered across the heap, so every traversal is a cache miss lottery. Optimization passes that should be embarrassingly parallel require heroic refactoring because global mutable state is everywhere.

The real cost isn't just that the compiler is slow—it's that you can't predict or reason about _why_ it's slow. You try adding a cache here, an arena allocator there, but without understanding the access patterns, you're just guessing. Eventually every change carries risk: will this make compilation 2% faster or 50% slower? You can't know without trying, so velocity approaches zero.

### Loss of Structure

The fundamental problem is that the codebase has mass but no structure. There are no clear boundaries you can rely on, no invariants that hold across the system. Each module has its own ad-hoc way of representing data, its own conventions for what's mutable and when. The compiler doesn't have an architecture—it has archaeology. Layers upon layers of decisions made for reasons no one remembers, held together by hope and regression tests.

This is why compiler engineering attracts a certain kind of masochist. Not because compilation is inherently complex, but because most compilers have lost the plot. They've become systems where every problem requires understanding everything, where no change is local, where the architecture provides no leverage. You're not building on solid foundations—you're pushing boulders uphill, hoping they don't roll back and crush you.

## Compilers Are Databases

### The Core Reframe

Here's the insight that changes everything: a compiler is a database. Not metaphorically, not as a cute analogy—literally. Your compilation stages are queries and transformations over structured, tabular data. Functions, symbols, expressions, and tests aren't objects in a class hierarchy. They're rows in tables with explicit schemas.

Think about what you actually have: a FunctionTable where each row contains a function's name, type signature, body reference, and source location. A SymbolTable mapping identifiers to their definitions. An ExpressionTable storing the nodes of your intermediate representation. A DependencyGraph recording which functions call which other functions. These are tables. You query them. You transform them. You maintain indexes over them for fast lookups.

Once you see this, you can't unsee it. The entire structure of a compiler becomes obvious because databases figured out how to handle exactly these problems decades ago.

### Stages as Database Operations

Every compilation stage maps cleanly to database operations. Parsing is inserting rows into the AST table—you're taking unstructured text and producing structured data with a schema. Type checking is a join between expression nodes and type definitions, verifying that the relationships are valid. Optimization is a series of SELECT-WHERE-UPDATE patterns over your SSA values: find instructions matching some pattern, compute their replacements, update the table.

Dependency tracking? That's just foreign keys and graph queries. When function F calls function G, you're recording an edge in a graph that you can traverse to answer questions like "which functions transitively depend on F?" or "what needs to recompile if I change G?" This is a standard database query—you're just running it over compilation artifacts instead of business data.

The shift isn't in what operations you perform, but in recognizing what they are. You're not "traversing an AST"—you're scanning a table. You're not "building a call graph"—you're materializing a view over function references. The database vocabulary gives you precise language for reasoning about what's happening.

### The Fundamental Shift

The deepest change is this: the compiler doesn't "process" code. It queries and materializes views over data.

Traditional compiler thinking: you have source code, you transform it through a pipeline of passes, each one mutating or rebuilding the representation until you get machine code out the other end. It's a manufacturing metaphor—raw materials go in, finished product comes out.

Database thinking: you have source code that gets loaded into canonical storage. Each compilation stage constructs a view over that storage optimized for its access patterns, does its work, and writes results back to canonical storage. The data persists. The views are ephemeral. Compilation is query execution, not manufacturing.

This shift in perspective makes everything else fall into place. Incremental compilation becomes cached query results. Parallelism becomes concurrent read-only queries with partitioned writes. Performance optimization becomes choosing the right indexes and data layout for your query patterns. The architecture writes itself once you have the right frame.

## What This Model Forces You To Do

### Design Constraints

Once you commit to "compiler as database," certain design decisions become forced moves. Data-Oriented Design stops being an optimization technique and becomes the natural architecture. You can't have a database where each row is a scattered object graph with pointers everywhere—databases use columnar storage because analytical queries (which compilation stages are) need to scan large amounts of data efficiently. So your FunctionTable isn't an array of Function objects. It's separate arrays: one for names, one for type signatures, one for bodies. When type checking scans functions, it loads only the columns it needs, packed tightly in cache.

Phases become pure functions with clear contracts. In a database, you don't have queries that secretly reach into other queries' internal state. Each query declares its input schema and produces an output schema. Same for compilation phases: Parse takes source text and produces an AST table. Resolve takes AST tables and produces symbol tables plus resolved ASTs. The phase boundary is a table schema. You can't cheat and have Resolve directly mutate Parse's data structures—the architecture won't let you.

Incremental compilation stops being a heroic feature you bolt on later and becomes a natural consequence of the model. Databases have solved this: it's called materialized views and cached query results. If the input hasn't changed, return the cached output. If a dependency changed, invalidate transitively and recompute. You're not inventing something new—you're applying a solved problem to your domain.

### Emergent Properties

Parallelism falls out almost for free. Database queries over read-only data are trivially parallelizable—each thread scans its partition, no coordination needed. Your type checking phase becomes: for each function in parallel, read from the AST table, read from the type table, verify consistency, write to the type-checked table. No locks, no synchronization within the phase. You only need to sync at phase boundaries when committing the new generation.

Testing becomes dramatically easier because you can mock at the data layer instead of the code layer. Want to test the dependency graph builder? Create a small SymbolTable with a few test entries, run the builder, verify the output DependencyGraph has the right edges. You don't need to set up an entire compiler pipeline, parse real source files, or maintain complicated test fixtures. The phase is a pure function over tables—just provide the input tables and check the output tables.

Perhaps most importantly, the model prevents you from hiding complexity. You can't have a tangled ball of mutable object graphs where everything reaches into everything else's internals, because there are no object graphs—there are only tables. You can't have implicit global state, because databases don't work that way. Every dependency is explicit, every data flow is visible in the table schemas. The architecture enforces discipline not through linting rules or code review guidelines, but through the fundamental structure of the system.

### Architectural Discipline

This is the real leverage. The "compiler as database" model isn't just about performance or incremental compilation—it's about having an architecture that doesn't collapse under its own weight as you scale. Clear boundaries mean you can reason locally. Explicit schemas mean you know what data each phase needs. Pure functions mean you can test and swap implementations independently.

When someone asks "where should I add this feature?", the answer is obvious from the data flow. Need a new optimization? It's a query over the IR table that produces a transformed IR table. Need to track additional metadata? Add a column to the relevant table. Need to change how symbol resolution works? Swap the implementation of that phase—the input and output schemas stay the same, so nothing else breaks.

The architecture provides leverage instead of fighting you. Each new feature builds on solid foundations instead of wobbling atop accumulated hacks.

## Storage: Generations and Content Addressing

### Two-Generation Model

The storage model is surprisingly simple: you maintain exactly two generations of data at any time. Generation N is the current, stable state—completely read-only. All queries during compilation read from N. Generation N+1 is where writes go during the current compilation. As you parse files, resolve symbols, and build dependency graphs, results flow into N+1.

Unchanged data doesn't need to be recomputed or copied—it just carries forward from N to N+1 via copy-on-write semantics. If function 42 hasn't changed, you don't write anything for it in N+1. When you query for function 42, you check N+1 first, don't find it, fall back to N. The function is logically present in N+1 without consuming any additional storage.

If compilation fails halfway through—parse error, type error, internal compiler bug, anything—you simply drop generation N+1. Generation N remains untouched and valid. You can retry immediately with no cleanup needed, no corrupted state to recover from. This is the key insight: keeping N read-only until N+1 is fully built means you can cancel and drop N+1 at any time with zero consequences.

When compilation succeeds, you atomically commit: generation N+1 becomes the new current generation, N becomes old_generation (kept around briefly), and you drop N-1 (which is now two generations back). At steady state, you always have exactly two generations in memory: the current one, and the one being built.

### Why This Works

This is Multi-Version Concurrency Control (MVCC) from database literature. Databases figured out decades ago that the cleanest way to handle concurrent reads during writes is to make writes go to a new version while reads continue seeing the stable old version. You get consistency (readers never see partial updates), isolation (writers don't block readers), and simple failure recovery (abort = drop the new version).

For compilers, this solves incremental compilation elegantly. You're not trying to surgically update data structures in place while tracking what's still valid. You're building a new generation that includes only the changed parts, with everything else carrying forward. The granularity of change detection is whatever you want—per function, per module, per statement—because unchanged items simply don't appear in N+1.

Garbage collection is trivial. You keep exactly two generations: current and being-built. After commit, you drop the generation that's now two behind. No reference counting, no mark-sweep, no worrying about cycles. The generation boundary is the GC boundary.

### Change Detection

Content hashes tell you what actually changed. Each item (function, test, module) has a hash computed from its source content. When you start a new compilation, you hash the input sources and compare to the hashes stored in generation N. Different hash means the item changed and needs recomputation. Same hash means carry forward.

Critically, the hash is metadata used for change detection—it's not the storage key. Your FunctionTable is still keyed by function_id, not by content hash. This matters because you want stable identifiers across compilations. Function 42 is always function 42, even if its implementation changes. The hash just tells you whether function 42 needs recomputation or can be carried forward from the previous generation.

Dependencies propagate through hashes too. If function A calls function B, and B's hash changes, then A's effective input has changed even if A's source text is identical. Your dependency graph lets you compute: "which items transitively depend on this changed hash?" Those all need recomputation. Everything else carries forward.

The beauty is that if you change whitespace or comments, the semantic hash (computed after parsing) might be identical even though the source hash changed. So parsing reruns, but nothing downstream invalidates. The system automatically collapses unnecessary work based on semantic equivalence, not textual equivalence.

## Computation: Phases as Queries

### Phase Structure

Each compilation phase does exactly one thing and has a crystal-clear contract: specific input tables, specific output tables. This isn't just good software engineering—it's the only way to make the database model work. Phases are queries over the database, and queries have schemas.

The phase boundary is where you synchronize parallel work, where you commit data to the next generation, and where you can reshape data layout if needed. Conflating multiple responsibilities in one phase means you can't cache the intermediate results independently, can't parallelize the sub-tasks differently, and can't reason about what changed and what needs recomputation. More importantly, the code becomes an incomprehensible mess because you've violated the single-responsibility principle at the architectural level.

Consider what happens when you keep phases focused: you can test each one in isolation by mocking its input tables. You can profile each one separately and immediately identify bottlenecks. You can swap implementations (try a different parsing algorithm, a different IR representation) without touching any other phase. The phase is a pure function—same inputs always produce same outputs. This is the leverage that makes large systems maintainable.

### Phase Benefits

More phases means more sync points, which sounds like overhead. But if each phase is focused and fast, the sync overhead is negligible. And you gain precise incremental recomputation—you can cache at every phase boundary, only rerunning the stages that actually need it. You gain clear testing boundaries—mock the input tables, verify the output tables, done. You gain swappable implementations—the contract is the table schema, not internal data structures.

The alternative—fewer, larger phases that do multiple things—means you can't cache intermediate results (what would you cache?), can't test sub-components (they're not separated), and can't reason about what needs recomputation (too much conflated). The phase boundaries are where the architecture lives. Get them right, and everything else is just implementation details.

## Performance: Views and Data Layout

### Storage Strategy

There's a fundamental tension in how you store compilation data. The canonical storage needs to be optimized for persistence and incremental updates—you want stable identifiers, efficient change detection, and compact representation. But each compilation phase has different access patterns. Type checking wants to traverse functions in dependency order with type information prefetched. Optimization wants hot and cold code separated. Code generation wants a tiny working set, processing one function at a time.

The solution is the same one databases use: one canonical representation, multiple views. The canonical storage is your source of truth, optimized for the common case and for incremental updates. But each phase constructs an ephemeral view over that storage, reshaped for its specific access patterns. The view exists only for the duration of that phase, then gets discarded. The next phase builds its own view optimized for what it needs.

This sounds expensive—aren't you constantly copying data around? But the reshape cost is predictable (no pointer chasing, just linear scans), parallelizable (each thread can build its portion of the view independently), and far cheaper than the cache misses you'd suffer from using the wrong data layout. If your type checking phase spends 30% of its time waiting on DRAM because function data is scattered across the heap, spending 5% of total time to build a properly-laid-out view is a massive win.

### Phase-Specific Views

What do these views actually look like? The type checker wants functions sorted by dependency order—when checking function F, all the functions it calls were just checked and are hot in cache. It wants type signatures co-located with function bodies (denormalized) so one cache line fetch gets both. It might want a prefetch buffer for commonly-used types so you're not constantly hitting the type table.

The optimizer wants a different layout entirely. Hot functions (frequently called) should be in one contiguous region of memory, cold functions elsewhere. The instruction stream for each function should be laid out linearly so scanning for patterns is just a sequential read. If you're doing multiple optimization passes, you might use a double-buffering scheme: read from one instruction buffer, write transformed instructions to another, then flip between passes.

Code generation wants yet another layout. The working set per function should be tiny—small enough to fit entirely in L2 cache. Register allocation state might be stack-allocated rather than heap-allocated because it's so ephemeral. The instruction emission buffer is just a growing vector that gets written once and never read during codegen.

### Performance Principles

The key insight is that recomputation should be cheap enough that you don't need to cache aggressively. If type-checking one function takes 50 microseconds, just redo it when its dependencies change. Don't build a complex caching system to avoid 50 microseconds of work—the bookkeeping will cost more than the computation. This is the opposite of what most compilers do, where everything is cached because the data structures are so hostile to traversal that recomputation is expensive.

Cache only what's genuinely expensive: parsing (if you have a complex grammar), optimization passes (especially dataflow analysis), code generation. For fast operations over well-laid-out data, just recompute. The simplicity is worth more than the microseconds.

Within a phase, parallelism comes from working on disjoint data. Each thread processes its own functions, its own modules, its own partition of the work. No locks, no atomics, no coordination until you sync at the phase boundary to commit results to the next generation. This works because the data model enforces it—phases read from generation N (read-only, shareable) and write to generation N+1 (partitioned, no contention).

The reshape operations between phases are where you pay the coordination cost: collecting results from all threads, compacting into the next generation's canonical storage, maybe building indexes for the next phase's queries. But this happens once per phase, not continuously during the phase's execution. The sync points are explicit, predictable, and parallelizable (each thread can compact its own partition).

### The Trade-Off

More phases means more reshape operations. But it also means each reshape is simpler (you're transforming less data), each phase has better cache behavior (working on optimally laid-out data), and you get precise incremental caching at every boundary. The overhead of reshaping is linear and predictable. The cost of bad data layout is random cache misses that can dominate execution time.

The test is simple: profile a phase. If you're spending significant time on memory access rather than actual computation, you need a better view. If your cache miss rate is low and you're doing real work, the view is good enough. The model gives you a framework for diagnosing performance problems—is this phase cache-bound or compute-bound?—and a clear path to fixing them: build a better view.

## How You Know It's Working

### Performance Metrics

The simplest test: can you compile 10,000 functions with one changed in under 100 milliseconds? This isn't an arbitrary number—it's the threshold where incremental compilation feels instant to developers. If your model is right, this should be achievable because you're only recompiling what actually changed, and the dependency graph tells you exactly what that is.

Can you predict performance impact by reasoning about view invalidation? When someone proposes adding a new piece of metadata to functions, you should be able to say: "That will invalidate the type checker's view because it includes function metadata, so type checking will need to rebuild its view. But optimization doesn't use that metadata, so its view won't change." If you can't reason about performance without profiling, your model isn't giving you enough structure.

The deeper test is whether your performance intuitions are reliable. In a well-architected system, you should be able to look at a proposed change and predict whether it will be fast or slow based on what data it touches and how it accesses it. Random performance surprises—where innocent-looking changes cause 10x slowdowns—are a sign that the architecture isn't providing the mental model you need.

### Developer Experience

Can a new contributor understand where to add their feature by looking at the data flow? This is the real test of architectural clarity. They should be able to trace: "My feature needs information from parsing and symbol resolution, so it should be a phase that takes ParsedAST and SymbolTable as inputs." They shouldn't need to understand the entire compiler—just the schemas of the tables they're reading and writing.

Can you test phases in isolation? If testing the dependency graph builder requires setting up an entire compiler pipeline, something is wrong. You should be able to write a test that creates a minimal ResolvedAST table with a few entries, runs the builder, and checks that the output DepGraph has the expected edges. The phase is a pure function over tables—testing it should be trivial.

Can you swap a phase implementation without touching other phases? Want to try a different parsing algorithm? The contract is "produces AST table with this schema"—change the implementation, run the tests, done. If swapping an implementation requires coordinating changes across multiple phases, your phase boundaries are in the wrong place or your schemas are too tight.

But perhaps the most revealing question: are you at war adding daily features, or does it feel natural? In a system with good architecture, most features should feel like they slot into an obvious place. You're building on solid foundations rather than navigating around accumulated complexity. New code extends the system rather than fighting it.

### Architectural Health

The ultimate test is whether the architecture provides leverage. When you add complexity—a new optimization pass, a new language feature, better error messages—does the architecture help you manage that complexity, or do you have to fight against it?

Leverage means you can reason locally. You can understand a phase without understanding the entire compiler. You can change a phase without worrying about invisible dependencies. You can predict performance without heroic profiling efforts. The architecture makes the hard things possible and the easy things easy.

The absence of leverage is what we described in "Why Compilers Collapse": every change is expensive, every feature requires understanding everything, velocity approaches zero. If adding features feels like pushing boulders uphill, hoping they don't roll back and crush you—that's the absence of architecture, not the presence of difficulty.

A good architecture doesn't make compilation easy. Compilation is genuinely hard. But it makes the hardness manageable. It provides structure that doesn't collapse under its own weight. It gives you invariants you can rely on and boundaries you can reason across. It turns compiler engineering from an exercise in archaeology into an exercise in design.

This is what "compiler as database" gives you: not a silver bullet that makes everything simple, but a coherent model that makes the complexity tractable. The database framing, the generational storage, the phase boundaries, the view-based layouts—these aren't independent tricks. They're all consequences of taking one good idea seriously and following it to its natural conclusions.
