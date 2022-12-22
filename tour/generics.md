```
list >> struct:
  t-item | type
>
  tail,
    nop
  head,
    value | t-item
    next | t-item list

i32-list >>
  i32 list

number-list >>
  t-item | type
  |? t-item number
>
  t-item list

add >>
  t-value | type
  x | t-value
  y | t-value
  |? t-value number
  |- t-value
>
  x y +

any >>
  t-collection | [| type |- type]
  t-item | type
  items | t-item t-collection*
  check | [| t-item |- bool]
  |? t-collection collection
  |- bool
>
  ?
```
