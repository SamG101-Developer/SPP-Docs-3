# 10 - Classes
## Contents
- [Introduction](#introduction)
- [Class declaration](#class-declaration)
- [Superimposition](#superimposition)
- [Class instantiation](#class-instantiation)
- [Class destruction](#class-destruction)
- [Self](#self)
- [Reflection](#reflection)

## Introduction
- Classes in S++ are similar to Rust - they separate their "state" and "behavior" into different blocks.
- Separation of state and behavior allows for behavioural extensions to be added to a class: [superimposition]().
- Classes are [types]() and therefore require a PascalCase name.

## Class declaration
- Use the `cls` keyword to define a class.
- A PascalCase identifier will follow the `cls` keyword, which will be the name of the class.
- Generic parameters can be defined after the class name.
- The attributes of the class will be defined in a block, forming the class' "state".

### Class Attributes
- Class attributes are defined in the class block.
- Class attributes need an identifier, and a type.
- Class attributes can be given a default value, that is initialized **per instantiation** (same as function default 
  arguments for optional parameters).

### Forward declaration
- Forward declaration is not required in S++.
- This is because the compiler performs a "pre-pass" over the source code, and collects all the definitions before 
  compiling the code.

### Class declaration example:
```s++
cls Vector3D[T: Copy] {
    x: T
    y: T
    z: T
}
```
- The `Vector3D` class has three attributes: `x`, `y`, and `z`, whose type is the generic parameter `T`, constrained to 
  the `Copy` type.

## Superimposition
- Superimposition is the process of adding behaviour to a class.
- Methods can be superimposed over a class, or an entire class can be superimposed over a class (mocking inheritance).
- Superimposition is a more structured, clear and organized alternative to inheritance & combining class behavior.
- Any number of superimposition blocks can be defined, even for classes from other modules.

### Superimposing methods onto a class
```s++
cls Foo {
    a: BigNum
    b: BigNum
}

sup Foo {
    use Str as In

    fun foo(&self, s: In) -> Void { ... }
    fun bar(&self, s: In) -> Void { ... }
    fun baz(&self, s: In) -> Void { ... }
}
```
- 3 methods have been declared in the `sup` block, for the `Foo` class, allowing for `Foo{a=1, b=1}.foo()` to be called.
- Any number of `sup` blocks can be defined over a class, allowing for splitting behaviour into groups of methods.
- Methods defined without bodies, ie `{ ... }` are **abstract**.
  - An abstract method marks the class as an abstract class => non instantiable.
  - Abstract methods must be implemented when superimposing an abstract class.
- Static methods are defined by omitting the `self` parameter.
  - Static methods can be abstract too.

### Superimposing a class onto a class
```s++
sup Add on BigNum {
    fun add(&self, other: Self) -> Self { ... }
}

sup Copy on BigNum {
    fun copy(&self) -> Self { ... }
}

sup Default on BigNum {
    fun default() -> Self { ... }
}
```
- When superimposing a class onto another class, methods from the super-class can be overridden.
- Generic methods can be specialized any number of times: see [specialization]().

### Layered superimposition
- Superimposing a chain of classes, for example where `C` superimposes `B` which in turn superimposes `A`, allows for 
  methods from both `A` and `B` to be called from an instance of `C`.
- Inheritance is "flattened", so the only way to override a method from `A` on `C` is to re-superimpose `A` onto `C`,
  and re-define the method.
- For a method on `C` originally from `A`, if `B` has overridden this method from `A`, overload resolution will choose 
  the method implementation from `B` over the method implementation from `A`, based on the fact that `B` superimposed 
  `A` first, and therefore has priority.


## Class instantiation
- To instantiate a class, use the `{ ... }` syntax containing all the required members of the class.
- There is some special syntax to simplify the instantiation of classes.

### Class instantiation example
```s++
cls Vector3D {
    x: BigNum
    y: BigNum
    z: BigNum
}

fun main() -> Void {
    let v = Vector3D { x=1, y=2, z=3 }
}
```

### Class instantiation argument names:
- `<variable>`: this variable must exist as an attribute on the class type, like `x` in the above example.
- `else`: used to specify a default object whose members are moved into the new class instance, generally `T.default()`.
- `sup`: used to specify a parent class instances for the new class instance, placed in a tuple.
  - The instances of parent classes are only ever required for parent classes that are stateful and non-`Default`.
  - If a class is stateless, for example `Copy`, there is only ` way of instantiating it, so it is not required to 
    specify the instance of `Copy` to use.
  - If a class is `Default`, it can be instantiated with `T.default()`, so it is not required to specify the instance of 
    `Default` to use.
- No name: this means that the argument it self has the same identifier as an attribute on the class being 
  initialized: for example, `Vector3D { x, y, z=3 }` is the same as `Vector3D { x=x, y=y, z=3 }`.

### Access modifiers
- Private members are not accessible outside the class.
- If a class has private attributes, it can only be initialized from inside the class it is defined in.

### Non-initializable classes
- The `@non_init` annotation can be used to prevent a class from being initialized.
- It inserts a 0-byte field with an invalid name into the class, which prevents it from being initialized.
- Impossible to give the name to the field, since it is invalid.

## Class destruction
- Objects are destructed automatically when they go out of scope, because it isn't possible for them to have any 
  borrows once their own lifetime ends.
- Custom behaviour can be defined for object destruction by superimposing `std.Del` and overriding the `del` method 
  on that class.

### Class destruction example
```s++
cls Vector3D {
    x: BigNum
    y: BigNum
    z: BigNum
}

sup Del on Vector3D {
    fun del(self) -> Void {
        print("Vector3D deleted")
    }
}
```

## Self
- There are two "self" keywords in S++: `self` and `Self`.

### `self` (value)
- `self` is a keyword that refers to the current instance of the type.
- `self` is used to access attributes and call methods on the current instance.
- `self` is only available in `cls` and `sup` blocks (including `fun` blocks inside `sup` blocks, but not free 
  functions in modules).

### `Self` (type)
- `Self` is a keyword that refers to the type of the current instance.
- `Self` can be used with nested types, or static methods: `Self.InnerType`, `Self.static_method()`.
- `Self` is only available in `cls` and `sup` blocks (including `fun` blocks inside `sup` blocks, but not free 
  functions in modules).

#### Note
- `Self` must be used when accessing inner types defined in a `sup` block.
- For example, if `InnerType` is defined on a type, then `Self.InnerType` must be used to access it.

## Reflection
- Can access members and methods of the class with strings
- Hooks into the AST forming the program, using the `std.ast` module.
- **Requires [self-hosting](), for the same reason as [annotations]()**

### Operations
#### Allowed
- `.get`: get the member of a class.
- `.set`: set the member of a class (the attribute must already exist, so the memory layout stays the same).
- `.has`: check if the member of a class exists.
- `.del`: delete the member of a class (causes a partial move, so memory layout stays the same).

#### Not allowed (would require the memory layout to change)
- Creating a new attribute on a class
- Removing an attribute from a class
- Changing the type of an attribute on a class
