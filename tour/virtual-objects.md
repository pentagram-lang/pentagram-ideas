```
delayed-sum >> struct:
  values | virtual:
    t-iterable < [< type | type]
    |& t-iterable iterable
    | i32 t-iterable*
  .sum >> self >
    self.values* 0 [+] fold
  .increment >> self >
    self.values* &= [1 +] map
    self

main >>
  d = [1 2 3] arr virtual delayed-sum
  [d.sum] dbg
  [d.increment.sum] dbg
```
