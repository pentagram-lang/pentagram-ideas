
```
_inner >> module:
  add >> x y > x y +

< allegro.bitmap
< my-pkg.main._inner
< my-pkg.other.a
< my-pkg.other.b
< std-fp
< std-math

<< my-pkg.other
<< std-math.sin
p << std-math.pi

*<< std-fp.applicative

main >>
  bitmap = 256 256 allegro.bitmap.create
  [other.a.x other.b.y +] dbg
  [1 2 my-pkg.main._inner] dbg
  [2 sin] dbg
  [p std-math.cos] dbg
  [1 val [+ 1] fmap] dbg
```
