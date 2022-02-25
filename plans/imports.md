# Tacit ideas / Plans / Imports

Although fully-qualified module usage may be a good default, the language supports importing individual identifiers into scope (optionally renaming) and importing all members into scope.

It is possible to "reach" inside a module to sub-modules, but it will be refactored by the auto-formatter into extra fully-qualified imports. It's even possible to use a package without `<` linking it first, but the formatter will add the links. Any `<<` or `*<<` imports count as usages and need to be listed via `<` links.

To link enum members, extension members, and trait members into scope, the `*<<` operator can be used.

The `<<` operator can be used to re-link any node in a package's tree.

For package creators, the use of index files, private modules, and re-exporting is encouraged, to create clean & usable package APIs.
