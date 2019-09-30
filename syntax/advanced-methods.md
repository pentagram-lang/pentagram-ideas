# Tacit ideas / Syntax / Advanced methods

## Named parameters

All overloads in Tacit must have matching parameter names. With that, it's possible to call methods using named parameters:
```
1 +initial  2 +initial 3 +addition +  +addition +
```

Unnamed parameters cannot follow named parameters in a method call, but otherwise, named parameters use the expression stack just like unnamed parameters.

You can call any method by naming its parameters, but you can also define methods parameters that must be specified as named when calling.
```
my-complex-func /= easy +hard +difficult +complex /=
    difficult &= default: 80 sqrt
    [easy hard difficult complex] arr sum

4 2 +  8 +hard  9 +difficult  my-complex-func
```

If named parameters are returned from a method, they must be used to call another method or saved into named variables:
```
f /=
  1 +x  2 +y

+x +y = f
f vec2 say
```

## Splats

Sometimes you want to bridge collections and unnamed parameters or maps/objects and named parameters. You can use splats to do this:
```
my-arr = 1 2 3 splat-arr
my-smap = 1 +x 2 +y 3 +z splat-map
my-obj = 1 +x 2 +y 3 +z splat-obj
head ..tail = my-arr ..
+x +y +z = my-smap +..
+x +z +.._ = my-obj +..
```

You can also define methods that accept a named splat or an unnamed splat:
```
my-complex-func /= one ..params +..nparams /= 
   ?

nop
    my-arr ..
    5
    my-smap +..
    8 +q
    my-obj +..
    my-complex-func
```

## Method guards

_Maybe not required_

## Assignment destructuring

The stack can be assigned to variables:
```
d m = 9 4 div-mod
x y = vec.points ..
+a +b = 2311 +a  8 1 * 9 +b
head ..tail = [1 2 3] arr ..
+id +..rest = ["id", 1; "data", 8] smap +..
```

_Other forms (including parameter destructuring) too weak, difficult to extend, not supported_
