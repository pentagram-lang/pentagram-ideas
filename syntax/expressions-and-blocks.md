# Tacit ideas / Syntax / Expressions and blocks

## Values and expressions

At the very core of Tacit codes is the value:
```
'hello'
```

Values can be combined, together with method calls, to make an expression:
```
1 1 +
```

When expressions are evaluated, values are pushed onto the current expression stack, and methods pop parameter values from the stack in order to push new values:

| Remaining expression | Expression stack |
| -------------------- | ---------------- |
| `9 16 + sqrt`        | ` `              |
| `16 + + sqrt`        | `9`              |
| `+ + sqrt`           | `9 16`           |
| `sqrt`               | `25`             |
| ` `                  | `5`              |

## Statements and blocks

Every line of code in Tacit is a statement, and statements can either be an expressions,variable assignment, module import, named parameter definition, and method definition:

| Statement type | Code |
| --- | --- |
| Expression | `'hi' say` |
| Variable assignment | `x = 0` |
| Module import | `<< pkg.tacit-lang.math` |
| Named paramter definition | `y < 1` |
| Method definition | `times-two >> z > z 2 *` |

When statements follow each other at the same level of indentation (leading spaces), they form a block:
```
x = 1
y = x 2 +
y say
```

Any expression can be written as a new indented block, so that means variable assignment and method definition (which both use expressions) can use blocks:
```
f >> a >
  a
  a
  *

b =
  c = 1
  d = 2
  c d *
```

The names defined inside of a block cannot be seen from outside of that block:
```
f >>
  x = 1
  x 2 *

g >>
  x = 3
  h >>
    x 4 *
  h h
```

## Method objects

Some methods need to use method objects as their parameters:
```
x = [1 2 3] arr
```

Inline method objects use the `[ ]` syntax, and the expression inside cannot start a new block.

## Method object operator

Method objects can also be created using the `:` method object operator, which does allow starting a new block.
```
y = arr:
  1
  x =
    2
    3
    +
  x
  x 4 *
```

This pattern is useful for building longer, multi-part expressions:
```
z =
  x y
  cat
  map: 10 +
  keep: a >
    a 11 gt
  2 take
```

## Prefix operator

Complex nested expressions are often easier to read with the help of the `,` prefix operator.
```
-- Original
"Can't You Sleep, Little Bear?" title<
1999 year<
"United States" "Massachusetts" "Cambridge"
origin origin<
book

-- With prefix operator
book,
  title < "Can't You Sleep, Little Bear?"
  year < 1994
  origin < origin,
    "United States"
    "Massachusetts"
    "Cambridge"
```

## String interpolation

Another special instruction that can't start new blocks is string interpolation:
```
name = "What's your name?" ask
'Hello, [name]' say
```

If the expression inside `[ ]` is so complex that it would need a new block, you can create a new variable first.

## Popping and pushing an outer expression stack

When you call a method, it pops parameter values from the expression stack you give it, and pushes result values to that stack. The way this works is when the method is called, every statement in the method body is evaluated using the caller's expression stack.
```
-- Push
f >> 1 2 3

-- Pop and push
g >> x y > x y *

-- Implicit pop and push
h >> 4 +

x =
  1 2 3 -- Push 3
  y = 4
  y say
  z = 5 6 *
  + + y z + + -- Pop 3, push 1

f >> a > -- Pop 1
  b = 1 2 +
  a + b + -- Pop 1, push 1

write-3 >>
  'a.txt' write-file -- Pop 1
  'b.txt' write-file -- Pop 1
  'c.txt' write-file -- Pop 1

sequence >>
  a > a 1 + -- Pop 1, push 1
  b > b 2 * dup -- Pop 1, push 2

nested >> a > -- Pop 1
  b = a 1 +
  c > -- Pop 1
    d = c 1 +
    e > -- Pop 1
      f = e 1 +
      b d f + + -- Push 1

f >> a > -- Pop 1
  b = 1 2 +
  a + b + -- Pop 1, push 1

arr:
  i = 1
  i -- Push 1
  i 1 + -- Push 1
  i 2 + -- Push 1
  i 3 + dup -- Push 2

vec2 >> tup:
  x y = -- Pop 2

f >> x > -- Pop 1
  y = x 2 * dup -- Push 1
  y 3 * -- Push 1
```

To give access to the full expression stack to a method object, call it in a normal expression:
```
f >> g >
  11 g*
  12
```

## Not yet implemented

A special value that prevents compiler errors and can be filled in later:
```
some-method-to-write-later /=
  ?
```
