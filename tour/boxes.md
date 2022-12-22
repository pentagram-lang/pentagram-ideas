```
delayed-sum >> struct:
  values | box:
    t-iterable < [< type | type]
    |& t-iterable iterable
    | i32 t-iterable*
  .sum >> self > self.values* 0 [+] fold
  .increment >> self > self.values* [1 +] box delayed-sum

main >>
  d = [1 2 3] arr box delayed-sum
  [d.sum] dbg
  [d.increment.sum] dbg
```
