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
    factor < f32
    self < rect
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
  0.5 &.scale

movable >> trait:
  t-value < type
>
  .move >>
    distance < vec2
    self < t-value
    | t-value

rect-movalbe >> impl:
  | rect movable
>
  .move >>
    distance < vec2
    self < rect
    | rect
  >
    self.top-left &= distance +
    self

main >>
  r = rect,
    top-left = 0 0 vec2
    size = 4 4 vec2
  r &=
    5 &.scale
    &.move,
      vec2,
        8 8
  [r] dbg
```
