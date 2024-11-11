# Pentagram ideas / Features / Named parameters

All trait implementations in Pentagram must have matching parameter names. With that, it's possible to call all methods using named parameters.

Unnamed parameters cannot follow named parameters in a method call, but otherwise, named parameters use the expression stack just like unnamed parameters.

You can call any method by naming its parameters, but you can also define methods parameters that must be specified as named when calling, i.e. "name-required" parameters.

After a named paramter is pushed onto the stack, it must be bound to a matching-name method parameter or assignment variable.

Using a named parameter defininition statement pushes the named parameter onto the stack, and does not create a local variable.

Only named parameters can have default values. If no named value is present on the statement stack, the default will be used.
