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

  arr:
    -- Even
    test-case:
      input = 16
      output = 4
    
    -- Odd
    test-case:
      input = 9
      output = 3

  -- Other numbers
  test-each: data >
    data.input sqrt  data.output  eq assert
```
