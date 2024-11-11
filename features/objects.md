# Pentagram ideas / Features / Objects

Everything is an object, including instances of primitive types and modules. Everything can have methods, properties, setters, etc.

Basic types just define their fields (and default values). Advanced types can define a type body, with the same syntax as a module body.

There won't be static methods, including factory constructors. If only factory constructors are desired, make the default constructor private by marking all parameters private.
