# Rust Lang

## Borrowing and References

* Ownership is transferred once the variable is assigned to another.
  * **OR IF ITS USED IN A FUNCTION CALL**
* References are read only access to a heap value.
* If you would like to modify a referenced value, you must make it a `&mut` reference, which can only be done once in a scope.
* Mut references may be in the same scope but once you reassign to another variable, the previous one becomes invalid.
* We also cannot have a mutable reference while we have an immutable one to the same value.

## Enums

* Enums describe different versions of a thing. In Rust, they can actually hold some kind of value other than just a number value. So instead of creating a struct for each version of a thing, with a field with an enum value in them, you can directly attach the struct or thing to the enum itself.

## Types

* When you use the `.into()`, it attempts to convert the type to whatever the type label is. But in the case of a function call, it will assume whatever the parameter type is.

Example:

```rust
fn string_slice(arg: &str) {
    println!("{}", arg);
}
fn string(arg: String) {
    println!("{}", arg);
}

// works:
string_slice("nice weather".into());
// and the following works:
string("nice weather".into());
```

Because either way, the compiler will assume the type based on the parameter type.
