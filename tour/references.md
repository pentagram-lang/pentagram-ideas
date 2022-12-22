```
f >> x < i32 | i32 ref >
  x 2 * ref

g >> x < i32 mref | nop >
  x mut,
    x* 2 *

main >>
  [4 f .*] dbg
  y = 3 f
  [y*] dbg
  z = 5 mref
  [z*] dbg
  z g
  [z*] dbg
```
