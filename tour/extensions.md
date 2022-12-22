```
rect >> struct:
  top-left | vec2
  size | vec2
  .area >>
    self < rect
    | f32
  >
    self.size.x self.size.x *
    self.size.y self.size.y *
    +
  .scale >>
    self < rect
    factor < f32
    | rect
  >
    self.size &= factor *
    self

.bottom-right >>
  self < rect
  | vec2
>
  self.top-left self.size +

.scale-half >>
  | [self < rect]* rect
>
  & 0.5 &.scale

movable >> trait:
  t-value < type
>
  .move >>
    self < t-value
    distance < vec2
    | t-value

rect-movalbe >> impl:
  | rect movable
>
  .move >>
    self < rect
    distance < vec2
    | rect
  >
    self.top-left &= distance +
    self
```
