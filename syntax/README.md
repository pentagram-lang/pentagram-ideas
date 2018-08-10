_Warning: the syntax on this page is all experimental_

***

# Basics

Assignment:
```
x = 1
```

Modification:
```
y &= 8 +
```

Methods:
```
plus /= x y =/
  x y +
```

Lambdas:
```
doubled = [1 2 3] arr [n =/ n 2 *] map
```

Point-free (tacit):
```
plus-one /= 1 +
```

Comments:
```
-- Helpful comment
```

Conditionals:
```
fact /= n =/
  if:
    n 1 gt,
      n  n 1 - fact  *
    else,
      1
```

# Literals

Integers:
```
x = 932
```

Floating point numbers:
```
y = 0.3
z = 8.1e30
```

Booleans:
```
[true false] arr say
```

Strings:
```
'Hello' ' world' cat say
```

## Numeric precision

Integer precision:
```
8i 8int-32 eq
40b 40unsigned-int-8 eq
```

Float precision:
```
1.0f 1.0float-32 eq
1.0d 1.0float-64 eq
```

## String interpolation

Basic variables:
```
'Hello [name]' say
```

No escapes, only two-letter variables:
```
command = '[tb]gcc *.cpp[nl]'
```

Variables have longer synonyms:
```
long-command = '[tab]gcc *.cpp[new-line]'
```

Disable interpolation:
```
'bracket[[' say
```

Double quotes also supported:
```
"I'm a friend" say
```

Triple quotes also supported:
```
'''I'm a friend''' say
```

_No special Tex-like English typographic shortcuts (that are imported by default)_

# Expressions and blocks

One expression can span multiple lines:
```
x =
  1 2 +
  3 -
y = [1 2 3] arr
  map: 2 +
  keep: 3 gt
```

Blocks (delayed expressions) can be inline:
```
x = [1 2 3] arr [4 *] map
```

Blocks can be multiple lines:
```
x =
  arr:
    1
    2
    3
  map:
    2 *
    2 *
```

_Assignment is a statement, not an expression_

_Multiline blocks must be "freestanding" and can only be nested inside other multiline blocks_

# Objects

Definition (same scope rules as assignment):
```
tree = [size = 32; age = 90] obj
vec2 /= x y =/ obj:
  x = x
  y = y
```

Property setters and modifiers:
```
a = 1 3 vec2
b = 8 a.set-x
c = [2 +] a.modify-x
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

Nested assignment (creates new copies of each level):
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

Setter and modifier accessors:
```
mod /= 3 swap .set-x  [4 +] swap .modify-y
```

## Inheritance

_Not supported (use automatic properties and methods instead)_

## Modification syntax

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

# Collections

Arrays:
```
my-array = [1 2 3] arr
my-other-array = arr:
  1 2 +
  2 8 *
  3 2 /
```

Options:
```
x = 7 opt
y = opt-none
z = [x y] arr flat
```

Dictionaries:
```
words = ["ok", 8; "the", 400] dict
numbers = dict:
  1, obj:
    even? = false
  2, obj:
    even? = true
```

Lazy iterators (with splat):
```
fib /= a b =/
 next = a b +
 [next  b next fib .splat] iter
```
# Modules

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

# Exceptions

_?_

# Advanced methods

Optional parameters (`obj` not needed, strictly evaluated):
```
paint /= x y opt =/
  opt &= optional:
    color = blue
```

## Named parameters

_Maybe not needed, overloads can cause conflicts_

## Method guards

_Maybe not required_

## Parameter destructuring

Tuples can be assigned to variables:
```
d m = 9 4 div-mod
x y = vec.points.splat
```

_Other forms too weak, difficult to extend, not supported_

# Assignment and declaration ordering

Assignment can only reference previous assignment/declaration:
```
x = 1
f /= 3 +
y = x f
```

Declaration can reference previous assignment/declaration:
```
x = 1
y = 2
f /= x +
g /= y * f
```

Declaration can also reference other declarations in current group:
```
f /= 1 +
x = 2
g /= a =/
  if:
    a x lt, 0
    else, a 1 - h  3 *
h /= a =/
  if:
    a x lt, 0
    else, a 1 - h  4 *
```

Repeat assignment produces new names, all other assignments/declarations stick to name in current scope:
```
x = 1
f /= x +
x &= f
```

Using `self` access syntax refers to final name:
```
f /= self =/ self.x +
x = 1
g /= self =/ self.x *
x = 2
```

_`self` methods cannot be called until all property assignments are complete_

## Ideal sorting

_`tacit format` will sort to have assignments first, followed by declarations, as allowed by the DAG, sorted alphabetically_

# Pointers

Create and copy an immutable pointer:
```
a = 100_000 primes ptr
b = a
```

Create and copy a mutable pointer:
```
a = 1280 720 pixmap mutable-ptr
b = a
```

Get a pointer's value:
```
cbar-1 /= list =/
  list.value.tail.value.head

cbar-2 /= list =/
  list*.tail*.head
```

Replace a pointer's value (creating a new pointer):
```
replace-cbar-1 /= replacement list =/
  list.value.tail.value.head = replacement
  list

replace-cbar-2 /= replacement list =/
  list*.tail.value* = replacement
  list
```

Write a mutable pointer's value:
```
write-cbar /= replacement list =/
  replacement  list*.tail*.set-head  list*.tail.write
```

# Rich comments

Normal text is captured:
```
-- Some text
```

Any character except `space` causes rich comments to be disabled:
```
--- Disabled
--Disabled
```

Run code:
```
-- @code-variable = 1
-- @code-method /= x =/ x 1 +
```

Switch back to text from inside code:
```
-- @title: My title
-- @aside:
--   Can be a whole new
--   block of text
-- @4pt small-size: Can also be parameterized
```

_Uses everything in scope and unprefixes `doc-`_

Execute code in line:
```
-- For @[strong: inline] markup
-- @[emph: Start] only also works
```

Refer to names in scope (creates link, does not execute):
```
-- Use [+] and [-]
```

Nested comments inside code inside comments is OK:
```
-- @x = 1 -- Assign
```

_To use this syntax outside of comments (for text-heavy code), use a special file extension_

***

# Keyboard symbols (for non-US keyboards)

Tier 1 (friendly):
```
-=;,.!%*()"_+:?
```

Tier 2 (okay):
```
'/@#$&
```

Tier 3 (unfriendly):
```
<>^`[]\~{}|
```
