## Simple types

```
(can-show) >>
  tvar (type)
> trait:
  show >>
    value (tvar)
    (str)

(can-try-convert) >>
  tvar-from (type)
  tvar-to (type)
> trait:
  try-convert >>
    value (tvar-from)
    (tvar-to opt)

(bool-can-show) >> (bool can-show) impl:
  show >>
    value (bool)
    (str)
  >
    value true eq if:
      "true"
    else:
      "false"

(opt-can-show) >>
  tvar (type)
  (tvar can-show)
> (tvar opt can-show) impl:
  show >>
    value (tvar opt)
    (str)
  >
    value is-val? if:
      value* show
    else:
      "nil"

(i32-bool-can-try-convert) >> (i32 bool can-try-convert) impl:
  try-convert >>
    value (i32)
    (bool opt)
  >
    value 0 eq if:
      false val
    value 1 eq if:
      true val
    else:
      nil
```

## Higher order types

```
applicative >>
  tvar [_ (type) (type)]
> trait:
  fmap >>
    tvar-value (type)
    tvar-result (type)
    function ([_ (tvar-value) (tvar-result)])
    value (tvar-value tvar*)
    (tvar tvar-result)

applicative >>
  tvar [_ (type) (type)]
  (tvar functor)
> trait:
  pure >>
    tvar-value (type)
    value (tvar)
    (tvar-result tvar*)

  apply >>
    tvar-value (type)
    tvar-result (type)
    function ([_ (tvar-value) (tvar-result)] tvar*)
    value (tvar-value tvar*)
    (tvar-result tvar*)
```
