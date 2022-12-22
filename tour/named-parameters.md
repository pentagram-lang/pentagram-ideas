```
add >>
  x | i32
  y | i32 = ?
  z | i32 = 0
  |- i32
>
  x y +  z +

make-z-squared >>
  z | i32
  z |- i32
>
  z = z * z

main >>
  x = 1
  y = 2
  result = add,
    q = 3
    q 3 *
    = y
    z = 4
  [result] dbg
  [1 y= 2 3 z= add] dbg
  [1 y= 2 3 make-z-squared add] dbg
  params = [1 y= 2 3 make-z-squared] tup
  [params* add] dbg
```
