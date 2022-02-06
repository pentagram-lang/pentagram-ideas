```
add >>
  x (i32)
  y (i32)
  z (i32) = 0
  (i32)
>
  x y +  z +

make-z >>
  z (i32)
  (z < i32)
>
  z z<

main >>
  x = 1
  y = 2
  result = add,
    1
    = y
    z = 3
  result say
  1 2 3 z= add say
  1 2 3 make-z add say

main >>
  x = 1
  y = 2
  result = add,
    1
    < y
    z < 3
  result say
  1 2 3 z< add say
  1 2 3 make-z add say
```
