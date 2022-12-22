```
-- Simple add
test:
  assert,
    1 1 +  2  eq

-- [sqrt]
tgroup:
  -- Four
  test:
    4 sqrt  2  eq assert

  -- Other numbers
  arr:
    ttup:
      input = 16
      output = 4
    ttup:
      input = 9
      output = 3
  tfor: input output >
    input sqrt  output  eq assert
```
