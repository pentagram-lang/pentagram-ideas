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

When expressions are evaluated, values are pushed onto the current expression stack, and methods pop values from the stack in order to push new values:

| Remaining expression | Expression stack |
| -------------------- | ---------------- |
| `9 16 + sqrt`        | ` `              |
| `16 + + sqrt`        | `9`              |
| `+ + sqrt`           | `9 16`           |
| `sqrt`               | `25`             |
| ` `                  | `5`              |

## Statements and blocks

Every line of code in Tacit is a statement, and statements can either be an expression or a declaration. Variable assignment, module import, and method definition are all declarations. Here are some examples:

| Statement type | Declaration type | Code |
| --- | --- | --- |
| Expression | N/A | `'hi' say` |
| Declaration | Variable assignment | `x = 0` |
| Declaration | Module import | `+= pkg.math.core` |
| Declaration | Method definition | `times-two /= y =/ y 2 *` |

When statements follow each other at the same level of indentation (leading spaces), they form a block:
```
x = 1
y = x 2 +
y say
```

Any plain expression can be written with the help of a new block, so that means variable assignment and method definition (which both use expression) can use blocks:
```
f /= a =/
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
f /=
  x = 1
  x 2 *

g /=
  x = 3
  h /=
    x 4 *
  h h
```

## Method objects

Some methods need to use method objects as their parameters:
```
x = [1 2 3] arr
```

Inline method objects use the `[ ]` syntax, and the expression inside cannot start a new block.

Alternatively, method objects can be created using the `:` syntax, which does allow starting a new block.
```
y = arr:
  1
  2
  3
  4
```

This pattern is useful for building longer, multi-part expressions:
```
z = x y
  cat
  map: 10 +
  keep: a =/
    a 11 gt
  2 take
```

## String interpolation

Another special instruction that can't start new blocks is string interpolation:
```
name = "What's your name?" ask
'Hello, [name]' say
```

If the expression inside `[ ]` is so complex that it'd need a new block, you can create a new variable first.

## Popping and pushing an outer expression stack

When you call a method, it pops parameter values from the expression stack you give it, and pushes result values to that stack. The way this works is when the method is called, the whole expression for the method body is evaluated using the caller's expression stack.
```
-- Push
f /= 1 2 3

-- Pop and push
g /= x y =/ x y *

-- Implicit pop and push
h /= 4 +
```

Now, if an expression (including a method body expression) includes a block, not every statement in the block has access to the whole expression's stack. Intuitively, only the last expression statements of the block have access, after all the declaration statements are done. All of the statements before that have their own local expression stacks.
```
x = 1 2 3 -- Push 3
  y = 4
  y say
  z = 5 6 *
  + + y z + + -- Pop 3, push 1

f /= a =/ -- Pop 1
  b = 1 2 +
  a + b + -- Pop 1, push 1

write-3 /=
  'a.txt' write-file -- Pop 1
  'b.txt' write-file -- Pop 1
  'c.txt' write-file -- Pop 1

sequence /=
  a /= a 1 + -- Pop 1, push 1
  b /= b 2 * dup -- Pop 1, push 2

nested /= a =/ -- Pop 1
  b = a 1 +
  c /= -- Pop 1
    d = c 1 +
    e /= -- Pop 1
      f = e 1 +
      b d f + + -- Push 1

f /= a =/ -- Pop 1
  b = 1 2 +
  a + b + -- Pop 1, push 1

arr:
  i = 1
  i -- Push 1
  i 1 + -- Push 1
  i 2 + -- Push 1
  i 3 + dup -- Push 2
```

If a block doesn't need to push or pop, it doesn't need any expression statements:
```
a = obj:
  b = 1
  c = 2
```

To make sure an effect-only method object doesn't access the outer expression stack, use a sentinel value:
```
f /= g =/
  _ = 11 g.call nil
  12
```

## Not used

If the result of an expression is not used, assigning to `_` marks it as unused. Parameters cannot be named `_`, but can be assigned to `_` for this effect.

```
_ = cause-side-effect-and-return-result
```

## Not yet implemented

A special value that prevents compiler errors and can be filled in later:
```
some-method-to-write-later /=
  ?
```
