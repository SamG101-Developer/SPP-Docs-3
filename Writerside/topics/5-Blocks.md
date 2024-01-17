# 5 - Blocks

## Blocks
- Blocks group statements and expressions together in a new scope.
- Blocks are always surrounded by `{` and `}` characters (non-optional, even for 1-line blocks).
- Blocks can be empty, but must still use the `{` and `}` characters.

### Where blocks are used (exhaustive list)
- A block will be referred to as the type `Block[T]` where `T` is the type of the item in the block.

| Where                      | Block type              |
|----------------------------|-------------------------|
| `case` expression patterns | `Block[Statement]`      |
| `loop` expression          | `Block[Statement]`      |
| `with` expression          | `Block[Statement]`      |
| `case-else` block          | `Block[Statement]`      |
| `loop-else` block          | `Block[Statement]`      |
| `cls` block                | `Block[ClassAttribute]` |
| `sup` block                | `Block[SupMember]`      |
| `fun` block                | `Block[Statement]`      |
| `let-else` block           | `Block[Statement]`      |
| inner scope                | `Block[Statement]`      |

## Conditional blocks
### General
- Conditional blocks are used to match a value against a number of patterns.
- They use the `case` keyword to declare the conditional block.
- They use the `else` keyword to declare the catch-all branch.
- A `case` block merges the functionality of a standard `if`, `switch/match/case` and `?:` into a single construct.
- As there are no `goto` statements, there is no different syntax for a `switch` statement, like in `C++`.

### Assignment from conditional blocks
- If the `case` expression is used for assignment, then the return type of each branch must be the same.
- The return type is the type of the final expression in the branch.

```s++
let x = case num
    == 1 { "one" }
    == 2 { "two" }
    == 3 { "three" }
else { "unknown" }

# x is of type Str
```

### Different ways to use the `case` statement:
#### Basics
```s++
case person
    == john { "hello john" }
    == jane { "hello jane" }
else { "hello stranger" }
```

#### Ternary
```s++
let x = case person == join { "hello john" } else { "hello stranger" }
```

#### Pattern matching
```s++
case person
    == Person { name="john", .. } { "hello john" }
    == Person { name="jane", .. } { "hello jane" }
else { "hello stranger" }
```
```s++
case tuple
    == (1, 2) { "tuple is (1, 2)" }
    == (3, 4) { "tuple is (3, 4)" }
else { "tuple is something else" }
```

#### Bindings
```s++
case person
    == Person { name="john", age, .. } { "hello john, you are " + age + " years old" }
    == Person { name="jane", age, .. } { "hello jane, you are " + age + " years old" }
else { "hello stranger" }
```
```s++
case tuple
    == (a, 1) { "tuple is ($a, 1)" }
    == (b, 2) { "tuple is ($b, 2)" }
else { "tuple is something else" }
```

#### Partial conditions
```s++
case person ==
    Person { name="john", .. } { "hello john" }
    Person { name="jane", .. } { "hello jane" }
else { "hello stranger" }
```

#### Guards
```s++
case person
    == Person { name="john", age, .. } && age > 18 { "hello john, you are " + age + " years old" }
    == Person { name="jane", age, .. } && age > 18 { "hello jane, you are " + age + " years old" }
else { "hello stranger" }
```

#### Multiple conditions
```s++
case person
    == john | jane { "hello john or jane" }
    == jack | jill { "hello jack or jill" }
else { "hello stranger" }
```

#### Unrelated conditions
```s++
case true {
    func1().attribute1 > 4 { "func1 returned true" }
    func2().attribute2 < 5 { "func2 returned true" }
else { "both returned false" }
```

## Loop blocks
### General
- The only loop block in S++ is the conditional `loop` expression.
- There is no `for` loop, as internal iteration is the only way to iterate over a collection: [Streaming API]().
- The `loop` expression is modelled & parsed as an expression but can never be used for assignment.
- The condition must evaluate to the `Bool` type.
- The `else` bock can be added for if the `loop` condition is `false` on the first iteration.

### Infinite looping detection
- If there are no condition-variables or no condition-variables are mutated in the loop's body, then the loop is 
  infinite, or never executed.
- Any non-pure function calls in the loop's condition are un-analyzable, so the loop is assumed to be finite.

### Control flow
- No `break` statement.
- No `continue` statement.
