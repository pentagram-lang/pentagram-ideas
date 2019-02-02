# Tacit ideas / Plans / Exceptions

## Context info

When an exception happens, it's often helpful to attach some contextual information at multiple levels of the call stack. This way when the exception is logged, it includes a full accounting of what happened to cause the exception. This context is pushed to a context stack using continuation marks.

## Memory deallocation

When the stack unwinds, all dynamically-allocated memory "owned" by stack frames needs to be deallocated.

To this, every stack frame needs a well-known (findable) block of size plus pointer items. All of those get deallocated when the stack unwinds normally through returns or when an exception happens. (This is core behaviour like storing return addresses on the stack, so it doesn't use continuation marks.)
