# Pentagram ideas / Tour / Errors and crashes

## Errors

```
foo >> n >
  n 0 eq if/try:
    -- Too small
    error
  n 100 gt elif/try,
    -- Too big
    crash
  else:
    n 1 +

-- propagate
bar >>
  m = 4 foo/try
  m say

-- handle
bar >>
  result = 4 foo/catch
  if,
    result.is-ok?,
      m = result*
      m say
    else,
      result.error-message say

-- escalate
bar >>
  m = 4 foo/try-or-crash
  m say
```

## Assertions

```
-- Check math
2 2 + 4 eq assert

-- Make sure we're in metric
speed.unit m-s eq const assert

-- Automatic default case const assert
animal match
[.is-dog?] case,
  animal.bark
[.is-cat?] case,
  animal.meow
```

## Context data

```
  "entering file handling" data data= trace/try:
    path open/try
```

## Higher order methods

```
map >> collection transform >
  collection len 0 eq if:
    [] arr
  else/try:
    [collection 0 get transform*/try] arr
    collection 1 slice-from  transform  map/try
    cat
```
