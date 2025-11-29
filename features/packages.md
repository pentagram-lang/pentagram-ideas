# **Pentagram Package System: High-Level Concept**

Pentagram’s package system is designed to grow organically with a project, supporting simple beginnings, seamless evolution, and advanced layouts — all without ceremony. Packages emerge directly from the filesystem and gain identity only where the author chooses to introduce it. The system keeps private development frictionless while ensuring fully consistent external dependencies.

The result is a structure that is predictable, decentralized in shape, centralized in resolution, and completely intention-driven.

---

## **1. Packages Emerge from the Filesystem**

A directory becomes a package the moment it contains a `_package` file.
Before that, it is simply part of its parent package’s internal structure.

There are no required fields.
No name declarations.
No version numbers.
No publishing metadata.
Identity comes entirely from the directory’s place in the project tree.

This lets local packages appear and evolve naturally, without bureaucracy.

---

## **2. A Single Root Package Forms the Local Universe**

Walking upward from any file, the first directory containing a `_package` is considered the project’s root package. This root provides the namespace prefix `pkg`, which describes the local package hierarchy.

Every package under the root has a name derived from its directory path:

```
pkg.x.y.z
```

This name does not distinguish between subpackages and submodules — the only difference is whether the directory contains a `_package`.

This gives the project a coherent internal namespace without global registration or configuration.

---

## **3. Subpackages Are Created Intentionally**

A folder becomes a subpackage only when it has its own `_package` file. Otherwise, it is part of its parent’s internal structure.

This lets authors start with a single package and later introduce subpackages simply by adding `_package` files, without refactoring imports or restructuring code. Package boundaries appear only when they serve clarity.

---

## **4. Local Packages Need No Versions or Metadata**

Local packages never have versions.
They evolve together as part of the same tree.

Because they are not published artifacts and do not participate in global resolution, they require no versioning, no licensing fields, and no names beyond their structural prefixes.

This keeps private development fluid and effortless.

---

## **5. External Dependencies Are Declared by Intention**

Pentagram separates intention from resolution:

- `_deps` expresses the direct dependencies the author intends to use
- `_lock` records the fully resolved, pinned dependency universe

Both are located in the root package, and both apply to all packages beneath it unless overridden.

Local packages do not appear in either file; they are not versioned, pinned, or published.

This separation gives projects clear intent in human-readable text, and deterministic resolution for machines.

---

## **6. Version Resolution Is Centralized at Lockfile Boundaries**

While package structure can be deeply nested and richly decentralized, version resolution is centralized under a single `_lock` file at the root.

Within any lockfile boundary:

- all external dependencies resolve to one consistent version
- no sibling package can diverge
- the entire subtree shares a unified dependency universe

A nested package can form its own dependency universe by introducing its own `_deps` and `_lock`, creating a clean, isolated boundary when needed.

This provides decentralization of structure and autonomy, while ensuring determinism and consistency in dependency resolution.

---

## **7. Local Dependencies Use the Reserved `pkg` Namespace**

A subpackage depends on another local package by referring to its structural name:

```
pkg.something
```

Because `pkg` is reserved exclusively for the local project, these references are always unambiguous and always resolve internally. No path rewrites, no relative imports, no special rules.

Local structure is first-class and frictionless.

---

## **8. Everything Works Without Ceremony**

A blank directory is a working project.
A `_package` file turns a folder into a package.
A nested `_package` creates a subpackage.
No configuration files are required.
No workspace definitions.
No manifests beyond the optional dependency declarations at the root.

The system adapts to the structure the author creates, not the other way around.

---

## **9. Intention Is Visible; Machinery Is Contained**

Pentagram keeps the developer’s intent in plain text:

- `_package` declares identity
- `_deps` declares intended external packages

And everything mechanical:

- dependency graphs
- resolved versions
- integrity information
- transitive closures

lives in `_lock`, where it cannot confuse or clutter the conceptual model.

The design keeps reasoning crisp, visible, and coherent.

---

## **10. A Package System for Real Projects and Real Growth**

Pentagram packages support:

- small, simple scripts
- modular applications
- monorepos with deep nested packages
- local refactoring without churn
- isolated subtrees with independent version universes
- deterministic builds
- zero-friction private development

The structure grows with the project — always clear, always intentional, always deterministic.
