# 6 - Operators
- S++ has a large number of binary & postfix operators, so keep code concise and readable.
- There are no unary operators, as these [reduce readability]() of code.
- Operators can be overloaded by superimposing the corresponding classes.

## Operator Precedence

| Precedence (Lo -> Hi) | Type                         | Operators                                                                                                       |
|-----------------------|------------------------------|-----------------------------------------------------------------------------------------------------------------|
| 0                     | Assignment                   | `=`, `+=`, `-=`, `*=`, `/=`, `%=`, `%%=`, `**=` `\|\|=`, `\|=`, `&&=`, `&=`, `^=`, `<<=`, `>>=`, `<<<=`, `>>>=` |
| 1                     | Null coalescing              | `??`                                                                                                            |
| 2                     | Logical or                   | `\|\|`                                                                                                          |
| 3                     | Logical and                  | `&&`                                                                                                            |
| 4                     | Comparisons                  | `==`, `!=`, `<`, `<=`, `>`, `>=`, `<=>`, `is`                                                                   |
| 5                     | Bit shifting/rotating        | `<<`, `>>`, `<<<`, `>>>`                                                                                        |
| 6                     | Math-/bitwise-additive       | `+`, `-`, `\|`, `^`                                                                                             |
| 7                     | Math-/bitwise-multiplicative | `*`, `/`, `%`, `%%`, `**` `&`                                                                                   |

- Simplifies the precedence / less to remember
- The `&&` and `||` operators are short-circuiting.
- Fixes the precedence issue in C-based languages of `&` lower a higher precedence than comparison operators.
- Operators of the same precedence are always evaluated left-to-right, except `**`.

## Assignment
- There is single-assignment and multi-assignment.
- Assignment is a statement, not an expression.
- Assignment returns a `Void` value, so cannot be used on the RHS of another assignment.


### Single assignment
#### Single assignment example
```s++
fun main() -> Void {
    let mut x = 1
    x = 2
}
```

### Multi assignment
#### Multi assignment example
```s++
fun main() -> Void {
    let mut x = 1
    let mut y = 2
    x, y = (y, x)  # use tuple-destructuring to swap the values
}
```
- The RHS must only contain 1 value, so use tuples to combine multiple values into 1 value.
- The number of variables on the LHS must match the number of values on the RHS.

### Compound assignment
| Operator | Description                                                         |
|----------|---------------------------------------------------------------------|
| `=`      | Assign the right object to the left object                          |
| `+=`     | Add the right object to the left object                             |
| `-=`     | Subtract the right object from the left object                      |
| `*=`     | Multiply the left object by the right object                        |
| `/=`     | Divide the left object by the right object                          |
| `%=`     | Assign the remainder of the left object divided by the right object |
| `%%=`    | Assign the modulo of the left object divided by the right object    |
| `**=`    | Assign the left object to the power of the right object             |
| `\|\|=`  | Logical or the left object with the right object                    |
| `\|=`    | Bitwise or the left object with the right object                    |
| `&&=`    | Logical and the left object with the right object                   |
| `&=`     | Bitwise and the left object with the right object                   |
| `^=`     | Bitwise xor the left object with the right object                   |
| `<<=`    | Bitwise shift the left object by the right object                   |
| `>>=`    | Bitwise shift the left object by the right object                   |
| `<<<=`   | Bitwise rotate the left object by the right object                  |
| `>>>=`   | Bitwise rotate the left object by the right object                  |
- Compound assignment can only be used with [single assignment](#single-assignment).

### Compound assignment overload classes
- The generic parameters `Rhs` and `Out` default to `Self`, so are not required to be specified.
- The statement `x += 1` is equivalent to `x.add_assign(1)`.
- The standard `=` operator **can not** be overloaded.

#### Mathematical operators
```s++
sup std.ops.AddAssign[Rhs=Self, Out=Self] on Num {
    fun add_assign(self, that: Rhs) -> Out { ... }
}
```
```s++
sup std.ops.SubAssign[Rhs=Self, Out=Self] on Num {
    fun sub_assign(self, that: Rhs) -> Out { ... }
}
```
```s++
sup std.ops.MulAssign[Rhs=Self, Out=Self] on Num {
    fun mul_assign(self, that: Rhs) -> Out { ... }
}
```
```s++
sup std.ops.DivAssign[Rhs=Self, Out=Self] on Num {
    fun div_assign(self, that: Rhs) -> Out { ... }
}
```
```s++
sup std.ops.RemAssign[Rhs=Self, Out=Self] on Num {
    fun rem_assign(self, that: Rhs) -> Out { ... }
}
```
```s++
sup std.ops.ModAssign[Rhs=Self, Out=Self] on Num {
    fun mod_assign(self, that: Rhs) -> Out { ... }
}
```
```s++
sup std.ops.PowAssign[Rhs=Self, Out=Self] on Num {
    fun pow_assign(self, that: Rhs) -> Out { ... }
}
```

#### Logical operators
```s++
sup std.ops.OrAssign[Rhs=Self, Out=Self] on Num {
    fun or_assign(self, that: Rhs) -> Out { ... }
}
```
```s++
sup std.ops.AndAssign[Rhs=Self, Out=Self] on Num {
    fun and_assign(self, that: Rhs) -> Out { ... }
}
```

#### Bitwise operators
```s++
sup std.ops.BitOrAssign[Rhs=Self, Out=Self] on Num {
    fun bit_or_assign(self, that: Rhs) -> Out { ... }
}
```
```s++
sup std.ops.BitAndAssign[Rhs=Self, Out=Self] on Num {
    fun bit_and_assign(self, that: Rhs) -> Out { ... }
}
```
```s++
sup std.ops.BitXorAssign[Rhs=Self, Out=Self] on Num {
    fun bit_xor_assign(self, that: Rhs) -> Out { ... }
}
```
```s++
sup std.ops.ShlAssign[Rhs=Self, Out=Self] on Num {
    fun shl_assign(self, that: Rhs) -> Out { ... }
}
```
```s++
sup std.ops.ShrAssign[Rhs=Self, Out=Self] on Num {
    fun shr_assign(self, that: Rhs) -> Out { ... }
}
```
```s++
sup std.ops.RolAssign[Rhs=Self, Out=Self] on Num {
    fun rol_assign(self, that: Rhs) -> Out { ... }
}
```
```s++
sup std.ops.RorAssign[Rhs=Self, Out=Self] on Num {
    fun ror_assign(self, that: Rhs) -> Out { ... }
}
```

## Binary operators
- Binary operators require 2 operands, 1 on the left and 1 on the right of the operator.

| Mathematical Operator | Name           | Description                      | Class         |
|-----------------------|----------------|----------------------------------|---------------|
| `+`                   | Addition       | Add two objects together         | `std.ops.Add` |
| `-`                   | Subtraction    | Subtract two objects             | `std.ops.Sub` |
| `*`                   | Multiplication | Multiply two objects             | `std.ops.Mul` |
| `/`                   | Division       | Divide two objects               | `std.ops.Div` |
| `%`                   | Remainder      | Get the remainder of two objects | `std.ops.Rem` |
| `%%`                  | Modulo         | Get the modulo of two objects    | `std.ops.Mod` |
| `**`                  | Exponentiation | Raise an object to the power of  | `std.ops.Pow` |

| Null coalescing Operator | Name            | Description                                                                    | Class |
|--------------------------|-----------------|--------------------------------------------------------------------------------|-------|
| `??`                     | Null Coalescing | If the left object is in teh residual fail state, then return the right object | `N/A` |

| Logical Operator | Name | Description | Class         |
|------------------|------|-------------|---------------|
| `&&`             | And  | Logical and | `std.ops.And` |
| `\|\|`           | Or   | Logical or  | `std.ops.Or`  |

| Bitwise Operator | Name           | Description          | Class            |
|------------------|----------------|----------------------|------------------|
| `&`              | Bitwise And    | Bitwise and          | `std.ops.BitAnd` |
| `\|`             | Bitwise Or     | Bitwise or           | `std.ops.BitOr`  |
| `^`              | Bitwise Xor    | Bitwise xor          | `std.ops.BitXor` |
| `<<`             | Bitwise Shift  | Bitwise shift left   | `std.ops.BitShl` |
| `>>`             | Bitwise Shift  | Bitwise shift right  | `std.ops.BitShr` |
| `<<<`            | Bitwise Rotate | Bitwise rotate left  | `std.ops.BitRol` |
| `>>>`            | Bitwise Rotate | Bitwise rotate right | `std.ops.BitRor` |

| Comparison Operator | Name                  | Description                                                           | Class         |
|---------------------|-----------------------|-----------------------------------------------------------------------|---------------|
| `==`                | Equals                | Check if two objects are equal                                        | `std.ops.Eq`  |
| `!=`                | Not Equals            | Check if two objects are not equal                                    | `std.ops.Ne`  |
| `<`                 | Less Than             | Check if the left object is less than the right object                | `std.ops.Lt`  |
| `<=`                | Less Than or Equal    | Check if the left object is less than or equal to the right object    | `std.ops.Le`  |
| `>`                 | Greater Than          | Check if the left object is greater than the right object             | `std.ops.Gt`  |
| `>=`                | Greater Than or Equal | Check if the left object is greater than or equal to the right object | `std.ops.Ge`  |
| `<=>`               | Compare               | Compare two objects                                                   | `std.ops.Cmp` |

| Assignment Operator | Name                      | Description                                                      | Class                  |
|---------------------|---------------------------|------------------------------------------------------------------|------------------------|
| `=`                 | Assignment                | Assign the right object to the left object                       | `std.ops.Assign`       |
| `+=`                | Addition Assignment       | Add the right object to the left object                          | `std.ops.AddAssign`    |
| `-=`                | Subtraction Assignment    | Subtract the right object from the left object                   | `std.ops.SubAssign`    |
| `*=`                | Multiplication Assignment | Multiply the left object by the right object                     | `std.ops.MulAssign`    |
| `/=`                | Division Assignment       | Divide the left object by the right object                       | `std.ops.DivAssign`    |
| `%=`                | Remainder Assignment      | Get the remainder of the left object divided by the right object | `std.ops.RemAssign`    |
| `%%=`               | Modulo Assignment         | Get the modulo of the left object divided by the right object    | `std.ops.ModAssign`    |
| `**=`               | Exponent Assignment       | Raise the left object to the power of the right object           | `std.ops.PowAssign`    |
| `&&=`               | And Assignment            | Logical and the left object with the right object                | `std.ops.AndAssign`    |
| `\|\|=`             | Or Assignment             | Logical or the left object with the right object                 | `std.ops.OrAssign`     |
| `&=`                | Bitwise And Assignment    | Bitwise and the left object with the right object                | `std.ops.BitAndAssign` |
| `\|=`               | Bitwise Or Assignment     | Bitwise or the left object with the right object                 | `std.ops.BitOrAssign`  |
| `^=`                | Bitwise Xor Assignment    | Bitwise xor the left object with the right object                | `std.ops.BitXorAssign` |
| `<<=`               | Bitwise Shift Assignment  | Bitwise shift the left object by the right object                | `std.ops.BitShlAssign` |
| `>>=`               | Bitwise Shift Assignment  | Bitwise shift the left object by the right object                | `std.ops.BitShrAssign` |
| `<<<=`              | Bitwise Rotate Assignment | Bitwise rotate the left object by the right object               | `std.ops.BitRolAssign` |
| `>>>=`              | Bitwise Rotate Assignment | Bitwise rotate the left object by the right object               | `std.ops.BitRorAssign` |

### Operator Chaining
- Simplify comparison operator chaining by manipulating the AST to add `&&` operators.
- `0 < a < 100` is valid, instead of the longer and more convoluted `0 < a && a < 100`.
- This feature is taken from Python.

#### Chainable operators
- The "spaceship" comparison operator, `<=>`, is not chainable.
- The `is` comparison operator is not chainable.
- All other comparison operators are chainable: `==`, `!=`, `<`, `<=`, `>`, `>=`.

## Operator overloading
- Operators can be overloaded by superimposing the corresponding classes.
- All operator classes are found in the `std.ops` module.


### Binary operator overloading example
```s++
cls Vector3D {
    x: Num
    y: Num
    z: Num
}

sup std.ops.Add[Rhs=Vector3D, Out=Vector3D] on Vector3D {
    fun add(self, that: Rhs) -> Out {
        ret Vector3D { x = self.x + that.x, y = self.y + that.y, z = self.z + that.z }
    }
}
```
- Specify the `Rhs` and `Out` types in the `sup` block.
- Implement the `add` method from the `std.ops.Add` class.
- In practice, `Rhs` and `Out` are optional, and default to `Self`, to are not required to be specified.
    - `sup std.ops.Add on Vector3D { ... }`
    - `fun add(self, that: Self) -> Self { ... }`

### Operator classes and methods
| Operator | Class          | Function                           |
|----------|----------------|------------------------------------|
| `\|\|`   | `std.ops.Or`   | `fun or(self, that: Rhs) -> Out`   |
| `&&`     | `std.ops.And`  | `fun and(self, that: Rhs) -> Out`  |
| `==`     | `std.ops.Eq`   | `fun eq(self, that: Rhs) -> Bool`  |
| `!=`     | `std.ops.Ne`   | `fun ne(self, that: Rhs) -> Bool`  |
| `<`      | `std.ops.Lt`   | `fun lt(self, that: Rhs) -> Bool`  |
| `<=`     | `std.ops.Le`   | `fun le(self, that: Rhs) -> Bool`  |
| `>`      | `std.ops.Gt`   | `fun gt(self, that: Rhs) -> Bool`  |
| `>=`     | `std.ops.Ge`   | `fun ge(self, that: Rhs) -> Bool`  |
| `<=>`    | `std.ops.Cmp`  | `fun cmp(self, that: Rhs) -> Ord`  |
| `<<`     | `std.ops.Shl`  | `fun shl(self, that: Rhs) -> Out`  |
| `>>`     | `std.ops.Shr`  | `fun shr(self, that: Rhs) -> Out`  |
| `<<<`    | `std.ops.Rol`  | `fun rol(self, that: Rhs) -> Out`  |
| `>>>`    | `std.ops.Ror`  | `fun ror(self, that: Rhs) -> Out`  |
| `+`      | `std.ops.Add`  | `fun add(self, that: Rhs) -> Out`  |
| `-`      | `std.ops.Sub`  | `fun sub(self, that: Rhs) -> Out`  |
| `\|`     | `std.ops.BOr`  | `fun bor(self, that: Rhs) -> Out`  |
| `^`      | `std.ops.BXor` | `fun bxor(self, that: Rhs) -> Out` |
| `*`      | `std.ops.Mul`  | `fun mul(self, that: Rhs) -> Out`  |
| `**`     | `std.ops.Pow`  | `fun pow(self, that: Rhs) -> Out`  |
| `/`      | `std.ops.Div`  | `fun div(self, that: Rhs) -> Out`  |
| `%`      | `std.ops.Rem`  | `fun rem(self, that: Rhs) -> Out`  |
| `%%`     | `std.ops.Mod`  | `fun mod(self, that: Rhs) -> Out`  |
| `&`      | `std.ops.BAnd` | `fun band(self, that: Rhs) -> Out` |


## Postfix operators
- Postfix operators are operators that come after the operand.

| Postfix Operator | Name                  | Description                                                                              |
|------------------|-----------------------|------------------------------------------------------------------------------------------|
| `(...)`          | `FunRef` Call         | Call a function or method with the given arguments, immutably borrowing the environment. |
| `(...)`          | `FunMut` Call         | Call a function or method with the given arguments, mutably borrowing the environment.   |
| `(...)`          | `FunOne` Call         | Call a function or method with the given arguments, taking ownership of the environment. |
| `{...}`          | Object Initialization | Initialize a struct with the given fields.                                               |
| `.`              | Member access         | Access a member of a struct or enum.                                                     |
| `?`              | Early return          | Return early from a function or method.                                                  |
