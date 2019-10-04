# Tacit ideas / Plans / Continuation marks

## Uses

There are a number of things in Tacit that need implicit tracking at the task stack level:

- error handlers
- cleanup handlers
- current working directory
- current environment variables
- current standard input/output streams
- logging/error context
- cancelability

Instead of making these mutable, requiring developers to clean up after themselves manually to avoid external side effects, having a continuation mark stack tied to the call stack keeps mark changes localized.

## Benefits

By using a language-level construct of continuation marks to manage composable stack state, it's now possible...

1. For all of these features to be built using Tacit itself, not a lower-level language that has special stack inspection rights
2. For all Tacit developers to introduce their own dynamic constructs built on continuation marks

## Implementation

Each mark gets its own dynamic-sized stack, with the size plus pointer stored in the task stack header. This allows each mark to get pushed/popped at different rates, and allows O(1) access to the latest value of each mark. (All marks must have a default initialization value.)

Every item in a continuation mark stack includes the new value and the call stack frame for when the item was added. Stack frame references are to the location in the stack where the return address was pushed.

When a continuation mark gets set, first it checks the call stack frame for the current item -- if it matches the current stack frame (i.e. during a tail call), then the current item is replaced. Otherwise, a new mark gets pushed to the stack and return address for the next call gets replaced by a special continuation mark unset routine.

When the call stack pops past where a continuation mark was previously set, via normal returns or via error stack unwinding, the memory used by the continuation mark must be deallocated. To accomplish this, instead of returning to normal code, the return happens to a special continuation mark unset routine, which does the deallocation, pops the mark stack, and then jumps to the return address specified by mark's stack frame.

### Alternative implementations

Continuation marks are actually the same kind of continuation data as return addresses. They could be passed to method invocations (continuation-passing style) and saved to the call stack just like return addresses. If a continuation mark was pushed and popped as often as a return address, identical handling would be logical, but this is often not the case.

## Tasks

When tasks are started, they inherit some of the continuation marks of the caller's context (e.g. current working directory), but not all (e.g. error handlers). This is determined mark-by-mark.
