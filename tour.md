# Pentagram Tour

## Fundamentals

### Hello world

```pentagram
-- This is the most basic program --
'Hello world!' say,
```

### Comments

```pentagram
-- All comments are delimited at start and end --
--
Comments can span
multiple lines
--
--- Add more hyphens -- if you want to use more inside ---
```

### Variable assignment

```pentagram
-- Create two variables and use them together --
foo = 40,
bar = 2,
baz = foo bar +,

-- Each binding creates a new scope (lexical replacement) --
x = 1,           -- x is 1
x = x 2 +,       -- new x is 3 (reads previous x)
x = x 3 *,       -- new x is 9 (reads previous x)

-- Variable mutation (syntactic sugar for lexical replacement) --
x = 1,
x _= 2 +,        -- equivalent to: x = x 2 +,

y = sv 1, 2 end-sv,
y _= 3 pushr,    -- equivalent to: y = y 3 pushr,

-- Rebinding record fields creates new records --
x = y: 1 z,
x.y = 2,                 -- sugar for: x = x 2 z.set-y,
x = x 3 z.set-y,         -- explicit setter function
```

## Primitive Values

### Numbers

```pentagram
-- All operators are postfix --
foo-1 = 1 2 +,
foo-2 = foo-1 3 -,
foo-3 = foo-2 4 *,
foo-4 = foo-3 5 /,

-- Negation is a special suffix postfix operator --
foo-1 = 1-,
foo-2 = foo-1-,
foo-3 = foo-2 2 + _-,
```

### Text

```pentagram
-- Common text operations are interpolation and concatenation --
foo = 'e',
bar = 'abc' 'd [foo]' cat,

-- No string escapes but sugar for special characters --
foo = 'a[nl]b[tb]c',

-- Add more quotes if you want more inside --
foo = ''a'b'',
bar = '''c''d''',

-- Or use alternative quotes --
foo = "a'b'",
bar = `a'"b'`,
```

### Booleans

```pentagram
-- Boolean combinators will short-circuit --
foo-1 = true f  false g  and,
foo-2 = true f  false g  or,
foo-3 = true f  false g  nand,
foo-4 = true f  false g  nor,
foo-5 = true f  false g  implies,
foo-6 = true f  false g  nimplies,

-- All conditional operators are spelled out --
foo-1 = true not,
foo-2 = 1 2 eq,
foo-3 = 3 4 ne,
foo-4 = 5 6 gt,
foo-5 = 7 8 gte,
foo-6 = 9 10 lt,
foo-7 = 11 12 lte,
```

## Literal Containers

### Grouped values

```pentagram
-- Store a group of values --
foo = gv 1, 2, 3 end-gv,
```

### Sequential values

```pentagram
-- Store a bunch of values in a row --
foo = sv 1, 2, 3 end-sv,
```

### Keyed values

```pentagram
-- Store an association between values --
foo = kv 1 'a', 2 3 end-kv,
```

### Literal type resolution

```pentagram
-- Literals resolve to default types when unconstrained --
foo-1 = 1,

foo-2 = 'x',

foo-3 = true,

foo-4 = sv 3, 4 end-sv,

foo-5 = kv 5 6, 7 8 end-kv,

-- TODO: configuring default types in lexical scope --
-- TODO: explicit type constraints via constructor trait --
```

## Container Wrappers

### Optional values

```pentagram
-- Store something that may or may not exist --
foo = nil,
bar = 1 val,
```

### References

```pentagram
-- Store an immutable reference to something --
foo = 1 ref,
```

### Mutable boxes

```pentagram
-- Store a value in a mutable box --
foo = 1 mut,
```

### Lazy values

```pentagram
-- Store a value that can be computed later --
foo = 1 laz,
```

## Field Access

### The value field

```pentagram
-- Field access desugars to get/set functions in type namespaces --
-- The * operator is sugar for .value --

x = 1 ref,
y = x*,                    -- reads: x ref.get-value,
x* = 2,                    -- rebinds: x = x 2 ref.set-value,

x = 1 laz,
y = x*,                    -- forces: x laz.get-value,
x* = 3,                    -- rebinds: x = x 3 laz.set-value,

-- Optional requires narrowing --
x = 1 val,
if x.is-val begin
  y = x*                   -- accesses: x opt.val.get-value,
end-if,

-- Mutable boxes are the exception --
x = 1 mut,
y = x*,                    -- reads: x mut.get-value,
x 2 write-mut,             -- actual mutation (no sugar)
```

## Control Flow

### Conditionals

```pentagram
-- Basic if-else structure --
if condition begin
  'true branch' say
elif other-condition begin
  'elif branch' say
else
  'false branch' say
end-if,

-- Tagged unions narrow through conditionals --
-- Synthetic .is-variant fields check the variant --
if pet.is-dog begin
  -- Inside this branch, pet is known to be a dog --
  pet.breed say
end-if,
```

### Loops

```pentagram
-- Infinite loop --
loop
  'forever' say
end-loop,

-- Conditional loop --
while condition begin
  'looping' say
end-while,

-- TODO: break, continue, return --
```

## Functions

### Basic functions

```pentagram
-- Function with type annotations --
def f fn
  par x i8,
  par y i8,
  i8
begin
  z = 1 ty i8 end-ty as,
  x y + z +
end-fn,
```

### Named arguments

```pentagram
-- Inline named arguments --
foo = name: 'dog' animal,

-- Multi-line named arguments using prefix with- syntax --
bar = with-animal
  name: 'cat'
end-animal,
```

### Type annotations

```pentagram
-- Types annotate values on the expression stack, not variables --
x = 42 ty i8 end-ty as,

-- Type fences can wrap any expression --
result = 1 ty i8 end-ty as 2 ty i8 end-ty as +,

-- Variables bind to typed values but have no types themselves --
y = x,
```

### Function signatures

```pentagram
-- Function parameters and return types shown in Basic functions section above --
```

### Closures

```pentagram
-- Inline lambdas capture variables from outer scope --
x = 10,
add-x = fn y begin y x + end-fn,
result = 5 add-x.call,  -- result is 15

-- Closures capture the lexical environment --
multiplier = 3,
numbers = sv 1, 2, 3 end-sv,
scaled = numbers fn n begin n multiplier * end-fn map,

-- Each closure captures what's in scope when it's created --
base = 5,
f1 = fn x begin x base + end-fn,
base = 10,
f2 = fn x begin x base + end-fn,
-- f1.call will add 5, f2.call will add 10

-- Multi-line closures for complex logic --
threshold = 100,
validator = fn value begin
  if value threshold gt begin
    'too large' err?
  else
    value
  end-if
end-fn,
```

## Custom Types

### Records

```pentagram
-- Define a record type --
def person rec
  par name txt,
  par age u8,
end-rec,

-- Fields become functions scoped in the type namespace --
p = person name: 'Alice' age: 30,
p-name = p person.name,

-- TODO: syntax for lexically extending the namespace with related functions --
```

### Tagged Unions

```pentagram
-- Define a tag for variants --
def animal tag,

-- Define variants under the tag --
def animal.dog rec
  par breed txt,
  par age u8,
end-rec,

def animal.cat rec
  par color txt,
  par indoor bool,
end-rec,
```

## Type System

### Specifications

```pentagram
-- Define a specification (interface/trait) --
def foo spec
  tpar t type,
end-spec,
def foo.f fn
  par self t,
  t
begin
  todo
end-fn,

-- Define a type --
def bar rec
  par x i32,
end-rec,

-- Implement the spec for the type --
def bar-foo impl bar foo end-impl,
def bar-foo.f fn
  par self bar,
  bar
begin
  self.x 1 + bar
end-fn,

-- TODO: spec default implementations --
-- TODO: existential implementations --
```

### Spec constraints

```pentagram
-- Specs can require other specs --
def eq spec
  tpar t type,
end-spec,
def eq.equals fn
  par self t,
  par other t,
  bool
begin
  todo
end-fn,

def ord spec
  tpar t type,
  t eq,              -- ord requires eq
end-spec,
def ord.compare fn
  par self t,
  par other t,
  ordering
begin
  todo
end-fn,

-- A type implementing both specs --
def point rec
  par x i32,
  par y i32,
end-rec,

-- Implement eq --
def point-eq impl point eq end-impl,
def point-eq.equals fn
  par self point,
  par other point,
  bool
begin
  self.x other.x eq self.y other.y eq and
end-fn,

-- Implement ord (which requires eq automatically) --
def point-ord impl point ord end-impl,
def point-ord.compare fn
  par self point,
  par other point,
  ordering
begin
  todo
end-fn,
```

### Impl constraints

```pentagram
-- Existential implementations: implement for all types meeting constraints --
def all-printable impl
  tpar t type,
  t to-string,           -- constraint: t must implement to-string
begin
  t printable            -- implement printable for all such t
end-impl,

def all-printable.print fn
  par self all-printable.t,    -- reference the type parameter
  unit
begin
  todo
end-fn,

-- Now any type with to-string automatically gets printable --
```

### Impl specificity overrides

```
-- TODO: no constraints, some constraints, all specific types --
```

### Generic types

```pentagram
-- Generic records use tpar for type parameters --
def box rec
  tpar t type,
  par value t,
end-rec,

-- Type parameters are named (allows omission and inference) --
x = 1 ty value: i8 box end-ty as,     -- explicit type parameter
y = 2 ty box end-ty as,               -- inferred type parameter
z = 3 box,                            -- fully inferred

-- Constructors can be called with explicit type parameters --
-- Type values must be generated by ty fence --
w = 4 value: ty i8 end-ty box,

-- Generic tagged unions --
def result tag
  tpar ok type,
  tpar err type,
end-tag,

def result.success rec
  par value result.ok,
end-rec,

def result.failure rec
  par error result.err,
end-rec,

-- Multiple type parameters can be partially specified --
r = data ok: ty txt end-ty result.success,            -- err inferred
r = data ty ok: txt result end-ty as result.success,  -- with type restriction
r = data result.success,                              -- both inferred

-- Generic functions with spec constraints --
def max fn
  tpar t type,
  par a t,
  par b t,
  t ord,                -- constraint: t must implement ord spec
  t
begin
  if a b gt begin a else b end-if
end-fn,
```

### Higher-kinded types

```pentagram
-- Type parameters can themselves be parameterized --
-- This enables abstractions over type constructors --

-- Define the functor spec --
def functor spec
  tpar f fn tpar t type, type end-fn,
end-spec,

def functor.map fn
  tpar a type,
  tpar b type,
  par self a functor.f.call,
  par transform fn par x a, b end-fn,
  b functor.f.call
begin
  todo
end-fn,

-- Implement functor for box --
def box-functor impl
  f: fn t begin t box end-fn box functor
end-impl,

def box-functor.map fn
  tpar a type,
  tpar b type,
  par self a box,
  par transform fn par x a, b end-fn,
  b box
begin
  self* transform.call box
end-fn,

-- Type-level functions are values that need .call to apply --
-- Use type-level lambdas to capture type constructors --

-- Implement functor for result (using existential impl) --
def result-functor impl
  tpar e type,
begin
  f: fn t begin t e result end-fn functor
end-impl,

def result-functor.map fn
  tpar a type,
  tpar b type,
  tpar e type,
  par self a e result,
  par transform fn par x a, b end-fn,
  b e result
begin
  if self.is-ok begin
    self.ok* transform.call e result.success
  else
    self e result.failure
  end-if
end-fn,

-- Partial application with type-level lambdas --
-- Close over types to create specialized type functions --
def pair rec
  tpar left type,
  tpar right type,
  par fst left,
  par snd right,
end-rec,

-- Create a type function that fixes the first parameter --
def pair-with-string impl
  f: fn t begin txt t pair end-fn functor
end-impl,
```

## Error Handling

### Error declaration

```pentagram
-- Functions that can error declare it in the prelude --
def f fn
  par x bool,
  err,
  bool
begin
  if x begin
    note: 'condition was true' err?
  else
    false
  end-if
end-fn,

-- Calls to erroring functions must be explicit --
-- 1 f,           -- Invalid: must handle or propagate
1 f?,             -- Propagate error up
r = f.try,        -- Capture error as a result value
```

### Error values and context

```pentagram
-- Errors are a single unit type with no variants --
-- All context comes from continuation marks --

-- Define custom marks (nominal, strongly typed) --
def file-path mark txt end-mark,
def retry-count mark u8 end-mark,

-- Marks scope to following statements in the block --
def open-file fn
  par path txt,
  err,
  file
begin
  > path file-path,                    -- mark applies to following statements
  > 0 retry-count,
  path open-internal                   -- if this errors, marks attach
end-fn,

-- The note mark is built-in for text context --
-- Use as named parameter on err --
note: 'file not found' err?,

-- Or set explicitly as a mark --
> 'opening file' note,
err?,

-- Read marks from captured errors --
result = 'data.txt' open-file.try,
if result.is-ok begin
  result.ok* use
else
  -- After narrowing, access marks with get-mark --
  note-text = result.err get-mark ty note opt end-ty as,
  -- The * suffix unwraps/extracts values --
  -- On error variant, * is a never function that propagates --
  result.err*?
end-if,

-- err and err* are never functions (safe in branches) --

-- Alternate pattern: inspect error then propagate --
result = database query.try,
if result.is-err begin
  -- Log error details without propagating --
  result.err log
end-if,
-- Propagate if error, continue if ok --
result*? process,
```

### Crashes

```pentagram
-- crash terminates the running task (not an error) --
if bad-state begin
  crash
end-if,

-- must crashes if condition is false --
x 0 gt must,

-- Marks are also read during crashes for diagnostics --
> 'validating input' note,
> user-id user-id,
input valid must,
```

### Error polymorphism

```pentagram
-- Higher-order functions can propagate error conditionally --
def filter fn
  tpar e type,               -- error-capability type parameter
  tpar t type,               -- element type parameter
  par d t seq,
  par f fn
    par x t,
    e has-err,               -- f has error-capability e
    bool
  end-fn,
  e has-err,                 -- filter has same error-capability as f
  t seq
begin
  -- body can call f? to propagate
end-fn,

-- If f doesn't error, filter doesn't error --
result = data non-error-fn filter,

-- If f errors, filter errors (must handle) --
result = data error-fn filter?,
```

## Syntax Overrides

### Custom delimiters

```pentagram
-- Use custom markers for nested or complex structures --
with-my-fn
  'custom syntax' say
end-my-fn,

-- Mark conditional blocks for clarity --
-- (Required by default lint when nesting duplicate keywords) --
if-'marker' condition begin
  'marked branch' say
end-if-'marker',
```

## Modules & Exports

```pentagram
-- Modules create namespaces and control visibility --
def utils mod
  -- Private by default --
  def helper fn
    par x i8,
    i8
  begin
    x 2 *
  end-fn,

  -- Public items marked with pub --
  pub def process fn
    par x i8,
    i8
  begin
    x helper
  end-fn
end-mod,

-- Import from modules --
use pkg.utils.process,
result = 5 process,

-- Nested modules for hierarchy --
def types mod
  pub def user rec
    pub par name txt,
    par password-hash txt      -- private field
  end-rec,

  def internal mod
    pub def user-id rec
      pub par value i64
    end-rec
  end-mod,

  -- Re-export to flatten hierarchy --
  repub pkg.types.internal.user-id
end-mod,

-- Import with rename --
use pkg.types.user as u,
use pkg.types.user-id as id,

-- Shortest path lint prefers repub --
use pkg.types.user-id,         -- canonical (re-exported)
-- use pkg.types.internal.user-id,  -- non-canonical (linter warns)

-- Siblings can access each other's public items --
def handlers mod
  def auth mod
    pub def login fn
      par username txt,
      par password txt,
      bool
    begin
      -- auth logic
      true
    end-fn
  end-mod,

  def admin mod
    use pkg.handlers.auth.login,

    pub def admin-login fn
      par username txt,
      par password txt,
      bool
    begin
      -- reuses sibling's login
      username password login
    end-fn
  end-mod
end-mod,

-- Parent modules gate what can be accessed from outside --
-- handlers.admin can see handlers.auth.login (sibling) --
-- Outside handlers/ can only see what handlers/mod re-exports --

-- Privacy can be bypassed with non-pub imports --
def utils mod
  def internal-helper fn
    par x i8,
    i8
  begin
    x 2 *
  end-fn,

  pub def process fn
    par x i8,
    i8
  begin
    x internal-helper
  end-fn
end-mod,

-- Non-pub bypasses export boundaries --
use pkg.utils.internal-helper non-pub,
use pkg.utils.process,

-- Works with renaming --
use pkg.utils.internal-helper non-pub as helper,

-- Common in test files to access private implementation --
-- Test files have lints disabled for non-pub imports --
def utils.test mod
  use pkg.utils.internal-helper non-pub,

  --
  Test internal helper directly
  --
  test
    result = 5 internal-helper,
    result 10 eq assert
  end-test
end-mod,
```

## Concurrency

```pentagram
-- TODO: structured concurrency primitives --
-- TODO: async operations (allowed in any function per manifesto) --
-- TODO: green threads --
```

## Testing

```pentagram
-- Basic tests assert conditions --
-- Check equality --
test
  x = 1 2 +,
  x 3 eq assert
end-test,

-- Tests can have parameters for test cases --
-- Parameterized test cases use rich comments --
--
Check addition works correctly

==
1 2 3 test-case,
5 7 12 test-case,
0 0 0 test-case,
==
--
test
  par a i8,
  par b i8,
  par expected i8
begin
  a b + expected eq assert
end-test,

-- Rich comments execute code between == delimiters --
-- Code in comments has access to file scope --
base = 100,

--
Check values above base

==
base 1 + test-case,
base 10 + test-case,
base 100 + test-case,
==
--
test
  par x i8
begin
  x base gt assert
end-test,

-- Doc helpers available in rich comments --
-- test-case, timeout, category, etc. are regular functions --
--
Integration test with timeout

==
5000 timeout,
'integration' category,
==
--
test
  result = slow-operation,
  result expected-value eq assert
end-test,

-- Tests are discovered automatically --
-- Organized in *.test.pg files adjacent to source --
-- Test runner shows hierarchy and results --
```
