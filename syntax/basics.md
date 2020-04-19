# Tacit ideas / Syntax / Basics

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

Conditionals (true or false required):
```
fact /= n =/
  if:
    n 1 gt,
      n  n 1 - fact  *
    else,
      1
```
