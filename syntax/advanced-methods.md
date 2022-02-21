# Tacit ideas / Syntax / Advanced methods

## Named parameters

All overloads in Tacit must have matching parameter names. With that, it's possible to call methods using named parameters:
```
+,
  initial = 1
  addition = 2 initial= 3 addition= +
```

Unnamed parameters cannot follow named parameters in a method call, but otherwise, named parameters use the expression stack just like unnamed parameters.

You can call any method by naming its parameters, but you can also define methods parameters that must be specified as named when calling. Any method parameters declared after the `<` marker are "name-required" parameters.
```
f >> x; y = ?; z = ? >
  x y + z +

1 2 y= 3 z= f say
```

After a named paramter is pushed onto the stack, it must be bound to a matching-name method parameter or assignment variable.
```
f >>
  1 x=  2 y=

f vec2
x y = f
```

Using a named parameter defininition statement pushes the named parameter onto the stack, and does not create a local variable.

## Optional parameters

Both named parameters can have default values. If no value is present on the statement stack, the default will be used.
```
f >> x; y = nil >
  x
  y 1 default
  +

8 f say
8 x=  9 val y=  f say

my-complex-func >> easy; hard = ?; difficult = 80 sqrt >
  arr:
    easy
    hard
    difficult
  sum

my-complex-func,
  4 2 +
  hard = 8
```

## Expression trees

Some methods evaluate their parameters lazily, like `and` and `or` (for short-circuiting).

Other methods walk through parameter evaluation step by step. For example, `assert` in debug mode saves the each evaluation node result of each parameter, which allows for both good error messages for `eq`, etc. checks, and for full interactive stepping through the expression trees.
