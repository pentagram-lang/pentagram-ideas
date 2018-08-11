# Tacit ideas / Syntax / Pointers

Create and copy an immutable pointer:
```
a = 100_000 primes ptr
b = a
```

Create and copy a mutable pointer:
```
a = 1280 720 pixmap mutable-ptr
b = a
```

Get a pointer's value:
```
cbar-1 /= list =/
  list.value.tail.value.head

cbar-2 /= list =/
  list*.tail*.head
```

Replace a pointer's value (creating a new pointer):
```
replace-cbar-1 /= replacement list =/
  list.value.tail.value.head = replacement
  list

replace-cbar-2 /= replacement list =/
  list*.tail.value* = replacement
  list
```

Write a mutable pointer's value:
```
write-cbar /= replacement list =/
  replacement  list*.tail*.set-head  list*.tail.write
```
