# 3 - Keywords
- S++ aims to use a small number of keywords, with re-use _generally_ limited to same-context usage (i.e `mut` can 
  be used in a couple different places but always refers to the mutability of the value or borrow).

### Module level declarations
#### `mod`
- Declare a `.spp` file as a [module]() for the module tree to be included in the compilation.
- The module name must reflect the directory/file structure up-to and including the `src` folder.

#### `cls`
- Declare a [class]() with the `cls ClassName { ... }` syntax.
- Only class state can be included in the `cls` definition.

#### `sup`
- Declare a [superimposition]() block for methods or another class over some existing class.
- Give subclass instances to the [object initializer]() of a class in `, sup=(...) }`

#### `fun`
- Declare a [function]() (global namespace) or method (inside a `sup` block).

#### `use`
- Declare a [type alias]() with the `use OldType as NewType` syntax.
- Declare a [namespace reduction]() type with the `use a.b.c.{d.e.F as G, H, I as J}`

### Variable declarations
#### `let`
- Declare a [variable]() (defaulting to immutable) with the `let x = 123` syntax.

#### `mut`
- Declare a variable as [mutable](), by combining with `let`, to use `let mut x = 123`.
- Declare a parameter as [mutable](), by placing `mut` before the identifier.
- Declare a borrow as [mutable](), by appending `mut` to the `&` symbol.

### Control flow
#### `case`
- Declare a conditional branch with the `case some_variable == true { ... }` syntax.
- See [conditional blocks]() for more.

#### `else`
- Declare the "catch-all" branch of an [if](#if) statement.
- Declare a [residual block]() for a [variable declaration]() of a [residual type]().
- Declare a default object for [object initialization]().

#### `loop`
- Declare a [conditional loop]() with the `loop condition { ... }` syntax.

#### `with`
- Declare a [context block] with the `with file = file.open(...) { ... }` syntax.


### Control flow exit
#### `ret`
- [Return]() a value out of a [function]() and to the caller context.

#### `gen`
- [Generate]() a value, with an optional convention, out of a [function]() and to the caller context.
- This is the same as "yielding" in most languages. The coroutine is resumed when the value is requested.
- Control is guaranteed to return to ths context, so yielding borrows it allowed.

### Type helpers
#### `where`
- Declare constraints for generic types with the `... where [X, Y: Copy & Default] { ... }` syntax.

#### `is`
- Check union types in pattern matching / general statements with the `let x = if some_value is { ... }`

#### `as`
- Used in type aliasing contexts, see [the `use` keyword](#use).
- **Not used for any type casting.**

### Types
#### `true`,`false`
- Represent program-long boolean constants.

#### `self` (value)
- Used to refer to the current _instance_ of a class, from inside a `cls` or `sup` block.

#### `Self` (type)
- Used to refer to the current _type_ of a class, from inside a `cls` or `sup` block.

### Misc
#### `on`
- Used with the `sup` keyword for superimposition of a class over another class.
- Uses the `sup Copy on Str` syntax to superimpose a class _on_ another class.

#### `async`
- Call a function asynchronously, like Go's `go` keyword
