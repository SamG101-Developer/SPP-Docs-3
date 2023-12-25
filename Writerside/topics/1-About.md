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
