```
x = 7 val
y = nil
z = x y cat

z = [x y] arr flat
my-array = [1 2 3] arr
my-other-array = arr:
  1 2 +
  2 8 *
  3 2 /
doubled = [1 2 3] arr [n > n 2 *] map
f >>
  a < i32 opt
  b < [i32 i32] tup
  c < [< i32 | i32]
  d < i32 arr
  | i32 arr
>
  arr:
    a*
    b*
    5
    c*
    d*
    6
    e if:
      7
    8 10 to each: i >
      i 2 *
    11 15 to .*

words = ["ok" 8; "the" 400] smap
numbers = smap:
  1 tup:
    even? = false
  2 tup:
    even? = true

more-numbers = smap:
  numbers*
  3 10 to each: i >
    i tup:
      even? = i 2 mod 0 eq

fib >> a b >
 next = a b +
 [next  b next fib .*] iter
```
