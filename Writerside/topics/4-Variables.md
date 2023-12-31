# 4 - Variables

## Variable declarations
### Initialized vs Uninitialized variable declarations
- A variable can be declared as initialized or uninitialized.
- A variable cannot be read from until it is initialized (assignment).
- A variable must use snake_case (improves readability).

#### Initialized example:
- `let x = 123`
- `let (x, y) = (1, 2)`
- `let Point{ x, y } = p`

#### Uninitialized example:
- `let x: Str`
- Type hint is required: cannot be inferred later from first assignment.
- Inferring from first assignment is technically possible but against design philosophy.

## Single `let` statements
- The `let` keyword is used to declare a variable, which defaults to immutable.

#### Single variable example:
```s++
fun main() -> Void {
    let x = 123
    let y: Str
    y = "hello world"
}
```


## Tuple `let` statements (destructuring)
- Declaring multiple variables at once requires tuple destructuring.
- The number of variables being declared must match the number of elements in RHS tuple.
- The type of each variable being declared is inferred from the RHS tuple.
- The mutability of each variable being declared must be individually specified.

### Placeholders
- Discard certain values from the RHS tuple using placeholders.
- The placeholder token is the underscore `_`.
- Any number of placeholders can be used.

### Variadic placeholders
- Capturing or ignoring a subset of values from the RHS tuple can be done using the variadic operator `..`.
- The variadic operator `..` must be bound to a variable or placeholder.
- The variadic operator `..` can only be bound to a single variable or placeholder.

### Multiple variables example:
```s++
fun main() -> Void {
    let (mut x, y) = (1, 2)
    let (mut x, y, z) = some_3_tuple
    
    let (x, _, _) = some_3_tuple  # only the first value is captured
    let (_, y, _) = some_3_tuple  # only the middle value is captured
    let (_, _, z) = some_3_tuple  # only the last value is captured
    let (_, _, _) = some_3_tuple  # all values are discarded (no point in the `let` statement here)
    
    let (x, ..others) = some_5_tuple        # `others` is a tuple of the remaining values
    let (x, .._) = some_5_tuple             # the remaining values are discarded
    let (x, ..others, _, z) = some_5_tuple  # `others` is a tuple of the remaining values
    let (.._, z) = some_5_tuple             # the first 4 values are discarded
}
```


## Object `let` statements (destructuring)
- Declare variables as attributes of an object using object destructuring.
- The attributes of the object must be declared in the `let` statement.
- The type of each attribute is inferred from the attribute types in the object.
- The mutability of each attribute must be individually specified.

### Object destructuring example:
```s++
fun main() -> Void {
    let Point{ mut x, y } = p
    let Point{ mut x, y } = Point{ x=1, y=2 }
}
```


## Recursive `let` statement destructuring
- Allows for nested destructuring of or objects.

### Recursive destructuring example:
```s++
fun main() -> Void {
    let ((x, y), z) = ((1, 2), 3)
    let (Point{ x, y }, z) = (Point{ x: 1, y: 2 }, 3)
    let Point{ pos=(x, y), z } = Point{ pos=(1, 2), z=3 }
}
```


## Re-declaring a variable
- Any variable can be re-declared, with a different type if necessary.
- From ths re-declaration onwards, the variable will be of the new type.
- The variable's mutability can be changed with a re-declaration.

### Re-declaration example:
```s++
fun main() -> Void {
    let x = 1                  # x is an immutable number
    let mut x = "hello world"  # x is now a mutable string
    x = "goodbye world"        # x can be mutated
}
```

## Shadowing
- A variable with the same name as a variable in an outer block, can be declared in an inner block.
- Inside this block, the new variable will shadow the outer variable.
- Once this inner block has terminated, the outer variable will be visible again for the outer block.
- The outer block will not be able to see the inner variable.
- The inner block can access and mutate the outer variable (given its declared as `mut`).

### Shadowing example 1:
```s++
fun main() -> Void {
    let x = 1
    {
        let x = "hello world"
        std.print(x)  # prints "hello world"
    }
    std.print(x)  # prints "1"
}
```

### Shadowing example 2:
```s++
fun main() -> Void {
    let mut x = 1
    {
        x = 2
        std.print(x)  # prints "2"
    }
    std.print(x)  # prints "2"
}
```

## Mutability (of a value)
- The default mutability for all variables and parameters is immutable.
- The `mut` keyword must be used to make a variable or parameter mutable.
- The mutability of an object's fields matches the mutability of the object itself.
- These features are taken from Rust.
- To see the internal mutability of borrows, see [Borrowing]().

### Variable mutability example:
```s++
fun main() -> Void {
    let mut x = 1                            # x is mutable
    let (mut x, y) = (1, 2)                  # x is mutable, y is immutable
    let Point {mut x, y} = Point {x=1, y=2}  # x is mutable, y is immutable
}
```

### Parameter mutability example:
```s++
fun main() -> Void {
    fun f(mut x: Num) -> Void { }         # x is mutable
    fun f(mut x: Num, y: Num) -> Void { } # x is mutable, y is immutable
}
```

## Residual blocks
- A variable that will be a [residual type]() cam have a residual block on declaration.
- The residual block is a block of code that will be executed iff the default initialization results in the `Fail` 
  state.
- This allows for a variable to be declared with a default value, but with a backup plan if the default value fails.

### Residual block example:
```s++
fun main() -> Void {
    let x = {
        let t1 = some_function()
        let t2 = some_function()
        some_function_that_returns_a_residual(t1, t2)
    } else {
        let value_0 = func1()
        let value_1 = func2()
        value_0 + value_1
    }
}
```

## Variable lifetimes
- All variables are local variables, and are therefore only valid for the duration of the block they are declared in.
- Variables that are owned objects can be returned or set to class attributes, extending their lifetime.
- There are no global or static variables, as they are not safe, and are therefore not allowed.
