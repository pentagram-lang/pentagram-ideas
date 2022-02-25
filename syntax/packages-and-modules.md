# Tacit ideas / Syntax / Packages and modules

## Imports

Import a link to a module:
```
math << pkg.tacit-lang.math
```

Import a link to a module using a default name binding:
```
<< pkg.tacit-lang.math
```

Import a link to a specific property or method:
```
<< pkg.tacit-lang.math.pi
<< pkg.tacit-lang.math.cos
```

Import links to all names directly:
```
*<< pkg.tacit-lang.math.cos
```

The benefits of linking vs. assignment syntax are...
- it works for methods and properties
- it doesn't trigger any copies
- it doesn't trigger any exports
- it allows for overloading

## Overloading

Overload an imported (linked) method (`.` required):
```
math.cos >> n/complex >
  n.real math.cos  n.imaginary math.cosh  *
  math.i  n.real sin  n.imaginary math.sinh  * *
  -
```

Create an extension method:
```
global-extensions.my-prop2 >> extend-self/my-type >
  extend-self.my-prop 2 *
```

Extension methods are only usable when imported. To make "global" add to the current package's prelude.

## Exports

Export a property or method:
```
squared >> dup *
zero = 0
```

Import & export a link to a module:
```
math << pkg.tacit-lang.math
>> math
```

Import & export a link to a method:
```
*<< pkg.tacit-lang.math
>> sin
```

Export links to all methods and properties:
```
*>> pkg.tacit-lang.math
```

Hide an export by default:
```
-hidden-x = 1
-hidden-f >> y > hidden-x y +
```

Use a hidden export:
```
z = m.-hidden-x 2 *
```

## Top-level effects

_Not allowed, because all property values are determined (and cached) at compile-time_

_Also, non-assignment/definition statements allowed in object blocks_

## Package management system

All packages and modules are under the `pkg` identifier.

The first level under `pkg` is an organization or handle. The second level under `pkg` is a source repository name. For example `pkg.tacit-lang.snappy` refers to the `snappy` repository in the `tacit-lang` organization.

Extra levels of identifiers can refer to a package deeper in the repository, or a module or object within a package.

The package name is specified by the author of the package (and can be changed), and can be overridden by a developer using the package (e.g. to avoid name conflicts). A warning is generated when adding if the canonical source control URL doesn't match the package's provided name.

When adding a package reference, Tacit automatically uses semantic versioning. The system "locks" in place the latest current version, and allows simple upgrades to compatible semantic versions. (This locking can be overridden to pin to specific versions or Git revisions.) Any potentially incompatible upgrades (including in the `0.x` range) trigger warnings with release note links.

A `pkg.tacit` file controls the developer's rules for referencing packages, and a `pkg.lock.txt` file tracks the exact (semantic-compatible) versions and Git revision each package. (Tacit will check that the lock file is included or excluded from source control correctly.)

## Local-package modules

For the package being developed, all modules are accessible under package's official name. When initializing the package, a name is suggested based on source control, or based on current logged in user & path.
```
<< pkg.my-handle.my-app.parser
math-utils << pkg.my-handle.my-app.utils.math
```

There is no relative addressing of local modules. All local module references need to use the full path to the module.

## Index modules

To help ease the boundary between modules, files, and directories, `index.tacit` modules get used whenever a parent directory is referenced.

For example:
```
-- index.tacit
a = 1
b = obj:
  c = 2

-- b/index.tacit
d = 3

-- b/e.tacit
f = 4

-- g.tacit
<< my-handle.my-app.a
<< my-handle.my-app.b
<< my-handle.my-app.b.c
<< my-handle.my-app.b.d
<< my-handle.my-app.b.e
<< my-handle.my-app.b.e.f

[a b c d e f] say
```

_(Note: object definitions for modules get merged.)_

It's also possible to have a module file named the same as an existing directory, which is equivalent to having that file called `index.tacit` and located inside the directory. For consistency, this will generate a warning and an automatic fix.

## Prelude

The standard Tacit prelude doesn't have any of its own definitions, it re-exports from other modules. For example, the `smap` method comes from `std-collections.smap` (as a method linked into the root package index).

The prelude for a package can be configured. You can switch to any module, including a published "empty prelude" module. For example, you can also define your own prelude and re-export everything in the standard prelude plus your own extensions.

## Engine

Some functionality isn't defined directly in Tacit modules, instead comes from `_engine.*` modules defined by the compiler.
