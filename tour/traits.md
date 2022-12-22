## Simple types

```
can-show >> trait:
  t-value | type
>
  show >>
    value | t-value
    |- str

can-try-convert >> trait:
  t-from | type
  t-to | type
>
  try-convert >>
    value | t-from
    |- t-to opt

bool-can-show >> impl:
  |- bool can-show
>
  show >>
    value | bool
    |- str
  >
    value true eq if:
      "true"
    else:
      "false"

opt-can-show >> impl:
  t-value | type
  |? t-value can-show
  |- t-value opt can-show
>
  show >>
    value | t-value opt
    |- str
  >
    value.is-val? if:
      value* show
    else:
      "nil"

i32-bool-can-try-convert >> impl:
  |- i32 bool can-try-convert
>
  try-convert >>
    value | i32
    |- bool opt
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
functor >> trait:
  t-container | [| type |- type]
>
  fmap >>
    t-value | type
    t-result | type
    function | [| t-value |- t-result]
    value | t-value t-container*
    |- t-container t-result

applicative >> trait:
  t-container | [| type |> type]
  |? t-container functor
>
  pure >>
    t-value | type
    value | t-value
    |- t-result t-container*

  apply >>
    t-value | type
    t-result | type
    function | [| t-value |- t-result] t-container*
    value | t-value t-container*
    |- t-result t-container*
```
