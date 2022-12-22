
```
_inner >> module:
  add >> x y > x y +

< allegro
< app.other
< std-fp.applicative*
< std-math
< std-math.sin

p < std-math.pi

main >>
  bitmap = 256 256 allegro.bitmap.create
  [other.a.x other.b.y +] dbg
  [1 2 self-pkg.main._inner] dbg
  [2 sin] dbg
  [p std-math.cos] dbg
  [1 val [+ 1] fmap] dbg
```
