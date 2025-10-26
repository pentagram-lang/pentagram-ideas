# Pentagram ideas / Tour / Errors and crashes

## Errors

```
foo >>
  n < i32
  |~ i32
>
  n 0 eq if?:
    -- Too small
    error
  n 100 gt elif,
    -- Too big
    crash
  else:
    n 1 +

-- propagate
bar >>
  |~ nop
>
  m = 4 foo?
  [m] dbg

-- handle
bar >>
  | nop
>
  result = 4 foo~
  result.is-ok if:
    m = result*
    [m] dbg
  else,
    [result.error-message] dbg

-- escalate
bar >>
  | nop
>
  m = 4 foo!
  [m] dbg
```

## Assertions

```
-- Check math
2 2 + 4 eq assert

-- Make sure we're in metric
speed.unit m-s eq const assert

-- Automatic default case const assert
animal match
[.is-dog] case,
  animal.bark
[.is-cat] case,
  animal.meow
```

## Context data

```
  "entering file handling" data data= trace?:
    path open?
```

## Higher order methods

```
sum >>
  get-numbers < [|? i32 arr]
  |? i32
>
  numbers = get-numbers*?
  numbers 0 [+] agg
```
