# Tacit ideas / Syntax / Advanced methods

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
