# Tacit ideas / Syntax / Testing

Testing files are named `*.test.tacit` and all get automatically run.

Inside a test file, all identifiers from the "base" module are imported directly, including hidden identifiers. (The testing package also gets imported directly.)

Tests are all named using strings, and the `test` method is overloaded for nesting blocks and blocks that return booleans:
```
'all spaces collapsed' test:
  'one space collapsed' test: 1 1 eq
```

Because Tacit modules can't have side effects, it's easy to tell exactly which tests need to run when code changes, and Tacit will only run the minimal set of tests again during continuous testing mode.
