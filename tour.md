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
foo-1 = f true  g false  and,
foo-2 = f true  g false  or,
foo-3 = f true  g false  nand,
foo-4 = f true  g false  nor,
foo-5 = f true  g false  implies,
foo-6 = f true  g false  nimplies,

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

## Control Flow

### Conditionals

```pentagram
-- Basic if-else structure --
if condition
  'true branch' say
elif other-condition
  'elif branch' say
else
  'false branch' say
end-if,

-- Tagged unions narrow through conditionals --
-- TODO: example of type narrowing --
```

### Loops

```pentagram
-- Infinite loop --
loop
  'forever' say
end-loop,

-- Conditional loop --
while condition
  'looping' say
end-while,

-- TODO: break, continue, return --
```

## Functions

### Basic functions

```pentagram
-- Define a function --
fn my-func arg1 arg2
  result = arg1 arg2 +
  result
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

### Function signatures

```pentagram
-- TODO: type annotations --
-- TODO: return value syntax --
-- TODO: closures --
```

## Custom Types

### Records

```pentagram
-- Define a record type --
rec Person
  name: Text
  age: Num
end-rec,

-- Fields become functions scoped in the type namespace --
person = Person name: 'Alice' age: 30,
person-name = person Person.name,

-- TODO: syntax for lexically extending the namespace with related functions --
```

### Tagged Unions

```pentagram
-- Define a tag for variants --
def animal tag,

-- Define variants under the tag --
def animal.dog rec
  breed: Text
  age: Num
end-rec,

def animal.cat rec
  color: Text
  indoor: Bool
end-rec,
```

## Type System

### Specifications

```pentagram
-- Define a specification (interface/trait) --
def foo spec,
def foo.f fn todo end-fn,

-- Define a type --
def bar rec x end-rec,

-- Implement the spec for the type --
def bar-foo impl bar foo,
def bar-foo.f fn x 1 + end-fn,

-- TODO: higher-kinded types --
-- TODO: spec conditions (bounds/constraints) --
-- TODO: spec default implementations --
-- TODO: existential implementations --
-- TODO: impl conditions --
```

### Generic types

```pentagram
-- TODO: generic type parameters --
-- TODO: generic function parameters --
```

### Type annotations

```pentagram
-- TODO: annotation syntax (same grammar as expressions per manifesto) --
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
if-'marker' condition
  'marked branch' say
end-if-'marker',
```

## Side Effects & Errors

```pentagram
-- TODO: how side effects are called/marked --
-- TODO: error propagation syntax --
-- TODO: error handling patterns --
```

## Modules & Exports

```pentagram
-- TODO: import/export syntax --
-- TODO: visibility (easy opt-in per manifesto) --
-- TODO: namespacing --
```

## Concurrency

```pentagram
-- TODO: structured concurrency primitives --
-- TODO: async operations (allowed in any function per manifesto) --
-- TODO: green threads --
```
