# Tacit ideas / Syntax / Expressions and blocks

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
