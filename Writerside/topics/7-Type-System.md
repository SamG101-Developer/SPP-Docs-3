# 7 - Type System

## Contents
- [Overview](#overview)
- [Primitives](#primitives)
- [Type inference](#type-inference)
- [Aliasing](#aliasing)
- [Namespace reduction](#namespace-reduction)
- [Casting](#casting)
- [Booleans](#booleans)
- [Strings](#strings)
- [Numbers](#numbers)
- [Arrays](#arrays)

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

## Type inference
- Type inference is extremely powerful: every rhs expression can be inferred.
- Type inference is used to reduce syntactic noise in the code.
- IDE's will have functionality to reveal the type on hovering over an identifier etc.

### Where type hints are required (exhaustive list)
- Function parameters: the exact signature of a functions must be known for overload selection.
- Function return type: the compiler must know what every function returns at compile time for RHS inference.
- Attribute types: for member access into a type, the compiler must know the type of every attribute.
- Uninitialized variables: uninitialized variables' types must be known for type-checking on assignment.

### Type annotations
- To provide a type annotation in any context where are identifier is declared, use `: [Type]`
- The exception is the function return type, which follows a `->` token
- For example, `fun function(a: Str, b: Str) -> Str { }`

| Statement            | Legal? | Reason                                                                 |
|----------------------|--------|------------------------------------------------------------------------|
| `let var: Str = "1"` | No     | The RHS is inferred as a `Num`, so the type annotation is unnecessary. |
| `let var: Str`       | Yes    | The RHS can not be inferred, so the type annotation is necessary.      |
| `let var = 1`        | Yes    | The RHS can be inferred, so the type annotation is not necessary.      |

### Inference rules
| RHS Expression Type               | How inference works                                               |
|-----------------------------------|-------------------------------------------------------------------|
| `<LiteralNumber>`                 | `BigNum \| BigDec`                                                |
| `<LiteralBoolean>`                | `Bool`                                                            |
| `<LiteralString>`                 | `Str`                                                             |
| `<LiteralArray>`                  | `Arr[Str]`                                                        |
| `<LiteralTuple>`                  | `Tup[Str]`                                                        |
| `<LiteralRegex>`                  | `Rgx`                                                             |
| `<Lambda>`                        | See [closure inference]()                                         |
| `<ParenthesizedExpressioj>`       | Infers the type of `some_expression` and uses that.               |
| `<IfExpression>`                  | Infers the final statement of each pattern block.                 |
| `<WhileExpression>`               | Cannot be used on the RHS of an expression.                       |
| `<YieldExpression>`               | Uses the `[Send]` generic parameter from `Gen[...]`.              |
| `<InnerScope>`                    | Infers the final statement of the inner scope.                    |
| `<WithExpression>`                | Infers the final statement of the block.                          |
| `<SelfKeyword>`                   | Infers the type of the current class (symbol table).              |
| `<VariadicOperator>`              | See [tuple variadic operator]().                                  |
| `<PostfixStructInitialization>`   | The type is the struct being initialized.                         |
| `<PostfixExpressionFunctionCall>` | Gets the correct overload and gets the return type.               |
| `<PostfixExpressionMemberAccess>` | Gets the type of the attribute (symbol table).                    |
| `<PostfixExpressionEarlyReturn>`  | Gets the return type of the function (symbol table).              |
| `<BinaryExpression>`              | Convert's to a regular function call, and infers the return type. |
- Recursive inference is used to algorithmically determine the type of the RHS.

## Aliasing
- Aliasing is when a type is given a new name, and can be used interchangeably with the original type.
- Aliasing is done using the `use` keyword, and is only allowed for types.
- Aliasing is useful for shortening long type names (and namespaces), and for creating type aliases for generic 
  parameters.

### Aliasing syntax
- The syntax for aliasing is `use OldName as NewName`.
- With generics, the syntax is `use [T] Map[T, Str] as NewName`.

## Namespace reduction
- Namespace reduction is similar to aliasing, but is used to reduce the length of a namespace, and doesn't require an 
  alias.
- Namespace reduction is done using the `use` keyword.

### Namespace reduction syntax
- The syntax for namespace reduction is `use a.b.c.{d.e.F as G, H, I as J}`.
- To use everything from a namespace, use `use a.b.c.*`.

## Casting
### Type casting
- Casting has no explicit keyword, because function calls can do the same thing.
- To normalize all casting, the `From[T]` class can be superimposed per type being cast _from_.
- The method `fun from (self, that: T) -> Self` is overridden for each `From` superimposition.
- The `In` type on the `From` type is the type being cast _from_. 

#### Type casting example
```s++
use std.ops.From

cls Foo {
    a: Num
    b: Num
}

sup From[Foo] on Str {
    fun from(self, that: Self.In) -> Self {
        ret "${that.a}, ${that.b}"
    }
}

fun main() -> Void {
    let foo = Foo { a=1, b=2 }
    let foo_str = Str.from(foo)
    std.assert(foo_str == "1, 2")
}
```

### Hierarchical casting
- There type system is so strong that there is no implicit upcasting.
- Requires explicit upcast and downcast methods on the `std.Inherit` class.

#### Hierarchical casting example
```s++
@inheritable
cls Foo {
    a: Num
    b: Num
}

sup Bar on Foo {
    c: Num
}

fun main() -> Void {
    let bar = Bar { c=3 }
    let foo = Foo { a=1, b=2, sup=(bar,) }
    let foo_bar = foo.upcast[Bar]()
    let bar_foo = foo_bar.downcast[Foo]().unwrap()
}
```

## Generics
- Generics can be specified on functions, classes, superimpositions & type-aliases.
- Generics allow for a type to be used with any type, and can be constrained.
- Generics that can be inferred can not be explicitly specified, as the compiler will infer the type.

### Generic parameter classifications
#### Required
- A generic parameter is required if a default type isn't provided.
- Inferrable required generic type parameters **must** follow other required generic parameters.

#### Optional
- A generic parameter is optional if a default type is provided.
- Optional type parameters can be omitted when instantiating the type.
- To give a value for the optional type parameter, a [named type argument]() must be used.
- Must follow required type parameters.

#### Variadic
- A generic parameter is variadic if it is prefixed with the `..` token.
- Variadic type parameters can be omitted when instantiating the type.
- A parameter with the variadic generic can be `n` number of parameters.
- Multiple variadic generic parameters can be specified, because **every** variadic generic parameter **must** be 
  inferrable (as a generic parameter or part of another type's constraints).

### Usages & inference

| Where used | Inferrable when...         |
|------------|----------------------------|
| `cls`      | Used in an attribute type. |
| `fun`      | Used in a parameter type.  |
| `sup`      | N/A                        |
- Generics are **always** inferrable if they appear in the constraint of another generic.
- Generics are **always** inferrable if they are optional (ie the default generic type argument is used).
- Inferrable generic parameters must be in the [required classification](#required) syntactically, ie not [optional](#optional).

### Generic constraints
- Short-hand or long-hand syntax can be used for generic constraints.

| Where used | Effect of constraint                                                           |
|------------|--------------------------------------------------------------------------------|
| `cls`      | Class can only be instantiated with satisfied constraints                      |
| `fun`      | Function can only be called with satisfied constraints (in overload selection) |
| `sup`      | The superimposition is only applied when the constraints are satisfied         |

## Booleans
- The `Bool` type is the only boolean type in S++.
- The keywords `true` and `false` are the 2 singleton instances of the `Bool` class (program long lifetime).
- The `Bool` class is a direct mapping to `LLVM`'s `bool` type.

#### Program long variables
- The `true` and `false` keywords are program-long variables that are immutable.
- The tye semantics work the same as a `Copy`-superimposed type.
- The backend uses the `LLVM` `bool` type to store the `Bool` class.

## Strings
- The `Str` type is the only string type in S++.
- It wraps a generic type that superimposed `std.backend.str.StrBackendAbtract` to provide the backend functionality.
- Variants of `std.backend.str.StrBackendAbtract` are `StrBackendRope` and `StrBackendVec`.
- The `Str` class calls multiple "core functions" of the backend class being abstracted over.

## Numbers
### Number base literals
#### Base 10:
- Additive prefix: `[-|+]`
- Integer part: `[0-9]+`
- Decimal part: `(\.[0-9]+)?`
- Number type suffix: `_[u|f|i][8|16|32|64|128]`

#### Base 16:
- Literal prefix: `0x`
- Integer part: `[0-9a-fA-F]+`
- Number type suffix: `_[u|f|i][8|16|32|64|128]`

#### Base 2:
- Literal prefix: `0b`
- Integer part: `[0-1]+`
- Number type suffix: `_[u|f|i][8|16|32|64|128]`

### Types of numbers
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

### Number type suffixes
- The suffixes are used to specify the type of the number.
- No suffix means the number is a `BigNum` or `BigDec` (checks decimal part).
- `u<number>` suffix means the number is a `U*` type, where `*` is the number.
- `i<number>` suffix means the number is a `I*` type, where `*` is the number.
- `f<number>` suffix means the number is a `F*` type, where `*` is the number.

## Arrays
- Arrays are the lowest level collection type in S++, like C++'s `T[]`.
- They array type is `std.Arr[T]`, where `T` is the type of the elements.
- They are fixed size and safely abstract over LLVM's array type.
- They are bounds checked for memory safety.

### Creating arrays
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

### General API
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
