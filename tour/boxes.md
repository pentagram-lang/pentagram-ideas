```
delayed-sum >> struct:
  t-iterable | [| type |- type]
  |& t-iterable iterable
>
  values | i32 t-iterable* box
  sum >> values* 0 [+] fold
  increment >> values* [1 +] box delayed-sum

main >>
  d = [1 2 3] box delayed-sum
  [d.sum] dbg
  [d.increment.sum] dbg
```
