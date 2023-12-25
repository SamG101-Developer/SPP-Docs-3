# 1 - About

## Introduction
S++ is a statically & strongly typed, compiled, general purpose programming language. It is designed to be a modern,
safe, and fast language. It has a very strong type system, and type inference so powerful that it is syntactically
disallowed to specify types for variable declarations with an initializer. This is because the type system is capable
of inferring the type of any expression, and will do so at compile time. S++ takes inspiration from Rust, Python & C++,
and borrows elements of Go, Swift and Java too.

### Key features
**Strong Typing**: S++ is a strongly typed language, meaning that at no point will type casts ever occur, unless an
explicit function call is made. The type-system is so strong that even automatic upcasting isn't a feature. Normal type
casts are performed with overloads of the `From` class.

**Static Typing**: S++ is a statically typed language, meaning that all types are known at compile time. However,
because type inference is so strong, the only places type declarations are required are for function parameters, class
attributes, return types, uninitialized variable declarations, and default types for generic parameters.

**Safety**: S++ is designed to be a safe language, by using second class borrows, enforcing the law of exclusivity, and
tracking ownership through blocks. These 3 features combined allow for safe programming, without complex lifetime
analysis, and without the use of a garbage collector. This means that S++ is a language that is both safe and fast.

**Performance**: S++ is designed to be a high performance language, with a focus on runtime performance. This is
achieved by compiling to native code, using LLVM as a backend. This means that S++ is able to achieve performance
similar to C++/Rust.

**Readability**: S++ is designed to be a readable language, achieving this by using a simple syntax inspired from the C
family of languages. There are no quirks or special cases, enforcing consistency and readability.

### Paradigms & Philosophy

**OOP**: The OOP model is encouraged in S++, but with a unique class design that is similar to Rust. The state and
behaviour of classes are separated: state is stored in the `cls` block, with any number of behaviour implementations
defined in superimposition `sup` blocks. As well as method being definable in `sup` blocks, entire classes can be
superimposed over a class, using the `sup Copy on Str` syntax. This allows for behaviour similar to multiple
inheritance, but with the flexibility to remove entire classes from the superimposition.

**FP**: S++ has first class functions, that are one of `FunRef, FunMut, FunOne` types. Functions are able to be passed
around as arguments, and returned from other functions. Functions are also able to be stored in variables, and called
from those variables. This allows for a functional style of programming. Closures that capture variables are also
subject to certain memory-related rules in terms of how they can be passed around.

**Immutability**: S++ encourages immutability, with all variables being immutable by default. This is achieved by the
`let` keyword being used to declare variables, with the `mut` keyword additionally being used to declare a variable as
mutable. This is a feature taken from Rust directly.

## Why S++?

S++ aims to solve a number of problems other languages have, whether it be syntactic, semantic, or performance related.

**Generics**:
- Problem: Traditionally, using `<...>` for generic parameters overcomplicates the parser, and forces strange syntax to
  be used, such as the `::<>` turbofish syntax in Rust. This is because types can be lowercase like variables, and there
  is an ambiguity between generic parameters and comparisons. Whilst S++ doesn't have this ambiguity, because types are
  in PascalCase, it still uses `[]` for generic parameters, as the `[]` is more readable.
- Solution: Use `[]` for generic parameters and arguments. Because all types in S++ require PascalCase, there is no
  ambiguity between generic parameters and array indexing, if array indexing was to be added in the future.

**Consistent Naming**
- Problem: Lots of languages use a mix of cases and underscores for naming, such as `str` and `String` in Rust, and
  `camelCase` and `snake_case` in Python.
- Solution: In S++, all types are PascalCase, and all other identifiers (method, variables etc) must use `snake_case`,
  [which has increased readability](https://www.cs.kent.edu/~jmaletic/papers/ICPC2010-CamelCaseUnderScoreClouds.pdf) compared to `camelCase`. This is done by:

| Identifier Type | Case         | Regex               |
|-----------------|--------------|---------------------|
| UpperIdentifier | `PascalCase` | `[A-Z][a-zA-Z0-9]*` |
| LowerIdentifier | `snake_case` | `[a-z][a-z0-9_]*`   |

**Semicolon rules**
- Problem: Rust uses optional semicolons to determine if a value is being returned or if it is a statement. This means
  that omitting a semicolon can change the meaning of the code.
- Solution: S++ uses Python's approach of using newlines to terminate statements. Semicolons are syntactically not
  allowed: they're not even optional like JavaScript. This also reduces syntactic noise.
