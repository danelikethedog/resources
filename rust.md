# Rust Lang

## Borrowing and References

* Ownership is transferred once the variable is assigned to another.
  * **OR IF ITS USED IN A FUNCTION CALL**
* References are read only access to a heap value.
* If you would like to modify a referenced value, you must make it a `&mut` reference, which can only be done once in a scope.
* Mut references may be in the same scope but once you reassign to another variable, the previous one becomes invalid.
* We also cannot have a mutable reference while we have an immutable one to the same value.
* Some types are auto-deref'd, so you don't need to use the `*` operator to deref them.

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

## Mapping Return Values

* You can one return type to another using the `.map_err()` method. The `enum` contains the types and the `impl`. The `enum` has inside the value the `Result` you want to "wrap". The `impl` will then have functions which create the new enums from the `Result`s.

```rust
enum ParsePosNonzeroError {
    Creation(CreationError),
    ParseInt(ParseIntError),
}

impl ParsePosNonzeroError {
    fn from_creation(err: CreationError) -> ParsePosNonzeroError {
        ParsePosNonzeroError::Creation(err)
    }
    // TODO: add another error conversion function here.
    fn from_parseint(err: ParseIntError) -> ParsePosNonzeroError {
        ParsePosNonzeroError::ParseInt(err)
    }
}

fn parse_pos_nonzero(s: &str) -> Result<PositiveNonzeroInteger, ParsePosNonzeroError> {
    // TODO: change this to return an appropriate error instead of panicking
    // when `parse()` returns an error.
    let x: i64 = s.parse().map_err(ParsePosNonzeroError::from_parseint)?;
    PositiveNonzeroInteger::new(x).map_err(ParsePosNonzeroError::from_creation)
}
```

## Traits

They are very similar to `interfaces` in other languages.

You can actually specify parameters which implement certain features, and use that to call certain methods.

```rust
pub fn notify(item: &impl Summary) {
    println!("Breaking news! {}", item.summarize());
}
```

This is synactic sugar for:

```rust
pub fn notify<T: Summary>(item: &T) {
    println!("Breaking news! {}", item.summarize());
}
```

Where we are saying `item` is a reference to a type which implements the `Summary` trait. The generic type `T` is "bound" by the `Summary` trait.

### Derive

* Comparison traits: Eq, PartialEq, Ord, PartialOrd.
  * PartialEq might be a book with different formats. The `type` field is inside the struct, but you can make it PartialEq to the type by saying that it is `PartialEq<BookFormat>` where the inner type is compared to the enum type.
* Clone, to create T from &T via a copy.
* Copy, to give a type 'copy semantics' instead of 'move semantics'.
* Hash, to compute a hash from &T.
* Default, to create an empty instance of a data type.
* Debug, to format a value using the {:?} formatter.

## Lifetime Annotations

* Lifetime syntax is about connecting the lifetimes of various parameters and return values of functions.
* Lifetime annotations are used to tell the compiler how long a reference is valid for.
* The compiler will try to infer the lifetime of a reference, but it can't always do it.
* Add the lifetime annotation to the function signature, and then to the reference itself, along with the return value.

## Message Passing

* Message passing is a way to pass data between threads.
* The `mpsc` module is used for message passing.
* [Link to doc](https://doc.rust-lang.org/stable/book/ch16-02-message-passing.html)

## Async/Await

* The code is not run until you `.await` it.
* The lifetime of the Future is the same as its parameters. If you have some parameters which will go out of scope relative to your async call, you can wrap the parameters and async call in an async block.

```rust
fn bad() -> impl Future<Output = u8> {
    let x = 5;
    borrow_x(&x) // ERROR: `x` does not live long enough
}

fn good() -> impl Future<Output = u8> {
    async {
        let x = 5;
        borrow_x(&x).await
    }
}
```

* You should not use types which are not `Send` in async blocks. This is because the async block is run on a separate thread, and the types must be able to be sent to that thread. This means you also shouldn't use types which are not `Sync` in async blocks (references).

* Pinning is needed to make sure that the future is not moved around in memory. This is because the future is stored on the heap, and the compiler can't know if the future will be moved around in memory. If pointers move location from inside that struct, you can have issues.
