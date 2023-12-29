# 7 - Type System

## Overview
- Static & strong type-system is enforced, ensuring maximum safety.
- Heavy type-inference is used to reduce semantic noise in the coded.
- Affine types are used for compatibility with the strict memory model.
- Every type is an object, some are compiler-known such as numbers etc.
- Types are identified by using a PascalCase identifier, otherwise they are variables.

## Primitives
- There are no primitive types like C++'s `int`, `float` etc, or Rust's `i32`, `f64` etc.
- The equivalent types are classes and are compile-known, however, and have some special behaviour.
- The class names are `[U|I|F][8|16|32|64|128]`, and `Bool`.

## Common Types

### Booleans
- The `Bool` type is the only boolean type in S++.
- The keywords `true` and `false` are the 2 singleton instances of the `Bool` class (program long lifetime).
- The `Bool` class is a direct mapping to `LLVM`'s `bool` type.

#### Program long variables
- The `true` and `false` keywords are program-long variables that are immutable.
- The tye semantics work the same as a `Copy`-superimposed type.
- The backend uses the `LLVM` `bool` type to store the `Bool` class.

### Strings
- The `Str` type is the only string type in S++.
- It wraps a generic type that superimposed `std.backend.str.StrBackendAbtract` to provide the backend functionality.
- Variants of `std.backend.str.StrBackendAbtract` are `StrBackendRope` and `StrBackendVec`.
- The `Str` class calls multiple "core functions" of the backend class being abstracted over.

### Numbers
#### Number base literals
###### Base 10:
- Additive prefix: `[-|+]`
- Integer part: `[0-9]+`
- Decimal part: `(\.[0-9]+)?`
- Number type suffix: `_[u|f|i][8|16|32|64|128]`

###### Base 16:
- Literal prefix: `0x`
- Integer part: `[0-9a-fA-F]+`
- Number type suffix: `_[u|f|i][8|16|32|64|128]`

###### Base 2:
- Literal prefix: `0b`
- Integer part: `[0-1]+`
- Number type suffix: `_[u|f|i][8|16|32|64|128]`

#### Types of numbers
- There are different number types for different sizes, signedness, and precision, all compiler-known.
- The number types are `_[U|I|F][8|16|32|64|128]` (LLVM-mapped), and `BigNum` and `BigDec`.
- By default, `BigNum` and `BigDec` are used for numeric literals, but suffixes can be used to specify the type.
- The number types are superimposed over `std.ops.*` classes, providing the operations for the types.

| Type     | Size (bits) | Signed? | Range (min)                                  | Range (max)                                 | Precision (bits) |
|----------|-------------|---------|----------------------------------------------|---------------------------------------------|------------------|
| `U8`     | 8           | No      | 0                                            | 255                                         | 8                |
| `U16`    | 16          | No      | 0                                            | 65535                                       | 16               |
| `U32`    | 32          | No      | 0                                            | 4294967295                                  | 32               |
| `U64`    | 64          | No      | 0                                            | 18446744073709551615                        | 64               |
| `U128`   | 128         | No      | 0                                            | 340282366920938463463374607431768211455     | 128              |
| `I8`     | 8           | Yes     | -128                                         | 127                                         | 7                |
| `I16`    | 16          | Yes     | -32768                                       | 32767                                       | 15               |
| `I32`    | 32          | Yes     | -2147483648                                  | 2147483647                                  | 31               |
| `I64`    | 64          | Yes     | -9223372036854775808                         | 9223372036854775807                         | 63               |
| `I128`   | 128         | Yes     | -170141183460469231731687303715884105728     | 170141183460469231731687303715884105727     | 127              |
| `F8`     | 8           | Yes     | ?                                            | ?                                           | ?                |
| `F16`    | 16          | Yes     | ?                                            | ?                                           | ?                |
| `F32`    | 32          | Yes     | -3.40282347E+38                              | 3.40282347E+38                              | 24               |
| `F64`    | 64          | Yes     | -1.7976931348623157E+308                     | 1.7976931348623157E+308                     | 53               |
| `F128`   | 128         | Yes     | -1.18973149535723176508575932662800702E+4932 | 1.18973149535723176508575932662800702E+4932 | 113              |
| `BigNum` | No limit    | Yes     | -∞                                           | +∞                                          | No limit         |
| `BigDec` | No limit    | Yes     | -∞                                           | +∞                                          | No limit         |

#### Number type suffixes
- The suffixes are used to specify the type of the number.
- No suffix means the number is a `BigNum` or `BigDec` (checks decimal part).
- `u<number>` suffix means the number is a `U*` type, where `*` is the number.
- `i<number>` suffix means the number is a `I*` type, where `*` is the number.
- `f<number>` suffix means the number is a `F*` type, where `*` is the number.

### Arrays
- Arrays are the lowest level collection type in S++, like C++'s `T[]`.
- They array type is `std.Arr[T]`, where `T` is the type of the elements.
- They are fixed size and safely abstract over LLVM's array type.
- They are bounds checked for memory safety.

#### Creating arrays
###### Array literals

| Array type    | Syntax              |
|---------------|---------------------|
| >= 1 elements | `let x = [1, 2, 3]` |
| 0 elements    | `let x = [Num]`     |
- The syntax for empty arrays is because type hints are not allowed for [initialized let statements]().

###### Array constructor

| Array type    | Syntax                         |
|---------------|--------------------------------|
| >= 1 elements | `let x = std.Arr.new(1, 2, 3)` |
| 0 elements    | `let x = std.Arr[Num].new()`   |
- For `>= 1` elements, the type is inferred from the elements.

#### General API
```s++
cls Arr[T] {
    @private data: std.c.CArr[T]
    @private length: U128
}

sup [T] Arr[T] {
    use Item = T
    
    fun get(i: U128) -> Ret[Self.Item, BoundsError] { ... }
    fun set(i: U128, val: Self.Item) -> Ret[Void, BoundsError] { ... }
    fun del(i: U128) -> Ret[T, BoundsError] { ... }
    fun has(i: U128) -> Bool { ... }
    
    fun len() -> U128 { ... }
}
```
- Other classes are superimposed onto `Arr[T]`, adding more functionality.
