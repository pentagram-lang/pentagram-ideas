```
delayed-sum >> type:
  values ([tvar [_ (type) (type)] (tvar iterable) > i32 tvar.call] box)
>
  sum >> values* 0 [+] fold
  increment >> values* [1 +] box delayed-sum

main >>
  d = [1 2 3] box delayed-sum
  d.sum say
  d.increment.sum say
```
