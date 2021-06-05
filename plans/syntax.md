# Tacit ideas / Plans / Syntax

## Goals

* Very few special cases
* Easy to add metadata
* Easy to add control flow
* DRY

## Features

* No distinction between method object and method definition (one isn’t delayed, one is)
* No repetition in module definition or consumption
* Everything postfix
* No data structure definition or naming
* Tuples are immediate multiple stack values, lists are delayed (e.g. indexed foreach)
