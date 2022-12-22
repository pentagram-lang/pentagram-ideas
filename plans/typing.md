# Pentagram ideas / Plans / Typing

The langauge will use explicit, static, nominal typing. This supports the goals of ergonomics, determinism, and performance.

Explicit typing is import for ergonomics and determinism, because once you write an explicit type into code, then anyone looking at the code in the future will know what type is used, and any invalid changes will introduce errors near the source instead of in far-away usages. Implicit typing can be inferred by the compiler, but explicit types will be introduced into code by the automatic formatter.

Static typing is for performance, to allow erasing runtime type metadata and runtime type checks. Type metadata and type checks can still be used at runtime, but won't be required for many cases.

Nominal typing also supports ergonomics and determinism. Because subtyping is not allowed, the system avoids an entire calculus of union and intersection types. The determination of dispatch targets is very clear, based simply on a types name. Each type is self-contained and without side effects -- there are no reasons to worry about field names defined in other modules. Nominal typing is also useful for similar-shaped types with dissimilar semantics.

## Method signatures

If a method signature has any type annotations specified, annotations must be specified to cover all parameters and return values. When there is no return value, a "no-op" should be specified.
