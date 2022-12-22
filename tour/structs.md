```
vec2 >> struct:
  x | f32
  y | f32

person >> struct:
  position | vec2
  name | str
  mortal,
    hat | str
  werewolf,
    fur | str

handle >> struct:
  fd | i32
  ctor >>
    name | str
  >
    fd = name open
  dtor >>
    self
  >
    self.fd close
```
