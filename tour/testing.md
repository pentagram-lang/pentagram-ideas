```
-- Simple add
test:
  assert,
    1 1 +  2  eq

-- [sqrt]
test-group:
  -- Four
  test:
    4 sqrt  2  eq assert

  -- Other numbers
  arr:
    tup:
      input = 16
      output = 4
    tup:
      input = 9
      output = 3
  test-each: data >
    data.input sqrt  data.output  eq assert
```
