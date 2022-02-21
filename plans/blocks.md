# Tacit ideas / Plans / Blocks

Non-assignment statements use the outer block's stack. Pushing & popping is allowed. Popping using parameter syntax is allowed.

Inside a block, any trailing assignment statements also result in pushing named parameters onto the stack. Any other assignment statements are just normal (scoped) variable creation.

Blocks cannot be arbitrarily started. They can only be started following an assignment, block, parameter, method, or prefix operator. This simplifies auto-formatting, and improves ergonomics because incremental edits to the head of a multiline chain will already be in a nested block.
