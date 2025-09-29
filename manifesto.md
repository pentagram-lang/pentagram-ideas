# The Pentagram Manifesto

## Background

### Modern perks

Programmers have never had more at their fingertips. Code is high level and has safety built in as a core feature. Toolchains are deep, runtimes are stable, ecosystems are vast. LLMs can summon code, patterns, and insights in ways that feel like science fiction finally arriving.

### Obtuse inevitability

And yet, stumbling blocks remain. Languages that are awkward to read and write, but whose champions call this “just how it must be.” Abstractions that grow baroque, explained away as sophistication. Every keystroke of friction defended as inevitable -- laws of physics rather than a design failure.

### Persistent confusion

For the foundation: twisted webs of shared, mutable state, enshrined as though object-orientation were some ideal. The religion so strong that it has become heresay to point out how this erodes clarity, eliminates reason, and leaves both humans and machines struggling in the dark.

### Cycle tax

Even the pursuit of speed is muddled. Real gains require endless tuning and ritual, while the base performance of programs wastes compute by default. Cycles are lost not to the substance of the work but to the indifference of the environment.

## Aspirations

### Reset

Language and culture are inseparable: the constructs of a language shape how programmers think, and the norms of the culture reinforce the language in turn. This feedback loop hardens over decades, making its flaws appear natural, even inevitable. Only a new language can break that loop. Only a recalibrated core can enable new goals, new practices, and a culture that embraces clarity, kindness, and power all at once.

### Ergonomics

Comfort and productivity in reading and writing code are not luxuries, but multipliers. A language should minimize friction: syntax should aid comprehension, not obscure it. Structures should support thought, not bend it. Ergonomics here means more than aesthetics — it means designing with respect for human cognition and making programming feel natural, humane, and clear.

### Determinism

Confidence in correctness is what transforms code from a liability into an asset. By eliminating shared, mutable state from the foundation, a language can make programs predictable and reasoning straightforward. Determinism is not a constraint but a liberation: it frees programmers from the fog of hidden interactions and enables higher-level reasoning about algorithms, data structures, and systems — drawing computation closer to its mathematical roots. Both humans and machines can reason with confidence, guided by clarity that can only come from the composition of simple values.

### Efficiency

As for performance, saving CPU cycles should not require endless incantations or ritual optimization. A well-designed language ensures that programs run fast by default. This is not some abstract concern: baseline performance has real environmental impacts and directly affects the utility of programs written in the language.

## Design

### Typography

In Pentagram, everything begins at the keyboard. Symbols are chosen to type cleanly on most layouts without gymnastics. Common operations are given the shortest names, a kind of linguistic Huffman coding. Whitespace is fluid in editing (cleaned up in auto-formatting), with unambiguous fences defining overall program structure.

### Flow

Code flows in the order of thought. Postfix form is the default, so values read as they transform, with prefix mode available when multi-line expression trees are more clear. Records have sugar for scoped field-access functions, and functions are never bound to records. Type annotations use the same grammar as expressions, so code and its types share a single form of reasoning.

### Trust

Values are immutable. Full stop. Code reads as mutation, but this is actually lexical replacement. Actual side effects require function calls. Errors are always explicitly propagated or captured call sites. Exports are an easy opt-in, and escape hatches exist.

### Power

Types are nominal and strict, null is never implicit, and generics with higher-order traits enable advanced expressivity. Tagged unions are narrowed through plain conditionals rather than a separate pattern-matching sub-language.

### Runtime

Pure values are copy-on-write with compiler proofs to skip the copy, making safety fast. Concurrency is structured and green-threaded, with asynchronous operations allowed in any functions. Modern async IO (e.g. io_uring) is the only IO.

### Meta

All forms of regularity and predictability help humans learn and refactor, and help machines model and assist. The clarity that benefits people is the same clarity that strengthens automated tooling.
