# 8 - Tuples
- Tuples, like in Rust, are first class objects, and can contain elements of different types.
- Tuples map to the `std.Tup[..Types]` type.
- Tuple construction is done using `(...)` syntax.

## Tuple construction
| Number of elements | Tuple value | Tuple type  | Expanded type     |
|--------------------|-------------|-------------|-------------------|
| 0                  | `()`        | `()`        | `Tup[]`           |
| 1                  | `("1",)`    | `("1")`     | `Tup[Str]`        |
| n                  | `(1, 2, 3)` | `(1, 2, 3)` | `Tup[U8, U8, U8]` |


## Tuple folding
- Binary folding: fold a tuple of the same types into a single variable by recursively applying a binary operator.
- Function call folding: fold a tuple into the application of a function by repeatedly applying the function to the 
  next element of the tuple.

### Binary folding
- Binary folding allows a tuple to be folded into a single value by recursively applying a binary operator.
- The two input types and the output type must all be the same ie `fun add(x: U8, y: U8) -> U8`.
- The order of application depends on the syntax used:

#### Left binary fold:
```s++
fun main() -> Void {
    let x = (0, 1, 2, 3)
    let y = .. + x
    std.assert(y == 6)
}
```

#### Right binary fold:
```s++
fun main() -> Void {
    let x = (0, 1, 2, 3)
    let y = x + ..
    std.assert(y == 6)
}
```

### Function call folding
- Function call folding allows a tuple to be folded into the application of a function by repeatedly applying the 
  function to the next element of the tuple.
- The function must take a single argument of the same type as the elements of the tuple; the return type doesn't matter.
- Each element in the tuple must be of the same type.
- The result of the applications will be a tuple of the results of the function calls.

```s++
fun function(x: U8) -> U8 {
    ret x + 1
}

fun main() -> Void {
    let x = (0, 1, 2, 3)
    let y = function(x)..
    std.assert(y == (1, 2, 3, 4))
}
```

## Tuple unpacking
- Unpack a tuple into a function as multiple arguments.
- This only calls the function once, but passes the tuple as multiple arguments.
- If the target function has optional parameters, this won't work, because optional parameters require a keyword 
  argument to be passed.
- This is the same as C++'s `std::apply` function.

```s++
fun func1(x: (Num, Num, Num)) -> Num {
    ret x.0 + x.1 + x.2
}

fun func2(x: Num, y: Num, z: Num) -> Num {
    ret x + y + z
}

fun main() -> Void {
    let x = (1, 2, 3)
    
    let y = func1(x)    # pass the tuple as a single argument
    let z = func2(..x)  # unpack the tuple into multiple arguments
    
    std.assert(y == z)
}
```

## Tuple destructuring
- This occurs when [declaring multiple variables at once](4-Variables.md#tuple-let-statements-destructuring).
- Placeholders and variadic destructuring are supported.

```s++
fun main() -> Void {
    let x = (0, 1, 2, 3)
    
    let (a, b, c, d) = x  # de-structure the tuple into multiple variables
    let (a, _, c, d) = x  # use a placeholder to ignore a value
    let (a, ..b, d) = x   # use variadic destructuring to pack the rest of the tuple into a single variable
    let (a, .._, d) = x   # use variadic destructuring to ignore the rest of the tuple
}
```

## Tuple indexing
- Tuples have the unique feature of type and value indexing.
- This is done using the `.` operator, followed by a number.

### Type indexing example
```s++
fun main() -> Void {
    use TupleType = (Str, Str, Str)
    use TupleType1 = TupleType.0  # TupleType1 is Str
    use TupleType2 = TupleType.1  # TupleType2 is Str
    use TupleType3 = TupleType.2  # TupleType3 is Str
}
```

### Value indexing example
```s++
fun main() -> Void {
    let x = (0, 1)
    let y = x.0  # y is 0
    let z = x.1  # z is 1
}
```
