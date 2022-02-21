```
(rect) >> type:
  top-left (vec2)
  size (vec2)
>
  = top-left
  = top-right
  area >> size.x size.x *  size.y size.y *  +
  scale >> self factor (f32) (rect) >
    self.size &= factor *
    self

(rect-ext) >> (rect) ext:
  bottom-right >> self (vec2) >
    self.top-left self.size +
  move >> self distance (vec2) (rect) >
    self.top-left &= distance +
    self

*<< @other.rect-ext

(named) >>
  tvar (type)
> trait:
  (named-ext) >> (tvar) ext:
    name >> (str)

(named-extra-ext) >>
  tvar (type)
  (tvar named)
> (tvar) ext:
  name-extra >> self >
    '[self.name] extra'
```
