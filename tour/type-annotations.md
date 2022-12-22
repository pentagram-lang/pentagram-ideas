```
add >>
  x | i32
  y | i32
  |- i32
>
  x y +

sub >> x y | i32 i32 |- i32 >
  x y -

hypot >> b c | f32 f32 |- f32 >
  b-squared c-squared | f32 f32 =
    b b *  c c *
  b-squared c-squared + sqrt

main >>
  [1 1 add] dbg
  [1 1 sub] dbg
  [3 4 hypot] dbg
```
