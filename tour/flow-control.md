```
-- Non-local return (escape continuation)
f >> return x >
  x 0 lte if:
    0 return*
  8 x /

-- Conditional (true or false required)
x 1 eq if:
  "a"
x y +  2  gt elif:
  "b"
else:
  "c"

-- Inline conditional (true or false required)
x 1 eq  "a"  "b"  iif

-- Value equality switch
x switch
1 case:
  "a"
2 case:
  "b"
else:
  "c"

-- Type match
y match
[.is-dog] case:
  "a [y.dog-name]"
[.is-cat] case:
  "b [y.cat-name]"
else:
  "c"

-- While loop
i = 0 mref
i* 10 lt while:
  ['at' i*] dbg
  i# &= 1 +

-- Infinite loop
loop: break >
  event = pull-event
  event process-event not if:
    break*

-- Item loop
sum = 0 mref
1 10 to each: item >
  sum# &= item +
```
