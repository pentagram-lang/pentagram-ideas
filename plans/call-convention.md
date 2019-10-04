# Tacit ideas / Plans / Call convention

## Overview

- Caller saves all registers, callee can use any register
  - Special case: starting from leaf methods and working up where possible until registers all run out, last registers are used first, register use (including parameters/results) annotated on methods, caller doesn't need to save caller-unused registers
- All parameters to caller via first registers
- All results from caller via first registers
- No `push`/`pop`/`call`/`ret` instructions, manually move to and from the stack
- Adjust the stack pointer twice: once on method entry, once on exit
- Tail calls are adjusting the stack pointer and unconditionally jumping
- Call boundaries can be changed by the compiler (especially tail calls), so no guarantees for stack traces except error source
  - Use context info and/or a time-travel debugger for full picture
- Push the return address onto the stack before any potential parameters/results

## Future benchmarking/extensions

- Treat return address as a parameter, and only save it when needed
- Some caller-save, some callee-save registers
- Top-down graph colouring for global register allocation
- Separate call (return + register save) and data (stack-allocated) stacks for cache locality
- No stack-allocated data, just the heap

## WebAssembly 1.0

This target doesn't have tail calls or arbitrary jumps, all it has is basic structured control flow. Assuming it does have labeled breaks and/or a jump table, maybe Tacit will code gotos (including task resumption) as follows:

```
function wholeProgram() {
  var nextLabel = 1;
  label0: while (true) {
    label1: while (true) {
      label2: while (true) {
        label3: while (true) {
          switch (nextLabel) {
            case 0: break label0;
            case 1: break label1;
            case 2: break label2;
            case 3: break label3;
          }
        }
        // Code for block3, options:
        // - break to label2
        // - break to label1
        // - break to label0
        // - set nextLabel, continue to label0, break to label3
      }
      // Code for block2, options:
      // - break to label1
      // - break to label0
      // - set nextLabel, continue to label0, break to label3
      // - set nextLabel, continue to label0, break to label2
    }
    // Code for block1, options:
    // - break to label0
    // - set nextLabel, continue to label0, break to label3
    // - set nextLabel, continue to label0, break to label2
    // - set nextLabel, continue to label0, break to label1
  }
  // Code for block0, options:
  // - return
  return;
}
```

This way, if the jump table is inefficient, at least the forward path jumps should still be fast. Same goes for if setting nextLabel and continuing to the switch statement and evaluating a case isn't implemented as a single jump (via jump threading optimization), at least the forward path jumps should still be fast.

To have more forward path jumps than back path jumps, tail calls can be on the forward path (needs total method ordering).
