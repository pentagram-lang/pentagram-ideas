# Tacit ideas / Syntax / Advanced methods

## Named parameters

All overloads in Tacit must have matching parameter names. With that, it's possible to call methods using named parameters:
```
+,
  initial < 1
  addition < 2 initial< 3 addition< +
```

Unnamed parameters cannot follow named parameters in a method call, but otherwise, named parameters use the expression stack just like unnamed parameters.

You can call any method by naming its parameters, but you can also define methods parameters that must be specified as named when calling. Any method parameters declared after the `<` marker are "name-required" parameters.
```
f >> x < y z >
  x y + z +

1 2 y< 3 z< f say
```

After a named paramter is pushed onto the stack, it must be bound to a matching-name method parameter or assignment variable.
```
f >>
  1 x<  2 y<

f vec2
x y = f
vec2, < x y
```

Using a named parameter statement pushes the named parameter onto the stack, and does not create a local variable.

## Optional parameters

Both unnamed and named parameters can be optional. If no value is present on the statement stack, `nil` will be used. If a value is specified, it is wrapped in `opt`.
```
f >> x [y] >
  x
  y 1 default
  +

8 f say
```

## Default parameters

Both unnamed and named parameters can have default values. If no value is present on the statement stack, the default will be used.
```
my-complex-func >> easy < hard [difficult = 80 sqrt] >
  arr:
    easy
    hard
    difficult
  sum

my-complex-func,
  4 2 +
  hard < 8
```

## Splats

You can define methods that accept an unnamed splat and/or a named splat. The splat will take all of (and only) the statement stack parameters.

```
my-complex-func /= one params.. < nparams.. > /=
  ?

my-complex-func,
  1
  my-arr ..
  2
  my-smap ..<
  3 x<
  my-obj ..<
```

(`say` prints all unnamed parameters from the expression stack using a splat.)

Sometimes you want to bridge collections and unnamed parameters or maps/objects and named parameters. You can use splats to do this:
```
1 2 3 splat-arr
1 x<  2 y<  3 z<  splat-smap
1 x<  2 y<  3 z< splat-obj
```

## Method guards

_Maybe not required_

## Expression trees

Some methods evaluate their parameters lazily, like `and` and `or` (for short-circuiting).

Other methods walk through parameter evaluation step by step. For example, `assert` in debug mode saves the each evaluation node result of each parameter, which allows for both good error messages for `eq`, etc. checks, and for full interactive stepping through the expression trees.
