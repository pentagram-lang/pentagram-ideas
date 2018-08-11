# Tacit ideas / Syntax / Assignment and declaration ordering

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
