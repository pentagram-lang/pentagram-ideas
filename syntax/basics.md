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
plus >> x y >
  x y +
```

Method objects:
```
doubled = [1 2 3] arr [n > n 2 *] map
```

Point-free (tacit):
```
plus-one >> 1 +
```

Comments:
```
-- Helpful comment
```

Non-local return (escape continuation):
```
f >> return x >
  x 0 lte
  if:
    [0] return
  8 x /
```

Conditional (true or false required):
```
x 1 eq if:
  "a"
x y +  2  gt elif:
  "b"
else:
  "c"
```

While loop:
```
i = 0 mptr
i* 10 lt while:
  "at [i*]" say
  i* &= 1 +
```

Infinite loop:
```
loop: break >
  event = pull-event
  event process-event not if:
    [] break
```

Item loop:
```
sum = 0 mptr
1 10 range each: item >
  sum* &= item +
```
