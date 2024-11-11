# Pentagram ideas / Features / Errors and crashes

Errors in Pentagram aren't managed using block scopes. They're required to be managed at call-sites, which makes this special control flow visible and explicit.

And crashes in Pentagram aren't managed at all. Crashes happen in response to non-local conditions (for example, system, assumptions, or API usage), so any attempt to recover or clean up locally without understanding the non-local causes will fail. A safer approach for recovery is non-local, to completely exit and restart the task, which the mechanism Pentagram provides.

## Assertions

Assertion failures at runtime cause crashes. They're program invariants and programmer assumptions. If they're proven false, there's no telling what whent wrong when, and no guarantee that unwinding the stack to an unsuspecting error handler might resolve the situation.

Assertions will be attempted to be checked at run-time (except for when the value is a compile-time constant, which can be required using the constant compile method).

When assertions fail, they produce usefully-formatted details. The details even have enough information to allow interactive browsing of equality failures.

Missing default cases for conditionals are a special type of static assertion that checks the default case would never be reached.

## Context data

Errors, crashes, and assertions use an any (rich) comment from the preceding line as the message. And all three will also include messages and data provided by trace context from the stack.

Unlike try and catch, trace context _is_ a block operator. This is because it doesn't have control flow side effects, and it's used for more than just errors and crashes.

## Syntax

The call-site modifiers are method postfixes (binding tightly with methods before expression evalutation).

Methods that raise errors need be annotated as fallible. Higher order methods that are otherwise infallible do not annotate themselves or their method objects as fallible -- but if a fallible method object is provided as a parameter and evaluated within the method, the higher order method changes to also become fallible. Infallible methods can always be substituted for fallible methods (as method objects or trait implementations).

## Error handler marks

Performance is a key to useful and usable error modeling. Errors need to be fast to raise, fast to catch, and fast to ignore if they don't happen.

To accomplish this, Pentagram uses its built-in model of delimited continuations using continuation marks. The stack unwinds to the error handler and execution continues from that point. Errors don't chew up return registers or cycles spent checking return values.

## Error data

All errors contain a message (which may be a closure-evaluated format string and/or a internationalized string), an unevaluated stack trace, and data.

The shape of the data included with potential errors is part of method signatures. If multiple errors are raised, the type of the error data in the signature will be a combination (in some cases this would require the handler to dynamic dispatch or reflection to use).

## Task isolation

When a Pentagram task crashes, by default it triggers a crash of all its child tasks and of its parent task. The reason why such a large scope is assumed is that parent and child processes can be very tightly coupled, unable to function independently, and there may even be effectively shared mutable state.

The way to get resilience is to build your program as a distributed network of cooperating tasks. You can define your own isolation boundaries for task groups and restart rules (building a recursive tree of task groups), and use chaos engineering to verify that the system performs well regardless what bugs or systems conditions might occur. And if conditions are too bad, exit completely and let the OS or orchestration layer take over, instead of trying/failing to deal with things locally.

To properly separate Pentagram tasks, there can't be any shared mutable state via references or files. Mutable reference ownership can be passed between tasks, while immutable references are okay to share because the data pointed to can never change.

## Arithmetic

A constant source of security bugs is arithmetic overflow and underflow. By default all arithmetic in Pentagram is checked, with the checks optimized away if provable at compile time.

This includes integer addition/subtraction, and maybe floating point divide by zero or other "not a number" or infinity results.

## Latency budget

If a task doesn't finish fast enough, it will be preemptively terminated by the Pentagram runtime. This is the only way to guarantee protection against infinite loop bugs.

To implement this, a signal timer is set for each OS thread. When the timer expires, the signal handler checks if the currently-running Pentagram task has run out of time. If so, the task is terminated just as if it had been any other crash-causing signal (e.g. out of memory).

## Platform calls

When calling into platform code, Pentagram threads are protected by an OS process boundary. This way platform code can crash and restart cleanly.

## Context info

When an error or crash happens, it's often helpful to attach some contextual information at multiple levels of the call stack. This way when the error/crash is logged, it includes a full accounting of what happened to cause the error. This context is pushed to a context stack using continuation marks.

## Memory deallocation

When the stack unwinds, all dynamically-allocated memory "owned" by stack frames needs to be deallocated.

To this, every stack frame needs a well-known (findable) block of size plus pointer items. All of those get deallocated when the stack unwinds normally through returns or when an error happens. (This is core behaviour like storing return addresses on the stack, so it doesn't use continuation marks.)

## Stack traces

Stack traces are expensive to generate, so at error-sites, only the bare minimum information is saved about stack instruction addresses.

If an error is handled without the stack trace needed, then that full cost is avoided.

Also, separating error raising from stack trace generation has the benefit of making stack traces available in other contexts, such as "say debugging".

## Core dumps

When a Pentagram program crashes, if there's a core dump flag/folder continuation mark, a core dump will be generated. This can be used to recreate the entire task offline for diagnosis. Core deumps include the call stack, local variables, and heap data.

## Cactii

If errors or crashes involve multiple tasks with multiple call stacks, all of that information needs to be captured in stack traces and/or core dumps. This creates a "cactus" shape.
