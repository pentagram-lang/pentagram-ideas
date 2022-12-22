# Tacit ideas / Plans / Modules

## Package management system

Every Tacit file represents a module, and modules are organized into packages. (Submodules can also be defined within a single source file.) Packages have their own settings, and can depend on other packages.

When initializing a new package, you will get prompted for a package identifier (default suggestion based on source control, or based on current logged in user & path). This top-level identifier will be used for all references within the package itself.

There is no relative addressing of local modules. All local module references need to use the full path to the module.

There is no hierarchy system or centralized registry for package names. All package names are top-level, all defined in their respective packages in source control. When using another package, you have the option of overriding the package name for local references in code.

Package references are added via source control URLs. When adding a package reference, Tacit automatically uses semantic versioning. The system "locks" in place the latest current version, and allows simple upgrades to compatible semantic versions. (This locking can be overridden to pin to specific versions or Git revisions.) Any potentially incompatible upgrades (including in the `0.x` range) trigger warnings with release note links.

A `pkg.tacit` file controls the developer's rules for referencing packages, and a `pkg.lock.txt` file tracks the exact (semantic-compatible) versions and Git revision each package. (Tacit will check that the lock file is included or excluded from source control correctly.)

## Index modules

To help ease the boundary between modules, files, and directories, `index.tacit` modules get used whenever a parent directory is referenced.

_(Note: member and submodule name collisions are not allowed.)_

It's also possible to have a module file named the same as an existing directory, which is equivalent to having that file called `index.tacit` and located inside the directory. For consistency, this will generate a warning and an automatic fix.

## Prelude

The standard Tacit prelude doesn't have any of its own definitions, it re-exports from other modules. For example, the map method comes from the standard collections package (as a method linked into the root package index).

The prelude for a package can be configured. You can switch to any module, including a published "empty prelude" module. For example, you can also define your own prelude and re-export everything in the standard prelude plus your own extensions.

## Runtime

Some functionality isn't defined directly in Tacit modules, instead comes from runtime modules defined by the compiler.

## Top-level effects

_Not allowed, because all property values are determined (and cached) at compile-time_

_Also, non-assignment/definition statements allowed in object blocks_
