```
vec2 >> struct:
  x | f32
  y | f32

animal >> struct:
  name | str
  location | tup:
    continent | str
    position | vec2
  canis | case:
    bark | str
  felidae | case:
    meow | str
    lion | case:
      mane | str
    lynx | case:
      tufts | str

handle >> struct:
  fd | i32
  .ctor >>
    self < handle
    name < str
    | handle
  >
    self.fd = name open
    self
  .dtor >>
    self < handle
    | nop
  >
    self.fd close

main >>
  a = animal/canis,
    "dog"
    ["NA" 32 100 vec2] tup
    "woof"
  [a.is-canis? a.is-lion?] dbg
```
