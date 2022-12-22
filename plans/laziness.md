# Pentagram ideas / Plans / Laziness

Some methods evaluate their parameters lazily, like "and" and "or" (for short-circuiting).

Other methods walk through parameter evaluation step by step. For example, assertions in debug mode saves the each evaluation node result of each parameter, which allows for good error messages and for full interactive stepping through of the expression trees.
