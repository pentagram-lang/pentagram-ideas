```
(list) >>
  tvar (type)
> enum:
  tail >> type:
    ()
  head >> type:
    value (tvar)
    next (tvar list)

(i32-list) >> (i32 list)

(number-list) >>
  tvar (type)
  (tvar number)
>
  (tvar list)

(tagged-i32) >>
  tvar-tag (str)
> type:
  value (i32)

add >>
  tvar (type)
  (tvar number)
  x (tvar)
  y (tvar)
  (tvar)
>
  x y +

any >>
  tvar-collection [_ (type) (type)]
  tvar-item (type)
  (tvar-collection collection)
  items (tvar-item tvar-collection*)
  check ([_ (tvar-item) (bool)])
  (bool)
>
  ?
```
