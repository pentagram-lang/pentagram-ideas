# Pentagram ideas / Features / Blocks

Parameters can be popped off the stack at any point. It's not required to increase indent when doing this. This is how multiple assignment can be split onto multiple lines (push the results first, then pop each value off as separate parameters).

Parameters can only be pushed onto the stack from a non-assignment statement. For assignment statements, all parameters must bind to assignment identifiers.

Inside a block, any trailing assignment statements also result in pushing named parameters onto the stack. Any other assignment statements are just normal (scoped) variable creation.

Blocks cannot be arbitrarily started. They can only be started following an assignment, block, parameter, method, or prefix operator. This simplifies auto-formatting, and improves ergonomics because incremental edits to the head of a multiline chain will already be in a nested block.
