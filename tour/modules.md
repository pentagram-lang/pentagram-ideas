
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
  "[other.a.x other.b.y +]" say
  "[1 2 my-pkg.main._inner]" say
  "[2 sin]" say
  "[p std-math.cos]" say
  "[1 val [+ 1] fmap]" say
```
