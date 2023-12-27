# 5 - Blocks

## Blocks
- Blocks group statements and expressions together in a new scope.
- Blocks are always surrounded by `{` and `}` characters (non-optional, even for 1-line blocks).
- Blocks can be empty, but must still be surrounded by `{` and `}` characters.

### Where blocks are used (exhaustive list)
- A block will be referred to as the type `Block[T]` where `T` is the type of the item in the block.

| Where           | Block type              | Explanation                                                                    |
|-----------------|-------------------------|--------------------------------------------------------------------------------|
| `if` expression | `Block[Pattern]`        | The `if` expression requires blocks of pattern blocks to form its body.        |
| pattern blocks  | `Block[Statement]`      | The "pattern" expression requires a block of statements to form its body.      |
| `while`         | `Block[Statement]`      | The `while` expression requires a block of statements to form its body.        |
| `while-else`    | `Block[Statement]`      | The `while-else` expression requires a block of statements to form its body.   |
| `fun`           | `Block[Statement]`      | The `fun` definition requires a block of statements to form its body.          |
| `let-else`      | `Block[Statement]`      | The `let-else` expression requires a block of statements to form its body.     |
| inner scope     | `Block[Statement]`      | An inner scope is a block.                                                     |
| `cls`           | `Block[ClassAttribute]` | A class definition requires a block of class attributes to form its body.      |
| `sup`           | `Block[SupMember]`      | A superimposition block requires a block of methods/typedefs to form its body. |

### Returning from blocks (for assignment)
- If a block is being used for assignment (as en expression), then the final statement of the block is returned.
- For example, in the following code, `x` is assigned the value of `y + z`:
```s++
fun main() -> Void {
    let x = {
        let y = 1
        let z = 2
        y + z
    }
}
```


## Conditional blocks
- The only conditional block in S++ is the `if` expression.
- It combines the simplicity of an `if` expression with the power of pattern matching.
- It also provides simple syntax such that a ternary operator is not required.

### Assignment
- If the `if` expression is used for assignment, then the return type of each branch must be the same.
- An `else` branch must also be included for assignment, in case none of the other branches are taken.
- For example, in the following code, `str` is assigned the value of the branch that is taken:
```s++
fun main() -> Void {
    let num = 1
    let str = if num ==
        1 { "one" }
        2 { "two" }
        3 { "three" }
        else { "unknown" }
}
```

### Different ways to use the `if` statement:
#### Simple `==` comparison against a number of values
```s++
let num = 1
let str = if num ==
    1 { "one" }
    2 { "two" }
    3 { "three" }
    else { "unknown" }
```
- Allows each branch's value to be compared against the partial function `my_number ==`
- The result of the `==` method being used must result in a value of type `Bool`.
- The `else` branch is taken if none of the other branches are matched with the `==`.

#### Different comparison operators
```s++
let num = 1
let str = if num
    < 1 { "less than one" }
    > 1 { "greater than one" }
    else { "one" }
```
- Allows the condition `num` to be applied against each branch's partial function.
- The result of the comparison must result in a value of type `Bool`.
- The `else` branch is taken if none of the other branches are matched with the comparison.

#### Extending operators to methods / attributes
```s++
let num = 1
let str = if num
    .is_even() { "even" }
    .is_odd() { "odd" }
    else { "impossible" }
```
- As `num ==` is the same as `num.eq()`, the `if` expression can be used to call any method.
- The result of the method must result in a value of type `Bool`.
- The `else` branch is taken if none of the other branch's methods return `true`.

#### Destructuring
```s++
let num = (2, 4, 6)
let str = if num ==
    (1, 2, 3) { "one" }
    (1, _, _) { "one" }
    (..other, 2) { "two" }
    (x, .._) { "other" }
    else { "other" }
```
```s++
let vec = Vector3D {x=1, y=2, z=3}
let str = if vec ==
    Vector3D {x=1, y=1, z=1} { "unit" }  # Check variable values for equality
    Vector3D {x=1, ..} { "x" }           # Skip unwanted variable values
    Vector3D {y=1, y, z} { "y" }         # Save variable values to new variables
    else { "other" }
```
- The `if` expression can be used to de-structure variables, into tuples or their class.
- The result of the comparison must result in a value of type `Bool`.
- The `else` branch is taken if none of the other branches are matched with the comparison.

#### Nested destructuring
```s++
let velocity = Velocity {dir=Vector3D{x=1, y=0, z=0}, speed=1}
let str = if velocity ==
    Velocity {dir=Vector3D{x=1, y=0, z=0}, ..} { "forward" }
    Velocity {dir=Vector3D{x=0, y=1, z=0}, ..} { "up" }
    Velocity {dir=Vector3D{x=0, y=0, z=1}, ..} { "right" }
    Velocuty {.., speed=0} { "stationary" }
    Vecocity {dir, speed} { "non-unit direction at ${speed}m/s" }
    else { "unknown" }
```
- Nest destructuring for fields of a class.
- Tuples, object initialization, and variables can be mixed.
- The result of the comparison must result in a value of type `Bool`.
- The `else` branch is taken if none of the other branches are matched with the comparison.

#### Multiple conditions
```s++
let num = 1
let str = if num ==
    1 | 2 | 3 { "one" }
    4 | 5 | 6 { "two" }
    else { "other" }
```
- Multiple conditions can be used on a single branch, separated by `|`.
- The result of the comparison must result in a value of type `Bool`.
- The `else` branch is taken if none of the other branches are matched with the comparison.

#### Pattern guards
```s++
let num = 1
let str = if num ==
    1 && some_function() { "one" }
    2 && some_function() { "two" }
    else { "other" }
```
- Pattern guards can be used to further refine the conditions of a branch.
- The result of the guard must be a value of type `Bool`.
- The `&&` token is used to introduce a pattern guard.

#### Union type decomposition
```s++
use Pet = Cat | Dog | Fish
let pet = Cat.new().to_union[Pet]()

let str = if pet is
    Cat { "cat" }
    Dog { "dog" }
    Fish { "fish" }
    else { "impossible" }
```
- Because the `is` keyword is a comparison operator, it can be used in the `if` expression.
- A union type can be matched to its constituent types using the `is` operator.
- The `else` branch is not required, iff the pattern branch exhausts all types in the union.
- Flow typing is used to treat the `pet` object as the compared type inside the scope of the pattern.

#### Ternary mock
```s++
let num = 1
let str = if num == 1 { "one" } else { "other" }
```
- The `if` expression can be used as a mock ternary operator.
- The `else` branch is required for ternary expressions.


## Loop blocks
- The only loop block in S++ is the `while` expression.
- There is no `for` loop, as internal iteration is the only way to iterate over a collection: [Streaming API]().

### Structure
- The `while` loop is modelled and parsed as an expression, but can not be used for assignment.
- The `while` loop required a condition following the `while` keyword, which must evaluate to the `Bool` type.
- Multiple statements, inside a new block, make up the body for the while loop.
- An optional `else` branch can be included, which is executed if the condition is `false` on the first iteration of 
  the loop.

### Infinite loop detection
- If no condition-variables are mutated in the loop's body, then the loop is infinite, or never executed.
- Any non-pure function calls in the loop's condition are un-analyzable, so the loop is assumed to be finite.

### The `while-else` block
- The `else` branch is executed if the condition is `false` on the first iteration of the loop.
- Removes the requirement for an `[if-while]-else` block, as the `else` branch can be used instead.

#### `while-else` example:
```s++
let mut num = 0
if num < 10 {
    while num < 10 {
        num += 1
    }
} else {
    num = 0
}
```
becomes:
```s++
let mut num = 0
while num < 10 {
    num += 1
} else {
    num = 0
}
```

### Control flow
- No `break` statement
- No `continue` statement
