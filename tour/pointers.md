```
f >> x < i32 | i32 ptr >
  x 2 * ptr

g >> x < i32 mptr | nop >
  x* &= 2 *

main >>
  [4 f .*] dbg
  y = 3 f
  [y*] dbg
  z = 5 mptr
  [z*] dbg
  z g
  [z*] dbg
```
