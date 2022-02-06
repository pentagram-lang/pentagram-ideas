```
list >> enum:
  tvar (any-type)
>
  tail >> type:
    ()
  head >> type:
    value (tvar)
    next (tvar list)

number-list >> alias:
  tvar (number)
  (tvar list)

add >>
  tvar (number)
  x (tvar)
  y (tvar)
  (tvar)
>
  x y +

any >>
  tvar-collection (collection)
  tvar-item (any-type)
  items (tvar-item tvar-collection)
  check ([_ (tvar-item); (bool)])
  (bool)
>
  ?


```
