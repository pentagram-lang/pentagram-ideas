# Tacit ideas / Syntax / Assignment and definition ordering

Assignment can only reference previous assignment/definition:
```
x = 1
f >> 3 +
y = x f
```

Definition can reference previous assignment/definition:
```
x = 1
y = 2
f >> x +
g >> y * f
```

Definition can also reference other definitions in current group:
```
f >> 1 +
x = 2
g >> a >
  a x lt if:
    0
  else:
    a 1 - h  3 *
h >> a >
  a x lt if:
    0
  else:
    a 1 - h  4 *
```

Repeat assignment produces new variables, all previous definitions stick to original variable at time of definition:
```
x = 1
f >> x +
x &= f
```

Using `self` access syntax refers to the current object:
```
obj:
  f >> self > self.x +
  x = 1
  g >> self > self.x *
  x = 2
```

_`self` methods cannot be called until all property assignments are complete_

## Ideal sorting

_`tacit format` will sort to have assignments first, followed by definitions, as allowed by the DAG, sorted alphabetically_

## Assignment destructuring

Assignment can be used to pull from the call stack for simple cases (no required-name parameters, no optional or default values):
```
d m = 9 4 div-mod
x y = vec.points ..
head ..tail = [1 2 3] arr ..
vec2 >> type:
  x/f32
  y/f32
  =
```
