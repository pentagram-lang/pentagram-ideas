# Tacit ideas / Syntax / Packages and modules

## Imports

Import a link to a module:
```
math += pkg.math.core
```

Import a link to a module using a default name binding:
```
+= pkg.math.core
```

Import a link to a specific property or method:
```
+= pkg.math.pi.core
+= pkg.math.cos.core
```

Import links to all names directly:
```
++= pkg.math.core
```

The benefits of linking vs. assignment syntax are...
- it works for methods and properties
- it doesn't trigger any copies
- it doesn't trigger any exports
- it allows for overloading

## Overloading

Overload an imported (linked) method (`.` required):
```
math.cos /= n =/
  n.real math.cos  n.imaginary math.cosh  *
  math.i  n.real sin  n.imaginary math.sinh  * *
  -
```

Create a global extension method (imported by default by all modules):
```
global-extensions.my-prop2 /= extend-self =/
  extend-self.my-prop 2 *
```

_(The overload target's module needs to be in scope via "linking" syntax not copy/assignment syntax for the overload to be globally available.)_

## Exports

Export a property or method:
```
squared /= dup *
zero = 0
```

Import & export a link to a module:
```
math += pkg.math.core
/= math
```

Import & export a link to a method:
```
++= pkg.math.core
/= sin
```

Hide an export by default:
```
-hidden-x = 1
-hidden-f /= y =/ hidden-x y +
```

Use a hidden export:
```
z = m.-hidden-x 2 *
```

## Top-level effects

_Not allowed, because all property values are determined (and cached) at compile-time_

_Also, non-assignment/definition statements allowed in object blocks_

## Package management system

All packages and modules under the `pkg` identifier are external packages that can be referenced by the current package being developed.

The first level under `pkg` is a tag/categorization system. `core` is the name for packages that can be referenced by default without installing. Others could include `web`, `file`, `gfx`, `input`, `net`, `domain`, etc.

The second level under `pkg` is the actual package name. For example the `snappy` of `pkg.data.snappy`. Or there can be three levels if the maintainer wants to have the package scoped to a person or organization.

(Any identifiers after the second level are methods and modules inside of a package.)

The package name and tag defaults are specified by the author of the package (and can be changed), and can be overridden by the developer using the package (e.g. to avoid name conflicts).

When adding a package reference, Tacit automatically uses semantic versioning. The system "locks" in place the latest current version, and allows simple upgrades to compatible semantic versions. (This locking can be overridden to pin to specific versions or Git revisions.) Any potentially incompatible upgrades (including in the `0.x` range) trigger warnings with release note links.

A `pkg.txt` file controls the developer's rules for referencing packages, and a `pkg.lock.txt` file tracks the exact (semantic-compatible) versions and Git revision each package. (Tacit will check that the lock file is included or excluded from source control correctly.)

## Local-package modules

For the package being developed, all modules are accessible under package's official name. The default name if not specified is the special `app` name.
```
+= pkg.default.app.parser
math-utils += pkg.default.app.utils.math
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
+= pkg.default.app.a
+= pkg.default.app.b
+= pkg.default.app.b.c
+= pkg.default.app.b.d
+= pkg.default.app.b.e
+= pkg.default.app.b.e.f

[a b c d e f] say
```

_(Note: object definitions for modules get merged.)_

It's also possible to have a module file named the same as a directory, which is equivalent to having that file called `index.tacit` and located inside the directory. For consistency, this will generate a warning and an automatic fix.
