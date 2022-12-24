# Pentagram ideas / Plans / Syntax

## Goals

- Very few special cases
- Easy to add metadata
- Easy to add control flow
- DRY

## Features

- No distinction between method object and method definition (one isn’t delayed, one is)
- No repetition in module definition or consumption
- Everything postfix

## Suffixes

There are a number of suffix operations expressions:
- negate
- plus
- dereference
- object access

All of these expressions use the value at the top of the stack after evaluating all preceding terms in the expression, regardless of whitespace.

The namespace access operator binds at a higher precedence and directly accesses the namespace before any evaluation. (It can also access operators, including the suffix operators.)

## Line editing

The rotate, prefix, and method object operators edit the flow of an expression on a line. Rotate takes the first value at the start of the line and inserts it in place of the operator. Prefix takes the following block of code and inserts it before the current "phrase" as part of the current expression. Method object takes the following block of code and inserts it before the current "phrase" as a method object.

THe "phrase" for prefix and method object operators is the sequence of identifiers and operators unbroken by whitespace.
