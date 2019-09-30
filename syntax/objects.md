# Tacit ideas / Syntax / Objects

Definition (same scope rules as assignment):
```
tree = [size = 32; age = 90] obj
vec2 /= x y =/ obj:
  x = x
  y = y
```

Property set and change:
```
a = 1 3 vec2
b = 8 a.set-x
c = [2 +] a.chg-x
```

Referencing properties directly (`self` implied):
```
vec2 /= x y =/ obj:
  x = x
  y = y
  magnitude /=
    x x * y y * + sqrt
  scale /= k =/
    k x *  k y *  vec2
```

Referencing properties through `self` keyword (`self` only usable when declared as a method parameter):
```
vec2 /= x y =/ obj:
  x = x
  y = y
  magnitude /= self =/
    self.x self.x * self.y self.y * + sqrt
  scale /= k self =/
    self.x = k self.x *
    self.y = k self.y *
    self
```

When `self` is used, existing references can stay (changed automatically by `tacit format`):
```
vec2 /= x y =/ obj:
  x = x
  y = y
  swap /= self =/
    self.x = y
    self.y = x
    self
```

Method overloads:
```
vec2 /= x y =/ obj:
  x = x
  y = y
  add /= other =/
    x other +  y other +  vec2
  add /= other =/
    x other.x +  y other.y +  vec2
```

Nested assignment (creates new copies of sub-objects at each level):
```
a.b.c = 8
```

## Automatic properties and methods

Automatic properties:
```
vec2 /= x y =/ obj:
  = x
  = y
```

Automatic properties through other objects:
```
vec2 /= vec2 z =/ obj:
  = vec2
  = vec2.x
  = vec2.y
```

Automatic methods:
```
my-math = obj:
  /= math.sin
  /= math.+
```

## Point-free accessors

Basic point-free properties and methods:
```
a .x  b.y  *  .z
3  x first  .f
```

Nested accessors:
```
a b * .z.y.x
```

Set and change accessors:
```
mod /= . 3 .x=  . [4 +] .y&=
```

Nested accessors:
```
a b *  .z.y.x
a b *  . 4 .z.y.x=
a b *  . [4 +] .z.y.x&=
```

Chained method calls with arguments (no swap/rotate needed):
```
mod /= object =/
  object
    . 3 .compute
    . [4 +] .transform
    . 1 2 3 .print
    . 1 2 .print-with: z =/
      z 3 *
```

## Multiple `self` variables

To avoid storing `self` in a new variable, all `self-*` variables are special:

```
f /= x self-module =/
  obj:
    g /= y self =/
      obj:
        h /= z self-inner =/
          x y z
```

## Inheritance

_Not supported (use automatic properties and methods instead)_

## Change syntax

Nothing beyond property assignment (wouldn't add much):

```
translated = points
  map: point =/
    point.x &= 2 +
    point.y &= 3 +
    point
```

## Property namespaces

_One global namespace, so be specific when overloading core methods_

## Extension methods

Syntactic extension only where imported (does not monkey-patch):

```
xy /= extend-self =/
  vector.x vector.y

set-xy /= x y extend-self =/
  extend-self.x = x
  extend-self.y = y
  extend-self
```
