# Tacit ideas / Syntax / Modules

Import a link to a module:
```
math += modules.core.math
```

Import a link to a module using a default name binding:
```
+= modules.core.math
```

Import a link to a specific property or method:
```
+= modules.core.math.pi
+= modules.core.math.cos
```

Import & export a link to a module:
```
core-math += modules.core.math
/= core-math
```

Import & export a link to a method:
```
++= modules.core.math
/= sin
```

Overload an imported (linked) method (`.` required):
```
math.cos /= n =/
  n.real math.cos  n.imaginary math.cosh  *
  math.i  n.real sin  n.imaginary math.sinh  * *
  -
```

Export a property or method:
```
squared /= dup *
zero = 0
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

Import links to all names directly (can't be used to create properties/methods):
```
++= modules.core.math
```

Import from a local-project module (from root):
```
+= local.shell
```

## Top-level effects

_Not allowed, because all property values are determined (and cached) at compile-time_

_Also, non-assignment/definition statements allowed in object blocks_
