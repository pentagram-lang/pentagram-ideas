# Tacit ideas / Syntax / Collections

Arrays:
```
my-array = [1 2 3] arr
my-other-array = arr:
  1 2 +
  2 8 *
  3 2 /
```

Options:
```
x = 7 opt
y = nil
z = [x y] arr flat
```

Maps:
```
words = ["ok", 8; "the", 400] smap
numbers = smap:
  1, obj:
    even? = false
  2, obj:
    even? = true
```

Lazy iterators (with splat):
```
fib /= a b =/
 next = a b +
 [next  b next fib ..] iter
```
