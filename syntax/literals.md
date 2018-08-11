# Tacit ideas / Syntax / Literals

Integers:
```
x = 932
```

Floating point numbers:
```
y = 0.3
z = 8.1e30
```

Booleans:
```
[true false] arr say
```

Strings:
```
'Hello' ' world' cat say
```

## Numeric precision

Integer precision:
```
8i 8int-32 eq
40b 40unsigned-int-8 eq
```

Float precision:
```
1.0f 1.0float-32 eq
1.0d 1.0float-64 eq
```

## String interpolation

Basic variables:
```
'Hello [name]' say
```

No escapes, only two-letter variables:
```
command = '[tb]gcc *.cpp[nl]'
```

Variables have longer synonyms:
```
long-command = '[tab]gcc *.cpp[new-line]'
```

Disable interpolation:
```
'bracket[[' say
```

Double quotes also supported:
```
"I'm a friend" say
```

Triple quotes also supported:
```
'''I'm a friend''' say
```

_No special Tex-like English typographic shortcuts (that are imported by default)_
