# RAII concept for Pentagram

## Goal

Provide a minimal, predictable mechanism for guaranteed resource release that fits Pentagram’s values: clear reasoning, deterministic behavior, and low cognitive overhead. The mechanism must be conceptually simple (easy to state and apply), implicit in normal control flow (including error unwinding), and expressive enough to support real-world resources (files, sockets, locks, native handles, etc.) without turning the language into a minefield of accidental duplication or hidden side effects.

## Core idea

Certain types carry a destructor-like semantic: when an instance’s lifetime ends, the language guarantees a defined, implicit finalization action runs exactly once. Finalization is not a language-level side channel for arbitrary mutation — it is the well-scoped release of external resources or other externally-visible cleanup. The rules governing when finalization runs are intentionally simple so that reading code gives immediate, reliable knowledge of when cleanup will happen.

## Design principles

1. **Simplicity of rules.** The lifetime/finalization model must be expressible in one or two sentences and require minimal mental bookkeeping. Complexity belongs in tooling and compiler proofs, not in language ergonomics.
2. **Deterministic finalization.** Finalizers run at a well-defined point in the program’s control flow, including during error propagation. Programmers and tools can reason locally about resource lifetimes.
3. **Non-surprising.** No hidden copies or implicit cloning that would cause multiple finalizations or postpone release unexpectedly.
4. **Composability.** The mechanism composes with other language features — e.g., implicit finalizers cooperate with immutable values, structured concurrency, and explicit error propagation.
5. **Minimal semantic surface.** The language exposes the idea (this type has guaranteed finalization) without requiring every user to learn a complex ownership calculus.

## Simple rules (conceptual)

- A type may be **finalizable** (i.e., have an implicit finalizer). Finalizability is a trait of the type’s semantics, not of a particular value.
- When a finalizable value’s lexical lifetime ends, its finalizer executes exactly once. The canonical lifetime end is the point where the value is no longer usable by the surrounding scope.
- Finalization is deterministic: in normal return and in error unwinding, the finalizer runs at the same, well-specified program point (scope-exit).
- Finalization is not visible as mutation to the language’s pure-value model except insofar as it affects external resources. Finalizers may perform I/O or call runtime cleanup hooks but do not change the immutable value semantics used for program reasoning.
- Finalizable types are not implicitly duplicated. Any operation that would produce a second live instance must be explicit and carry semantic weight (e.g., wrapping in a shareable container or transferring ownership).
- If a finalizable value is moved into another binding, the move transfers the finalizer; the finalizer remains single and tied to the live instance.

## Relation to immutability and deterministic reasoning

Pentagram’s immutable-by-default model separates value semantics from resource semantics. Finalization is about _responsibility_ for an external resource, not about mutating the value. Reading code should let you infer both the pure data flow (unchanging values) and the lifecycle of resources (when they are released). This keeps reasoning simple: immutability simplifies data reasoning; RAII gives a single, local place to reason about resource lifetime.

## Error handling & exceptions

Finalizers run during error propagation (unwinding). Errors remain explicit in the language’s error model — propagation is visible where it matters — but resource release is guaranteed and implicit. That means:

- Cleanup does not depend on the caller remembering to call a close method.
- Finalizers must be written defensively: they should ideally be idempotent in the face of partial effects (since they run exactly once, idempotence is about robustness to partial external state).
- Finalizers should avoid throwing unchecked errors that obscure original exceptions; the language should define how finalizer failures are reported (for example: aggregated, suppressed with diagnostics, or promoted in a well-specified way). The exact reporting strategy can be deferred to implementation, but the concept doc should state that finalizer failure handling is defined and deterministic.

## Interactions with sharing and explicit duplication

RAII’s utility depends on avoiding accidental aliasing of finalizable resources. Conceptually:

- Finalizable values are single-owner by default. Sharing a resource must be explicit and carry semantic consequences (e.g., reference counting, explicit handles, or borrow-like transient views).
- If a programmer needs a cloneable resource, they wrap it in an explicitly shareable abstraction. The language’s core rules prohibit implicit, silent cloning of finalizable resources.
- This keeps the reasoning model clean: seeing a finalizable value means you know there is one unique cleanup event to consider.

## Safety and guarantees

- **Single finalization guarantee.** For each finalizable instance, the runtime guarantees exactly one finalizer invocation.
- **Scope determinism.** The point of finalizer invocation is a lexical point (scope exit) that is uniform across normal return and error unwinding.
- **No hidden mutation semantics.** Finalizers release external resources; they do not change program-visible immutable values or violate deterministic assumptions about pure computations.
- **Compiler proofs.** The compiler may leverage knowledge of finalizability to elide unnecessary copies, reorder code where safe, and statically check misuse (e.g., use-after-finalize).

## Behavioral expectations for writers

- Treat finalizable types as responsibility-bearing handles. Do not assume they are cheap value-like objects.
- Prefer explicit adapters to convert finalizable resources to shareable forms when needed.
- Write finalizers to be robust: detect and tolerate partially-released external state, avoid throwing in common cases, and emit diagnostics for unrecoverable cleanup failures.

## Why RAII fits Pentagram

- **Ergonomics:** RAII reduces cognitive load by making cleanup automatic and local to lexical structure; programmers don't have to remember explicit close calls.
- **Determinism:** Guaranteed, lexically-tied finalization restores predictability in resource lifetimes and simplifies reasoning for both people and tooling.
- **Efficiency:** Automatic, deterministic release avoids background waits and dangling handles, enabling the runtime to reclaim resources promptly without heavy runtime bookkeeping or conservative garbage collection heuristics.

## Minimal roadmap for a future implementation (conceptual)

1. **Type marker.** Decide how the language denotes a type as finalizable (a minimal semantic flag; no complex syntax needed for this doc).
2. **Lexical lifetime model.** Specify the simple rule for when scope-exit happens (the one-line rule that readers can memorize).
3. **Move semantics.** Define what it means to move a finalizable instance between bindings; ensure the finalizer moves with the instance.
4. **Sharing adapters.** Standardize a small set of shareable wrappers (e.g., reference-counted, immutable handle view) that programmers use to explicitly opt into sharing.
5. **Finalizer failure policy.** Pick a deterministic, simple policy for what happens when a finalizer itself errors during unwinding.
6. **Static checks.** Add compile-time checks to prevent accidental duplication or use-after-finalize patterns.
7. **Tooling.** Provide diagnostics and lints that surface common RAII misuses (e.g., inadvertently holding long-lived finalizable values).

## Closing note

Make the rules memorable and local: a reader should be able to scan a function and immediately know which values are responsibility-bearing and when their cleanup happens. Keep the primitives tiny — finalizability, move-transfer, and explicit sharing — and push complexity into orthogonal libraries and compiler checks. That keeps the language ergonomic, deterministic, and efficient in practice.
