```
rect >> struct:
  top-left | vec2
  size | vec2
  area >> size.x size.x *  size.y size.y *  +
  scale >> self factor < self-type f32 | self-type >
    self.size &= factor *
    self

rect-ext >> struct-ext:
  | rect
>
  bottom-right >> self < self-type | vec2 >
    self.top-left self.size +
  move >> self distance < self-type vec2 | self-type >
    self.top-left &= distance +
    self

*<< @other.rect-ext
```
