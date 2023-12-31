# 11 - Modules

## Contents
- [Introduction](#introduction)
- [Namespacing](#namespacing)
- [Order of member definition](#order-of-member-definition)


## Introduction
- Every `.spp` file that is tagged with the `mod` keyword is included in the compilation tree.
- The module name must follow the directory structure
  - Starting with and including `src.`
  - Ending with the filename (without the extension)

## Namespacing
- Namespaces reflect the module name, which in turn reflect the directory structure.
- The filename, however, is **not included** in the namespace, as this causes too much clutter.
- Typically, `use std.Opt` would be used, so that `Opt[T]` can be used instead of `std.Opt[T]`.
- To use `Add` instead of `std.ops.Add`, `use std.ops.Add` can be used, which imports `Add` from the module `std.ops.
  add`

## Order of member definition
- The compiler performs a "pre-pass" to determine the definitions.
- This means that the order of members doesn't matter.
