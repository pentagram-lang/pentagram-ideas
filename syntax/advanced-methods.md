# Tacit ideas / Syntax / Advanced methods

## Configuration parameters

Explicit `obj` not needed, strictly evaluated:

```
paint /= x y conf =/
  conf &= configure:
    color = blue

1 4 [] paint
1 4 [color = red] paint
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
