# 12 - Memory

## Contents
- [Introduction](#introduction)
- [2nd class borrows](#2nd-class-borrows)
- [Law of Exclusivity](#law-of-exclusivity)
- [Move vs Copy](#move-vs-copy)
- [Memory errors mitigated](#memory-errors-mitigated)

## Introduction
- S++ has several memory safety mechanisms to ensure that no memory errors ever occur.
- The memory model is based on 2nd class references, and the law of exclusivity.
- All memory errors that could (not will) occur are caught at compile time (being too careful > missing some errors).

## 2nd class borrows
- Borrows to an object can only be taken at function call sites, or `yield` statements.
- Borrows cannot be returned, or stored in class attributes (extending their lifetime).
- Borrows can always be stored on the stack, improving performance and automatically releasing.

### Creating borrows
- The borrows are valid for the duration of the function call (push to stack).
- The borrows are automatically released at the end of the function call (pop from stack).
- See the [law of exclusivity](#law-of-exclusivity) for more information on conflicting borrows.

#### Creating an immutable borrow
- A simple example might look like `f(&x, &y)`.
- This takes immutable borrows of `x` and `y`.

#### Creating a mutable borrow
- A simple example might look like `f(&mut x, &mut y)`.
- This takes mutable borrows of `x` and `y`.

### Lifetime analysis
- There is no need for lifetime analysis, as control will always come back to the caller, forcing the condition that 
  a borrow's lifetimes is **always** <= the lifetime of the object it borrows.
- This means that the owner object will always outlive the borrow and therefore the borrow will always be valid.

### Mutability of Borrows vs Values
- The mutability of a borrow is not necessarily the same as the mutability of the value it is stored under.
- The mutability of a borrow is only relevant to the value it points to, not the value it is stored under.
- For example, `fun (x: &mut Num) -> Void { }` takes a mutable borrow of `x`, but `x` itself is immutable.
  - That means whilst `x` cannot be reassigned, the value it points to can be mutated.
- For example, `fun (mut x: &Num) -> Void { }` takes an immutable borrow of `x`, but `x` itself is mutable.
  - That means whilst `x` can be reassigned, the value it points to cannot be mutated.

#### Mutability truth table
| Declaration     | Description                                              |
|-----------------|----------------------------------------------------------|
| `x: &T`         | The borrow `x` is immutable, and can not be re-assigned. |
| `x: &mut T`     | The borrow `x` is mutable, and can not be re-assigned.   |
| `mut x: &T`     | The borrow `x` is mutable, and can be re-assigned.       |
| `mut x: &mut T` | The borrow `x` is mutable, and can be re-assigned.       |

## Law of Exclusivity
- The law of exclusivity ensures that borrows are don't conflict with each other at a single call site.
- They prevent conflicting borrows from being taken by checking each borrow against a set of rules, which involve 
  all borrows in the function call.
- The law of exclusivity is enforced at compile time, and is therefore impossible to break at runtime.

## Laws
- Only 1 mutable borrow to an object can exist at a time.
- N amount of immutable borrows to an object can exist at a time.
- A mutable borrow cannot exist at the same time as an immutable borrow.

## Overlapping
- If an entire object is being borrowed mutably, even an attribute cannot be borrowed immutably.
- If an attribute of an object is being borrowed mutably, the entire object cannot be borrowed mutably, but another 
  attribute can, as there would be no overlap.

## Move vs Copy
- When a value is passed to a function, it is either moved or copied.
- If the value is moved, the caller no longer has access to it.
- If the value is copied, the caller still has access to it.
- Moving is always the semantic used unless `Copy` is [superimposed]() on the type.

### Copying
- Superimpose the `Copy` type onto the type to allow for copying.
- The `copy` method is automatically called when a copy is required.
```s++
sup Copy on Str {
    fun copy(self) -> Self { ... }
}
```

## Memory errors mitigated

#### Null pointer dereference
This commonly occurs in C++ when a pointer is pointing to nothing, and is subsequently de-referenced, but as it 
doesn't actually reference anything, an irrecoverable error is thrown. S++ has no concept of pointers, and all borrows
are always valid because they are second class. As it is both impossible to ever reference a "nothing" object, and 
have a pointer that can be de-referenced, it is impossible to ever attempt to de-reference a null pointer.

#### Dangling pointer / Use after free
This is when a pointer to an object is used after it has been freed. This is impossible in S++ because all object 
ownership is tracked, as soon as an identifier is "moved" ie is the parameter to a function with the by-value 
convention, any subsequent attempt to use the identifier, except assigning to it again, will result in a compile 
time error. This means that it is impossible to ever use an object after it has been freed. After re-assignment, the
object is no longer considered to be freed, and can be used again.

#### Buffer overflow & underflow
This is when a buffer is written to or read from outside its bounds. This is impossible in S++ because all buffers 
are checked to ensure that the read or write is within its bounds at runtime. This is done by storing the length of
the buffer in the buffer itself, and checking that the read or write is within the bounds of the buffer. This is 
done for all buffers, including strings, arrays, and slices.

#### Memory leak
This is when an object is not freed. This is impossible in S++ because all objects, including borrows, are stored on
the stack, and are therefore freed when the scope ends. This means that it is impossible to ever have a memory leak.

#### Double free
This is when an object is freed twice. This is impossible in S++ because there is no way to manually "free" an
object, as all memory operations are automatically managed. This means that it is impossible to ever double free an
object. It is possible to call a method which consumes the object, but ownership tracking, as discussed in
[Dangling pointer / Use after free](#dangling-pointer--use-after-free), prevents the object from being used again to free it.

#### Uninitialised memory
This is when a variable is read from before it has been initialised. This is impossible in S++ because ownership
tracking ensures that uninitialised objects are not used, except for setting their value. This means that it is
impossible to ever read from an uninitialised object. When a variable is defined like `let x: BigNum`, it is 
modelled as a freed object, so ownership tracking prevents usage until it is initialised.

#### Pointer arithmetic
This is when a pointer is incremented or decremented by an invalid amount. This is impossible in S++ because there
is no concept of pointers, and all borrows are always valid because they are second class. This means that it is
impossible to ever attempt to increment or decrement a pointer.

#### Memory corruption
This is when a pointer is used to access memory that it does not point to. This is impossible in S++ because borrows
safely abstract over pointers. This means that it is impossible to ever attempt to access memory that is not
pointed to by the borrow.

#### Type conversion
This is when a pointer is converted to a pointer of a different type. This is impossible in S++ because all type
conversions happen at the object level, as there is no access to pointers. There are no implicit conversions either.
This means that it is impossible to ever attempt to convert a pointer to a pointer of a different type.

#### Race condition
This is when two threads access the same memory at the same time. This is impossible in S++ because unique ownership,
required that the `Shared[T]` type is used over a `Mutex[T]` type, which forces safe access of data being used over 
multiple threads.

#### Deadlock
This is when two threads are waiting on each other. This is impossible in S++ because all memory accesses are atomic,
and mutexes are not re-entrant and can not be locked twice. This means that it is impossible to ever have a deadlock.
TODO (is this true?)

#### Static data
- No static variables or attributes are allowed.
- TODO

#### Global data
- No global variables or attributes are allowed.
- TODO

#### Alignment
TODO

#### Stack overflow
TODO

#### Heap fragmentation
TODO
