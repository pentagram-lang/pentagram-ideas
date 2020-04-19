# Tacit ideas / Syntax / Errors and crashes

Errors in Tacit aren't managed using block scopes. They're required to be managed at call-sites, which makes this special control flow visible and explicit.

And crashes in Tacit aren't managed at all. Crashes happen in response to non-local conditions (for example, system, assumptions, or API usage), so any attempt to recover or clean up locally without understanding the non-local causes will fail. A safer approach for recovery is non-local, to completely exit and restart the task, which the mechanism Tacit provides.

```
foo /= n =/
  cond.try:
    n 0 eq,
      -- Too small
      error
    n 100 gt,
      -- Too big
      crash
    else, n 1 +

-- propagate
bar =/
  m = 4 foo.try
  m say

-- handle
bar =/
  result = 4 foo.catch
  if,
    result is-ok?,
      m = result.ok-result
      m say
    else,
      result.error-message say

-- escalate
bar =/
  m = 4 foo.try-or-crash
  m say
```

## Assertions

Assertion failures at runtime cause crashes. They're program invariants and programmer assumptions. If they're proven false, there's no telling what whent wrong when, and no guarantee that unwinding the stack to an unsuspecting error handler might resolve the situation.

```
2 2 + 4 eq assert
```

Assertions will be attempted to be checked at compile-time (except for static assertions which must be verifiable at compile time and have no runtime impact, and which can be used for method overload resolution).

```
-- Make sure we're in metric
speed.unit m/s eq static-assert
```

When assertions fail, they produce usefully-formatted details. The details even have enough information to allow interactive browsing of equality failures.

Missing `else` cases for conditionals are a special type of static assertion that checks the `else` case would never be reached.

```
animal match:
  is-dog?,
    animal.bark
  is-cat?,
    animal.meow
```

## Not nil assertion

When dealing with optional values, you may find yourself often using `x is-nil? not assert` when you don't want to handle nils. The shortcut in Tacit for this is the `!` suffix operator.

```
person.company!.address!.city! say
```

## Messages and data

All three `error`, `crash`, and `assert` use an any (rich) comment from the preceding line as the message. And all three have an optional named parameter for including data.

```
# Can't open file
[path = path] obj +data error
```

## Higher order methods

It's okay to use `.try` on a method object even if in actual use, raising errors isn't part of that method object's signature.

```
map /= collection transform =/
  if,
    collection len 0 eq,
      [] arr
    else,
      [collection 0 get transform.try] arr
      collection 1 slice-from  transform  map
      cat
```

## Context

Unlike try and catch, context _is_ a block operator. This is because it doesn't have control flow side effects, and it's used for more than just errors and crashes.

```
bar =/
  "trying 4" context:
    4 foo.try
    "hi" say
```
