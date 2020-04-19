# Tacit ideas / Syntax / Enums

Tacit comes with built-in enums like `true`, `false`, `opt`, and `nil`. You can also create your own.

If you need a constant, use `cenum` (pronounced "see-num"). If you need to store a value, use `venum` (pronounced "vee-num").


```
color = obj:
  blue += cenum
  gree += cenum

dog += venum
cat += venum
```

By using `+=`, you get a method for creating enum objects and an extra method for type checking (e.g. `is-dog?`).


## Composite `is` checks

Because all built-in Tacit types come with an `is` check, you can build your own `is` checks that you can use e.g. for method dispatch.

```
is-named-dog? /= obj =/
  obj is-dog?  obj.name is-opt?  and
```
