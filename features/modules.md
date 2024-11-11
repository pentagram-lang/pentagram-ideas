# Pentagram ideas / Features / Modules

## Package management system

Every Pentagram file represents a module, and modules are organized into packages. (Submodules can also be defined within a single source file.) Packages have their own settings, and can depend on other packages.

When initializing a new package, you will get prompted for a package identifier (default suggestion based on source control, or based on current logged in user & path). This top-level identifier will be used for all references within the package itself.

There is no relative addressing of local modules. All local module references need to use the full path to the module.

There is no hierarchy system or centralized registry for package names. All package names are top-level, all defined in their respective packages in source control. When using another package, you have the option of overriding the package name for local references in code.

Package references are added via source control URLs. When adding a package reference, Pentagram automatically uses semantic versioning. The system "locks" in place the latest current version, and allows simple upgrades to compatible semantic versions. (This locking can be overridden to pin to specific versions or Git revisions.) Any potentially incompatible upgrades (including in the `0.x` range) trigger warnings with release note links.

A `pkg.penta` file controls the developer's rules for referencing packages and Git revision for each package. Pentagram does not need a lockfile, because an unbroken chain of matching hashes is enough to determine the full dependency tree. Packages versions are installed exactly as specified, and if a package is a dependency multiple times, the newest specified version is used. The syntax for the package file matches the Pentagram AST (but the file is never executed).

## Index modules

To help ease the boundary between modules, files, and directories, `index.penta` modules get used whenever a parent directory is referenced.

_(Note: member and submodule name collisions are not allowed.)_

It's also possible to have a module file named the same as an existing directory, which is equivalent to having that file called `index.penta` and located inside the directory. For consistency, this will generate a warning and an automatic fix.

For package creators, the use of index files, private modules, and re-exporting is encouraged, to create clean & usable package APIs.

## Prelude

The standard Pentagram prelude doesn't have any of its own definitions, it re-exports from other modules. For example, the map method comes from the standard collections package (as a method linked into the root package index).

The prelude for a package can be configured. You can switch to any module, including a published "empty prelude" module. For example, you can also define your own prelude and re-export everything in the standard prelude plus your own extensions.

## Runtime

Some functionality isn't defined directly in Pentagram modules, instead comes from runtime modules defined by the compiler.

## Top-level effects

_Not allowed, because all property values are determined (and cached) at compile-time_

_Also, non-assignment/definition statements allowed in object blocks_
