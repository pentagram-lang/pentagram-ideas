# Pentagram ideas / Features / Serialization

## Interchange formats

For easy interchange, it's important to support formats like JSON, MsgPack, XML, TOML, YAML, etc. A base in-memory object interchange format might be useful (like Serde).

## Simple format

This format will be used for default string interpolation, and conversion via `say`. It will also be used for input conversion via `ask`. These will be implemented via the language's base conversion traits to/from string.

Strings won't be quoted and collections won't have any markers (e.g. `nil` will convert to `''`). Input conversion will only be supported for basic types: stirngs, numbers, booleans.

## Pretty format

This format will be used for debugging, via `dbg`. The format will be compatible with language source code, including stirng quoting and type constructors. Whitepsace, newlines, another other syntax features will be activated by running the generated AST through the automatic formatter. Any unrepresentable values could be written as syntax-compatible unknown placeholder.

The reverse will also be supported: parsing a string into an AST or simple/safe values (i.e. consider Python pickle security issues).
