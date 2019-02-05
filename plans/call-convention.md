# Tacit ideas / Plans / Call convention

## Overview

- Caller saves all registers, callee can use any register
  - Special case: starting from leaf methods and working up where possible until registers all run out, last registers are used first, register use (including parameters/results) annotated on methods, caller doesn't need to save caller-unused registers
- All parameters to caller via first registers
- All results from caller via first registers
- No `push`/`pop`/`call`/`ret` instructions, manually move to and from the stack
- Adjust the stack pointer twice: once on method entry, once on exit
- Tail calls are adjusting the stack pointer and unconditionally jumping
- Call boundaries can be changed by the compiler (especially tail calls), so no guarantees for stack traces except exception source
  - Use context info and/or a time-travel debugger for full picture
- Push the return address onto the stack before any potential parameters/results

## Future benchmarking/extensions

- Treat return address as a parameter, and only save it when needed
- Some caller-save, some callee-save registers
- Top-down graph colouring for global register allocation
- Separate call (return + register save) and data (stack-allocated) stacks for cache locality
- No stack-allocated data, just the heap
