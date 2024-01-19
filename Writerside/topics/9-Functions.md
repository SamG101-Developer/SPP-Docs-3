# 9 - Functions

## Contents
- [Introduction](#introduction)
- [Function declaration](#function-declaration)
- [Function parameters](#function-parameters)
- [Function return type](#function-return-type)
- [Function types](#function-types)
- [Function operators](#function-operators)
- [Function overloading](#function-overloading)
- [Function recursion](#function-recursion)
- [Class `self` parameter](#class-self-parameter)
- [Closures](#closures)
- [Partial functions](#partial-functions)
- [Coroutines](#coroutines)
- [Asynchronous functions](#asynchronous-functions)

## Introduction
- Functions are first class objects that map to the `Fun[Ref|Mut|One]` types.

## Function declaration
- Functions are declared with the `fun` keyword.
- [AST reduction]() remodels them into classes with `Fun...` types superimposed on.

### Function declaration syntax
```s++
fun function(a: Str) -> Void { ... }
```

```s++
@public("specific/to/some/path")
@inline("always")
fun function_name[T, U](param1: T, param2: U) -> RetTy where [T: Copy] { ... }
```

### AST Reduction
#### Original code:
```s++
fun foo(a: Num) -> Num { ... }
fun foo(a: Str) -> Num { ... }
```

#### Transformed code:
```s++
cls __MOCK_foo { }
sup FunRef[Num, Num] on __MOCK_foo {
    fun call_ref(&self, a: Num) -> Num { ... }
}
sup FunRef[Num, Str] on __MOCK_foo {
    fun call_ref(&self, a: Str) -> Num { ... }
}

let foo = __MOCK_foo { }
```
- The AST reduction allows for overloads to be defined on functions with different argument types.
- It normalizes functions to be treated as objects / arguments to other functions.
- Access to the mock classes is semantically impossible due to the `__` prefix.
- The `let` statement is immutable to prevent mutation of the function.

## Function parameters
### Type annotations
- Parameters must be type-annotated, by design, to provide a clear interface.
- This is because there is no "order" as to which code would be inferred from to get the type.
- Parameters, like variables, are immutable by default -- prefix the parameter name with `mut` to make mutable.

### Parameter classifications
- Parameters can be classified as required, optional, or variadic.
- The order of the parameters must be required, optional, then variadic.

#### Required parameters
- Required parameters are parameters that must be specified when calling the function.
- An example of required parameters is the `a` and `b` parameters in `fun function(a: U16, b: Str) -> U16 { ... }`.

#### Optional parameters
- Optional parameters are required parameters with a default value.
- Their default value is used if the parameter is not specified when calling the function.
- To make an optional parameter, add an `=` after the parameter name, and then the default value.
- Giving the argument to an optional parameter requires a [named argument]().
- Optional parameters can be mutable, but cannot be borrows.
- Optional parameters still require type annotations, and the default value is type checked against this.
- Unlike Python, default values are evaluated at the call site, not the function definition site.
- Optional parameters must follow required parameters, and come before a variadic parameter.
- An example of an optional parameter is the `b` parameter in `fun function(a: U16, b: Str = "Hello") -> U16 { ... }`.

#### Variadic parameter
- A function can have one variadic parameter, which is prefixed with the `..` token.
- The type of the variadic parameter can be a type, or a variadic generic type.
- If the variadic parameter is a normal / non-variadic type, then each argument must be of that type.
- If the variadic parameter is a variadic generic type, then each argument can be different types:

1. Variadic parameter, non-variadic type parameter
    - This allows for the final parameter of a function to be variadic.
    - Every element in the variadic parameter must be of the same type, which can be generic.
    - `fun function_1[T](..x: T) -> Void { }`  # All the arguments must be the same type, T.
    - `fun function_2(..x: Str) -> Void { }`   # All the arguments must be Str.

2. Variadic parameter, variadic type parameter
    - This allows for the final parameter of a function to be variadic.
    - Every element in the variadic parameter can be of different types.
    - `fun function[..Ts](..x: Ts) -> Void { }`  # All the arguments can be different types.


### Parameter conventions
#### By-value
- By-value parameters are the default parameter convention -- ie no prefix.
- By-value parameters are passed by value, and are moved into the function.
- The moves are copies if `Copy` is superimposed on the argument type.

#### By-immutable-reference
- By-immutable-reference parameters are passed by immutable reference.
- By-immutable-reference parameters are prefixed with `&`.
- By-immutable-reference parameters cannot be optional.
- An example of a by-immutable-reference parameter is the `a` parameter in `fun function(&a: U16) -> U16 { ... }`.

#### By-mutable-reference
- By-mutable-reference parameters are passed by mutable reference.
- By-mutable-reference parameters are prefixed with `&mut`.
- By-mutable-reference parameters cannot be optional.
- An example of a by-mutable-reference parameter is the `a` parameter in `fun function(&mut a: U16) -> U16 { ... }`.

## Function return type
- The return type must always be specified for a function.
- Only 1 value can be returned from a function, but a tuple can wrap multiple values and be returned.

### Returning a value
- Use the `ret` keyword to return a value from a function.
- The `ret` keyword can be followed by an expression (no value for `Void`).
- An example of returning a value is `ret 1`.

### Returning a single value
- The return type is specified after the `->` token in a function prototype.
- The return type can be any type, including a generic type.
- The return type must always be specified by design: clear interface.
- An example of returning a single value is `fun function() -> U16 { ... }`.

### Returning multiple values
- Multiple values can be returned from a function by returning a tuple.
- The caller can then de-structure the tuple to get the values.
- Destructuring can be done with `let (x, y, z) = function()`.
- An example of returning multiple values is `fun function() -> (U16, Str, Str) { ... }`.

## Function types
- Functions are first class objects that map to the `Fun[Ref|Mut|One]` types.

| Function type         | Description                                     |
|-----------------------|-------------------------------------------------|
| `FunRef[Out, (..In)]` | A function that borrows its environment`        |
| `FunMut[Out, (..In])` | A function that mutably borrows its environment |
| `FunOne[Out, (..In])` | A function that owns its environment            |

### Class methods
- The function type is determined by the `self` parameter.
- See the [Self Parameter]() topic for more information.
- No `self` parameter means the function is a static method: `FunRef[Out, (..In)]`.

### Free functions in the module scope
- Free functions (not part of a class) will always be `FunRef[Out, (..In)]`.
- This is because the module scope is immutable and can not be moved.
- An example of a free function is `fun function() -> U16 { ... }`.

### Extending these types to closures
- Closure types are slightly more complicated due to environment capture.
- See the [Closures]() topic for more information.

## Function operators
- Logical operators can be applied to _some_ functions, depending on their return types.

#### The AND operator
- The `&&` operator can be applied to functions that return `Bool`.
- The new function only returns `true` if both functions return `true`.
- The new function returns `false` if either function returns `false`.
- The second function is only called if the first function returns `true`.

#### The OR operator
- The `||` operator can be applied to functions that return `Bool`.
- The new function returns `true` if either function returns `true`.
- The new function only returns `false` if both functions return `false`.
- The second function is only called if the first function returns `false`.

#### The NOT operator
- The `!` operator can be applied to functions that return `Bool`.
- The new function returns `true` if the original function returns `false`.
- The new function returns `false` if the original function returns `true`.


```s++
sup[R: Bool, ..Ts] Or on Fn[R, Ts] {
    fun or(self, that: Fn[R, Ts]) -> Fn[R, Ts] {
        ret fun (..args: Ts) -> R { ret self(..args) || that(..args) }
    }
} 

sup[R: Bool, ..Ts] And on Fn[R, Ts] {
    fun and(self, that: Fn[R, Ts]) -> Fn[R, Ts] {
        ret fun (..args: Ts) -> R { ret self(..args) && that(..args) }
    }
}

sup[R: Bool, ..Ts] Not on Fn[R, Ts] {
    fun not(self) -> Fn[R, Ts] {
        ret fun (..args: Ts) -> R { ret !self(..args) }
    }
}
```

## Function overloading
- Functions can be overloaded by having the same name, but different argument types.
- The compiler will generate a mock class for each overload (see [AST Reduction](#ast-reduction)).

### What can be overloaded
- Function parameter types can be overloaded.
- Function parameter count can be overloaded.

### What can't be overloaded
- Function return types can't be overloaded.
- Function parameter names can't be overloaded.
- Function parameter order can't be overloaded.
- Function parameter mutability can't be overloaded.
- Function parameter convention can't be overloaded.

## Function recursion
- Recursive functions are allowed in S++.
- All recursive functions are changed to be tail-call recursive.
- Tail call optimization is then applied to the function calls.

### Tail call optimization
- Tail call optimization is a compiler optimization that removes the stack frame of a function call.
- This is done by replacing the current stack frame with the stack frame of the function call.
- The stack size is not increased, and the stack frame is not pushed onto the stack.
- Recursion becomes time-limited, not space-limited.

### Halting problem semantic analysis
- There are a number of cases where the compiler known if the recursion will halt.
- No global or static variables + a strict ownership system => easier to determine if mutations will allow the base
  case to be reached.

## Class `self` parameter
- Class method that are applied to the instance require a `self` parameter.
- The convention of the `self` dictates how the environment of the class is captured.
- The `self` parameter is always the first parameter of a class method.
- A class method without a `self` parameter is a [static method]().

| `self` parameter | Function type  | Description                                                 |
|------------------|----------------|-------------------------------------------------------------|
| `&self`          | `FunRef[..Ts]` | A function that borrows its environment.                    |
| `&mut self`      | `FunMut[..Ts]` | A function that mutably borrows its environment.            |
| `self`           | `FunOne[..Ts]` | A function that takes ownership of its environment.         |
| `mut self`       | `FunOne[..Ts]` | A function that takes mutable ownership of its environment. |

#### The `&self` environment (by-immutable-reference)
- Borrows the instance to the class, immutably.
- Can not mutate any part of the class.
- Can not move any attributes from the instance of the class.

#### The `&mut self` environment (by-mutable-reference)
- Borrows the instance to the class, mutably.
- Can mutate any part of the class (not moving).
- Can not move any attributes from the instance of the class.

#### The `self` environment (by-value)
- Moves the instance of the class into the method.
- Can not mutate any part of the class.
- Can move any attributes from the instance of the class.

#### The `mut self` environment (by-value)
- Moves the instance of the class into the method.
- Can mutate any part of the class.
- Can move any attributes from the instance of the class.

## Closures
- Closures are anonymous functions that can capture their environment, and act as expressions.
- They very closely follow the syntax of functions, with 3 main differences:
    1. Closures have no name, as they are assigned to a variable.
    2. Closures can not have any annotations on.
    3. Closures can capture variables from the parent scope.

### Closure syntax
```s++
let x = fun (a: Str, b: Str) -> Str { a + b }
```

### Capturing variables
- Variables can be captured from the parent scope, as either normal or named captures.
- Normal captures capture the variable, and named captures allow an alias (useful for class variables).
- They can be captured with a convention, the same as a [parameter conventions]().
- Variable captures adhere to the same [argument memory rules]().

#### Capturing variables example
```s++
let x = 1
let y = 2
let z = fun (a: Num) with [&x, &y] -> Num { a + x + y }
```

### Closure types
- Functions are one of the 3 types: `FunRef[..Ts]`, `FunMut[..Ts]` and `FunOne[..Ts]`.
- Closures are the same, but due to memory rules for environment variables, they are slightly more complicated.
- The **function type** of a closure is determined by the mos restrictive convention of the captures.
- The **memory convention** of a closure is the least restrictive convention of the captures.
- For example, if a closure captures `[x, &y]`, then the closure is a `&FunOne`
    - The type is `FunOne`, because `x` is moved into the closure, so the closure can only be called once.
    - The closure is modelled as an immutable borrow, because the closure is not allowed extend the lifetime of the borrow.
    - This means the closure would have to be yielded if another function wanted to use it.


## Partial functions

## Coroutines
- Coroutines are functions that can be suspended and resumed as a later point in the program.
- Coroutines are declared in the same way as functions, with 0 syntactic differences.
- A function becomes a coroutine when 1+ `gen` statements are present in the function body, like Python.
- Coroutines must return the `std.Gen[Yield, Ret=Void, Send=Void]` type.
- **Note that coroutines are generators.**
- **Note that coroutines are not (necessarily) asynchronous functions.**

### Out
#### Yielding from a coroutine
- Values whose type matches the `Yield` type parameter can be yielded from a coroutine.
- Values are yielded with the syntax `gen "123"`.
- Yielded values can be borrows, because control is guaranteed to return to the coroutine before it is destroyed.
- **TODO: yielding borrows required the ret type to be Gen[Yield=Ref[Str]]?**

#### Returning from a coroutine
- A coroutine can have a `ret` statement, which returns a value from the coroutine.
- Returning a value stops the `.next()` method from returning any more values in the pass state.
- If the `Ret` type is `Void`, a `ret` statement is not required, but any code after the final `gen` will be executed.

### In
#### Sending to a coroutine
- Values whose type matches the `Send` type parameter can be sent to a coroutine.
- Values are sent to the coroutine with the syntax `coroutine_name.next("Hello")`.
- The values are received by the coroutine with `let x = gen 100`: `x` is `"Hello"`.
- Because `Send` defaults to `Void`, the `next` method does not often require an argument.

### Chaining coroutines
- Coroutines can be chained together using the `gen with` statement.
- This is most commonly seen in yielding out of 1 generator and into another.

## Asynchronous functions
- Traditional synchronous and asynchronous functions introduce a lot of issues, especially around code duplication 
  and "function colour" etc.
- Instead, in S++, the function **call** is either `async` or not, allowing for the function to be called in an 
  asynchronous context.
- If the function is called asynchronously, then a `std.Fut[Type]` object is returned, wrapping the return type of the 
  original function being called in the asynchronous context.
- The `std.Fut[Type]` type has an `await()` method, which blocks the current thread until the future is resolved.
- This was taken from Go, where the `go` keyword is used similarly to the `async` keyword in S++.
