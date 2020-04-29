# Rust

## Variables

```rust
// inferred
let x = 5; // immutable

// immutable
let mut x = 5;

// typed - unsigned int32
let guess: u32 = "42".parse().expect("Not a number!");

// shadowing - a variable can be redefined using the previous value
let x = 5;
let x = x + 1; // x = 6
let x = x * 2; // x = 12

```

## Data types

```rust
i8 // signed int8
i32 // signed int32
// ...

// unsigned
u8 // unsigned int8
u32 // unsigned int32
// ...

```

`isize` and `usize` depend on architecture, 32 bits on 32-bit and 64 on 64-bit respectively.

```rust

// number literals
10_000 // decimal
0xff // hex
0o77 // oct
0b1111_0000 // binary
b'A' // bytes
```

```rust
// float is f64 by default
let x = 2.0 // f64

let y: f32 = 2.0 // f32
```

```rust
// tuple
let tup: (i32, f64, u8) = (500, 6.4, 1);

// destructuring
let tup = (500, 6.4, 1);

let (x, y, z) = tup;

// accessing specific fields can be done by index
let five_hundred = tup.0;
let six_point_four = tup.1;
let one = tup.2;
```

```rust
// inferred array
let a = [1, 2, 3, 4, 5];

// explicitly typed
let a: [i32; 5] = [1, 2, 3, 4, 5];

// short initialization
let a = [3; 5]; // [3, 3, 3, 3, 3]
```

## Structs

```rust
struct Ticket {
  title: String,
  description: String
}

// instantiate struct
let t = Ticket {
  title: String::from("Hello"),
  description: String::from("World"),
};

// mutate fields - entire struct has to be mutable
let mut t = Ticket {
  title: String::from("Hello"),
  description: String::from("World"),
}

t.title = String::from("Goodbye");

// when variable and field have the same name
// we can use the Field Init shorthand
fn create_ticket(title: String, description: String) -> Ticket {
  Ticket {
    title,
    description,
  }
}

// tuple struct - they are grouped by some context, but the fields
// do not have names
struct Color(i32, i32, i32);
struct Point(i32, i32, i32);

// `black` and `origin` look the same, but they are of different types
// they cannot be switched
let black = Color(0, 0, 0);
let origin = Point(0, 0, 0);

// specific values can be access by index
let r = black.0;
let g = black.1;
let b = black.2;

// Unit-Like struct
// these are empty struct, they are useful when no attributes are needed
// but you want to implement traits (similar to Go)
struct Person;
```

## Functions
```rust
// basic
fn main() {
    println!("Hello, world!");

    another_function();
}

fn another_function() {
    println!("Another function.");
}

// receive parameters
fn another_function(x: i32) {
  println!("The value of x is: {}", x);
}

// return values
fn five() -> i32 {
  5 // implicit return - when implicit return is used, semi-colon should not be used either
}
```

## Control Flow

### if-else
```rust
  let number = 3;

  if number < 5 {
    println!("true");
  } else {
    println!("false");
  }
```

```rust
  let condition = true;
  let number = if condition {
    5
  } else {
    6
  }

  println!("number: {}", number);
```

### Loops
```rust
  // infinite loop (similar to `for {}` in Go)
  loop {
    println!("again");
  }

  // returning values from loops:
  let mut counter = 0;

  let result = loop {
    counter += 1;

    if counter == 10 {
      break counter * 2; // returns 20
    }
  };

  println!("The result is {}", result);
```

```rust
  let mut number = 3;

  while number != 0 {
    println!("{}!", number);

    number -= 1;
  }

  println!("LIFTOFF!!!");
```

```rust
  let a = [10, 20, 30, 40, 50];

  for element in a.iter() {
    println!("the value is: {}", element);
  }
```

```rust
fn main() {
  for number in (1..4).rev() {
    println!("{}!", number);
  }

  println!("LIFTOFF!!!");
}
```

## Ownership

Rust does not have garbage collection, however, it does not require to explicitly allocate and
free memory. Instead, Rust uses the ownership system that is verified in compile time.

* Each value in Rust has a variable that’s called its owner.
* There can only be one owner at a time.
* When the owner goes out of scope, the value will be dropped.

```rust
  {                      // s is not valid here, it’s not yet declared
    let s = "hello";   // s is valid from this point forward

    // do stuff with s
  }                      // this scope is now over, and s is no longer valid
```

*s* becomes valid when we come into scope and becomes invalid as we leave.
A *drop* function is implicitly called at the end of the block, and this is
where Rust automatically frees all the allocated memory for us.

### Stack and heap
The types covered in `Data Types` are allocated on the stack (they are fixed size).
Ownership applies to values allocated on the heap - unknown size and growable.

The computer stack frame (within the context of a function) is random access, suppose we have the following allocations:

```rust
  let x = 8;
  let y = 7;

```

The stack frame will look roughly like this:
<pre>
<code>
 -----
 | 8 |
 -----
 | 7 |
 -----
</code>
</pre>

Since we know the size of each value (i32 by default) and we know where the stack starts
we can easily "jump" to each value using *start_offset + data_type_size + index*

### String

```rust
  // This is a string literal (type *&'static str*), it is immutable and is known ahead of time.
  // It is compiled in the binary, similar to *final String* in Java.
  let s = "string"

  // This creates a *String* from a string literal, it is *mutable*, allocated on the heap, and *owned*
  let s = String::from("string");

  // It can be mutated the following way
  let mut s = String::from("string");
  s.push_str(" string"); // "string string"
```

When we call `String::from` we request memory from the OS.
But we also need to free the memory, unlike garbage collected languages, but Rust does not require
us to explicitly free, unlike unmanaged languages.

```rust
{ // We go into scope
  let s = String::from("string");
} // The scope is over and `s` is dropped, it can no longer be used!
```

The memory we allocated for `s` is released once we go out of scope, without us doing anything explicit.
```rust
  // We push to 5's onto the stack and that is great
  let x = 5;
  let y = x;
```

```rust
{
  // We will not get two "hello" strings! Rust does not perform deep copy
  let s1 = String::from("he");
  let s2 = s1;
}
```

A *String* is made up like this:
<pre>
<code>
------------       -------------
|name|value|       |index|value|
------------       -------------
|ptr | ----------->| 0   | h   |
------------       -------------
|len | 2   |       | 1   | e   |
------------       -------------
|cap | 2   |
------------
</code>
</pre>

* `ptr` is a pointer to the address of the first character
* `len` is the amount of memory used by `s1`
* `cap` is how much it can grow without an extra allocation

When `s1` is assigned to `s2` we get another structure, similar to the one on the left, only for `s2`.
The pointer and the metadata are copied, but no the data pointed by the variable!

Since *drop* is called at the end of the block, Rust will attempt to free `s1` and then `s2`, but since the they
have a pointer to the same location on the heap, it might free the same memory area twice (*double free*)!
This leads to undefined behavior which can cause data corruption and security issues.

Luckily, Rust protects us from such atrocities, so instead of copying the pointer along with the metadata,
Rust will *move* the data from `s1` to `s2`, meaning `s1` is no longer valid

```rust
{
  // We will not get two "hello" strings!
  let s1 = String::from("he");
  let s2 = s1;
}
```
This will fail with a compilation error:
<pre>
move occurs because `s1` has type `std::string::String`, which does
  not implement the `Copy` trait
</pre>

So, in other words, Rust does no perform a "shallow copy", but rather, it performs a *move* operation.

If we do want a deep copy we can use *clone*:
```rust
let s1 = String::from("hello");
let s2 = s1.clone();

println!("s1 = {}, s2 = {}", s1, s2);
```

Rust has a `Copy` trait the we can implement for types, but it won't let derive it if the type has implemented `Drop`
(because it manages memory on its own) as well (heap allocated)

```rust
#[derive(Debug, Copy, Clone)]
struct Foo;

let x = Foo;

// since Foo derives Copy, y is a copy of x!
let y = x;


// No derive
struct Foo;

let x = Foo;

// x has moved into y and is no longer valid after the next line!
let y = x;

```

### Functions

Passing a variable to a function will move or copy, similar to `=`

```rust
fn main() {
    let s = String::from("hello");  // s comes into scope

    takes_ownership(s);             // s's value moves into the function...
                                    // ... and so is no longer valid here

    let x = 5;                      // x comes into scope

    makes_copy(x);                  // x would move into the function,
                                    // but i32 is Copy, so it’s okay to still
                                    // use x afterward

} // Here, x goes out of scope, then s. But because s's value was moved, nothing
  // special happens.

fn takes_ownership(some_string: String) { // some_string comes into scope
    println!("{}", some_string);
} // Here, some_string goes out of scope and `drop` is called. The backing
  // memory is freed.

fn makes_copy(some_integer: i32) { // some_integer comes into scope
    println!("{}", some_integer);
} // Here, some_integer goes out of scope. Nothing special happens.
```

`s` is heap-allocated, so its value is moved and not copied, ownership is passed to
`takes_ownership` and it frees it.

`x` however is stack allocated, so a copy is performed and it is still accessible after
the function call. It will be cleared after `main` completes.

#### Return values

Returning a value transfers the ownership to the caller.

```rust
fn main() {
    let s1 = gives_ownership();         // gives_ownership moves its return
                                        // value into s1

    let s2 = String::from("hello");     // s2 comes into scope

    let s3 = takes_and_gives_back(s2);  // s2 is moved into
                                        // takes_and_gives_back, which also
                                        // moves its return value into s3
} // Here, s3 goes out of scope and is dropped. s2 goes out of scope but was
  // moved, so nothing happens. s1 goes out of scope and is dropped.

fn gives_ownership() -> String {             // gives_ownership will move its
                                             // return value into the function
                                             // that calls it

    let some_string = String::from("hello"); // some_string comes into scope

    some_string                              // some_string is returned and
                                             // moves out to the calling
                                             // function
}

// takes_and_gives_back will take a String and return one
fn takes_and_gives_back(a_string: String) -> String { // a_string comes into
                                                      // scope

    a_string  // a_string is returned and moves out to the calling function
}
```

The pattern here is assignment (`=`) moves the value (heap-allocated), returning gives
ownership back. So passing a value to a function and returning is the current way for us
to make changes, quite tedious.

```rust
fn main() {
    let mut s = String::from("hello");
    s = append(s);
    println!("{}", s)
}

fn append(mut s: String) -> String {
    s.push_str(", world");
    return s;
}
```

But... there is a better way

#### References and Borrowing

If we look at the last example and want to avoid returning, only making the change and returning
the ownership back we can do:

```rust
fn main() {
    let mut s = String::from("hello");
    append(&mut s); // a mutable borrow
    println!("{}", s)
}

fn append(s: &mut String) { // Borrows the value
    s.push_str(", world"); // makes the change, but the original s is still the owner
}
```

Since `append` has no ownership of `s`, the value is not dropped.


Say we want to calculate the length of a string
```rust
fn main() {
    let s1 = String::from("hello");

    let (s2, len) = calculate_length(s1);

    println!("The length of '{}' is {}.", s2, len);
}

fn calculate_length(s: String) -> (String, usize) {
    let length = s.len(); // len() returns the length of a String

    (s, length)
}
```

If we don't return `s` in `calculate_length`, `s1` will be dropped and we will
get a compilation error:
```
2 |     let s1 = String::from("hello");
  |         -- move occurs because `s1` has type `std::string::String`, which does not implement the `Copy` trait
3 |
4 |     let len = calculate_length(s1);
  |                                -- value moved here
5 |
6 |     println!("The length of '{}' is {}.", s1, len);
  |                                           ^^ value borrowed here after move
```

So we must return `s`.

However, we just learn how we can lend a value to function without giving up our rightful ownership of it.

```rust
fn main() {
    let s1 = String::from("hello");

    let len = calculate_length(&s1);

    println!("The length of '{}' is {}.", s1, len);
}

fn calculate_length(s: &String) -> usize {
    let length = s.len(); // len() returns the length of a String

    length
}

This is an *immutable borrow*, unlike the `append` example.
Essentially, `s` is a pointer to to `s1`.

By default all borrows are *immutable* as are variables.
`&` indicates an immutable borrow, while `&mut` indicates a mutable borrow (and lend).
The caller will pass `&mut s` and callee will receive `&mut String`.

However, unlike *immutable borrow*, we can only have __one__ mutable borrow in a scope.
```rust
let mut s = String::from("hello");

let r1 = &mut s;
let r2 = &mut s;

println!("{}, {}", r1, r2);
```

Will fail with:
```
  |
4 | let r1 = &mut s;
  |          ------ first mutable borrow occurs here
5 | let r2 = &mut s;
  |          ^^^^^^ second mutable borrow occurs here
6 |
7 | println!("{}, {}", r1, r2);
  |                    -- first borrow later used here
```

This is restriction is made to help us avoid data races.

Mixing *mutable* and *immutable* borrows at the same time is also forbidden, as
it is a data race as well (the immutable borrower might read stale data).

We can do a *mutable* borrow once the *immutable* ones are no longer used (and vice-versa)
```rust
let mut s = String::from("hello");

let r1 = &s; // no problem
let r2 = &s; // no problem
println!("{} and {}", r1, r2);
// r1 and r2 are no longer used after this point

let r3 = &mut s; // no problem
println!("{}", r3);
```

if we were to use `r1` or `r2` again after the *mutable* borrow was introduced (in the same scope), the code would fail to compile.

#### Dangling references

```rust
fn main() {
    let reference_to_nothing = dangle();
}

fn dangle() -> &String {
    let s = String::from("hello");

    &s
}
```

Here `dangle` returns a reference to a `String` `s` that is dropped at the end of the function.
Thus `reference_to_nothing` will point to memory location that is no longer valid.

However, Rust will not let us do anything like this and will fail with:
```
this function's return type contains a borrowed value, but there is
  no value for it to be borrowed from
```

We can fix it by not using references, and just returning the value:
```rust
fn dangle() -> String {
    let s = String::from("hello");

    s
}
```
We are effectively transferring the ownership of `s` the caller.

### Slices

Similar to primitives, slices do not have ownership. Slices let as references contiguous sequences of
elements in a collection, rather than the entire collection.

Suppose we want to write a program that returns the first word of a string, if there's no space in the
string it will return the entire string.

```rust
fn first_word(s: &String) -> usize { // &String because we do not want ownership, usize because that's the type of a collection's index
  let bytes = s.as_bytes();

  for (i, &item) in bytes.iter().enumerate() { // enumerate returns a tuple of index and a reference to an item
    if item == b' ' {
      return i;
    }
  }

  return s.len();
}
```

We only return `usize` here, on its own without context. If there string were to change later the index we returned is
no longer valid.
```rust
fn main() {
    let mut s = String::from("hello world");

    let word = first_word(&s); // word will get the value 5

    s.clear(); // this empties the String, making it equal to ""

    // word still has the value 5 here, but there's no more string that
    // we could meaningfully use the value 5 with. word is now totally invalid!
}
```

#### String slices

Luckily, Rust provides us with a type that references a part of a `String`.
```rust
let s = String::from("hello world");

let hello = &s[0..5];
// Also works
let hello = &s[..5];

let world = &s[6..11];
// Also works
let hello = &s[6..];

// The entire string - useful when we need to return an &str
let entire = &s[..];
```

In the mamory it will look somethign like this
```
s
------------         -------------
|name|value|         |index|value|
------------         -------------
|ptr |  ------------>| 0   | h   |
------------         -------------
|len | 11  |         | 1   | e   |
------------         -------------
|cap | 11  |         | 2   | l   |
------------         -------------
                     | 3   | l   |
                     -------------
world                | 4   | o   |
------------         -------------
|name|value|         | 5   |     |
------------         -------------
|ptr |  ------------>| 6   | w   |
------------         -------------
|len | 5   |         | 7   | o   |
------------         -------------
                     | 8   | r   |
                     -------------
                     | 9   | l   |
                     -------------
                     | 10  | d   |
                     -------------
```

In other words, the slices is references to a specific "starting cell" and length of how much we
we continue after it.

Going back to `first_word`, we can now do:
```rust
fn first_word(s: &String) -> &str { // A string slice is of type &str!
  let bytes = s.as_bytes();

  for (i, &item) in bytes.iter().enumerate() {
    if item == b' ' {
      return &s[..i]
    }
  }

  return &s[..]
}
```

Now the value is tied to the string we send (it is a reference), so it cannot change.
```rust
fn main() {
    let mut s = String::from("hello world");

    let word = first_word(&s);

    s.clear(); // error!

    println!("the first word is: {}", word);
}
```

This will not compile with the error:
```
error[E0502]: cannot borrow `s` as mutable because it is also borrowed as immutable
  --> src/main.rs:18:5
   |
16 |     let word = first_word(&s);
   |                           -- immutable borrow occurs here
17 |
18 |     s.clear(); // error!
   |     ^^^^^^^^^ mutable borrow occurs here
19 |
20 |     println!("the first word is: {}", word);
   |                                       ---- immutable borrow later used here
```

`s.clear()` is a *mutable borrow* because `String#clear` receives `&mut self`. This is an operation
on an object (instance of a struct) so it does not need to be specified (similar to Python).

#### String literals are slices
```rust
let s = "Hello, world!";
```
`s` is an `&str`, as already stated earlier, this is compiled into the binary and cannot be changed.


#### String Slices as Parameters

In `first_word`, rather than taking in a &String, we can use an &str since we do not mutate the string
at all.

The signature would be:
```rust
fn first_word(s: &str) -> &str
```
And the usage would be:
```rust
fn main() {
    let my_string = String::from("hello world");

    // first_word works on slices of `String`s
    let word = first_word(&my_string[..]);

    let my_string_literal = "hello world";

    // first_word works on slices of string literals
    let word = first_word(&my_string_literal[..]);

    // Because string literals *are* string slices already,
    // this works too, without the slice syntax!
    let word = first_word(my_string_literal);
}
```

#### Other slices

We can also have slices on types other than strings (a `String` slice is &str).
An i32 vector would be &[i32]
```rust
let a = [1, 2, 3, 4, 5];

let slice = &a[1..3];
```
`slice` has a reference to `2` and length of 2, so it will have the [2, 3]


## Structs - continued

```rust
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let rect1 = Rectangle { width: 30, height: 50 };

    println!(
        "The area of the rectangle is {} square pixels.",
        area(&rect1)
    );
}

fn area(rectangle: &Rectangle) -> u32 {
    rectangle.width * rectangle.height
}
```

Here `rectangle` is an *immutable borrow* of `Rectangle`. We are not changing the struct so no need for a *mutable borrow*,
and since `main()` uses it after function call, we cannot take owenrship of it.

### Adding Functionality with Derived Traits

If we want to print the struct using `println!` the following way:
```rust
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let rect1 = Rectangle { width: 30, height: 50 };

    println!("rect1 is {}", rect1);
}
```

This, however, would produce an error:
```
error[E0277]: `Rectangle` doesn't implement `std::fmt::Display`
```

For this to work we need derive the `Debug` trait and use `{:?}` instead of `{}`

```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let rect1 = Rectangle { width: 30, height: 50 };

    println!("rect1 is {:?}", rect1);
}
```
Would output: `rect1 is Rectangle { width: 30, height: 50 }`

Even better output can be achieved by using `{:#?}` which will produce this magnificent output:
```
rect1 is Rectangle {
    width: 30,
    height: 50
}
```


### Method Syntax

Methods are similar to structs as they are defined with `fn`, but differ in the context in which they are created.
Methods are created in the context of a `struct` (or an `enum` or a *trait object*). Their first parameter is `self`, which
represents an instance of the `struct` on which they are applied (similar to Python or `this` in Java).

```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }
}

fn main() {
    let rect1 = Rectangle { width: 30, height: 50 };

    println!(
        "The area of the rectangle is {} square pixels.",
        rect1.area()
    );
}
```

In the example the method borrows `self` immutabely, but it can also get ownership by passing `self` without `&` or borrow mutably with `&mut self`.

Implementing a second method that accept an instance of the struct

```rust

impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }

    fn can_hold(&self, other: &Rectangle) -> bool {
        self.width > other.width && self.height > other.height
    }
}
```

`can_hold` checks whether another instance of `Rectangle` can fit into the current instance. We use `&self` and `&Rectangle` because
we do not want to change any of the instances and we do not want ownership to allow the calling function to use the instances after
this function call.

Usage will look like this:
```rust
fn main() {
    let rect1 = Rectangle { width: 30, height: 50 };
    let rect2 = Rectangle { width: 10, height: 40 };
    let rect3 = Rectangle { width: 60, height: 45 };

    println!("Can rect1 hold rect2? {}", rect1.can_hold(&rect2)); // true
    println!("Can rect1 hold rect3? {}", rect1.can_hold(&rect3)); // false
}
```

### Associated Function

We can define function within the `impl` block that do not receive `self`. This is like `static` function in Java.
They are commonly used for creation purposes, `String::from("hello")` - `from` is an example of an associated function.

Creating one on `Rectangle`
```rust
impl Rectangle {
    fn square(size: u32) -> Rectangle {
        Rectangle { width: size, height: size }
    }
}
```

To call `square` we'd do `Rectangle::square(10)`. The `::` is a namespace notation, and the function, being under `Rectangle`,
is namespaced by it.

Multiple `impl` blocks are allowed as well.


## Enums

Simple enums are defined the following way:
```rust
enum IpAddrKind {
    V4,
    V6,
}
```

They can be used with namespace notation `::`
```rust
let four = IpAddrKind::V4;
let six = IpAddrKind::V6;
```

Accepting an enum kind and passing in functions:
```rust
fn route(ip_kind: IpAddrKind) { }

route(IpAddrKind::V4);
route(IpAddrKind::V6);
```

If we want to usually we could do something like this with a `struct`:
```rust

struct IpAddr {
    kind: IpAddrKind,
    address: String,
}


let home = IpAddr {
    kind: IpAddrKind::V4,
    address: String::from("127.0.0.1"),
};

let loopback = IpAddr {
    kind: IpAddrKind::V6,
    address: String::from("::1"),
}
```

But enums give us a much better an concise way:
```rust
enum IpAddr {
    V4(String),
    V6(String),
}

let home = IpAddr::V4(String::from("127.0.0.1"));

let loopback = IpAddr::V6(String::from("::1"));
```

This adds typed data to each variant!
We can even use different types for each variant:
```rust
enum IpAddr {
    V4(u8, u8, u8, u8),
    V6(String),
}

let home = IpAddr::V4(127, 0, 0, 1);

let loopback = IpAddr::V6(String::from("::1"));
```
Now `IpAddr::V4` is a tuple of 4 integers, and `IpAddr::V6` is a `String` (since ipv6 can have all characters as well).
The standard library already contains `use std::net::{IpAddr, Ipv4Addr, Ipv6Addr};`.

```rust
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(i32, i32, i32),
}
```

The `struct` equivalent of this would be:
```rust

struct QuitMessage; // unit struct
struct MoveMessage {
    x: i32,
    y: i32,
}
struct WriteMessage(String); // tuple struct
struct ChangeColorMessage(i32, i32, i32); // tuple struct
```

But we wouldn't be easily able to implement a method for these structs as we would for the `enum`!
```rust
impl Message {
    fn call(&self) {
         println!("{:?}", self);
    }
}

let m = Message::Write(String::from("hello"));
m.call();
```

This would print `Write("hello")` (of course #[derive(Debug)] has to be added to the `enum`).

### Option Enum

The Rust standard library exposes the `Option` enum. This enum is useful whenever a value can be present or missing (same as `Optional` in Java).
Rust does not have `null`, so using `Option::None` is the way to represent a missing value.

```rust
enum Option<T> {
    Some(T),
    None,
}
```

The `Option` enum is included in the prelude, so no need to use the namespace notation (`::`).

```rust
    let some_number = Some(5);
    let some_string = Some("a string");

    let absent_number: Option<i32> = None;
```

The `<>` notation is for generics, like in Java. Because we assign to None, Rust cannot know ahead of time what type will be stored in `Some`.

```rust
    let x: i8 = 5;
    let y: Option<i8> = Some(5);

    let sum = x + y;
```

This fails as `+` is not a defined operation on the `Option` type. Essentially, `Option<T>` has to be converted to `T` before we can operate
on it.

By using `Option<T>` we allow a "`null`" value, but by using `Option<T>` Rust forces to handle both cases, `Some` and `None`.
How do we do that? we have [methods](https://doc.rust-lang.org/std/option/enum.Option.html) like `is_some` and `is_none`, but there an even
better way:

### match

Rust has a cool operator call `match`. It allows comparing a value against a pattern, and perform code for each pattern
(similar to `if` and `switch-case`, but not exactly).

```rust
enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter,
}

fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => 1,
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter => 25,
    }
}
```

With `if` we need to return a boolean value `if coin == Coin::Penny` here there is no need to and it also requires deriving `PartialEq`.

Using multiple lines of code in an arm
```rust
fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => {
            println!("Lucky penny!");
            1
        }
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter => 25,
    }
}
```

```rust
#[derive(Debug)] // so we can inspect the state in a minute
enum UsState {
    Alabama,
    Alaska,
    // --snip--
}

enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter(UsState),
}
```

To acesss the `UsState` value, we can add another variable
```rust
fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => 1,
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter(state) => {
            println!("State quarter from {:?}!", state);
            25
        }
    }
}
```

Using a call like `value_in_cents(Coin::Quarter(UsState::Alaska))`, will make `state` have the value of `UsState::Alaska`. And `coin`
will be `Coin::Quarter(UsState::Alaska)`.

### Matching Option<T>

So how do we properly handle the the two options of `Option<T>`?
```rust
fn plus_one(x: Option<i32>) -> Option<i32> {
    match x {
        None => None,
        Some(i) => Some(i + 1),
    }
}

let five = Some(5);
let six = plus_one(five);
let none = plus_one(None);
```
This way we can perform operation, we return `None` in case there's nothing, and perform +1 if there's something and return the result
wrapped with `Some` which is an `Option<T>`.

If for some reason we forget to create an arm for `None`:
```rust
fn plus_one(x: Option<i32>) -> Option<i32> {
    match x {
        Some(i) => Some(i + 1),
    }
}
```

Rust will be angry at us:
```
error[E0004]: non-exhaustive patterns: `None` not covered
 --> src/main.rs:3:15
  |
3 |         match x {
  |               ^ pattern `None` not covered
  |
  = help: ensure that all possible cases are being handled, possibly by adding wildcards or more match arms
```

Matches are exhaustive, all patterns have to be accounted for!

### _ Placeholder

using `_` in a `match` expressions allows us to match "all other values" at once:
```rust
let some_u8_value = 0u8;
match some_u8_value {
    1 => println!("one"),
    3 => println!("three"),
    5 => println!("five"),
    7 => println!("seven"),
    _ => (),
}
```
`_` will match 0, 8 up to 255, they all will lead to returning a *Unit value*.

### if let

Sometimes a more concise approach is wanted.

```rust
let some_u8_value = Some(0u8);
match some_u8_value {
    Some(3) => println!("three"),
    _ => (),
}
```

Instead, we can do:
```rust
if let Some(3) = some_u8_value {
    println!("three");
}
```

But! This comes at a price: we lose the exhaustive checking and in this case `None` is not properly handled. This means we need
to be careful when choosing one over the other and be mindful of the trade-offs.

Another example:
```rust
let mut count = 0;
match coin {
    Coin::Quarter(state) => println!("State quarter from {:?}!", state),
    _ => count += 1,
}
```

Is the same as writing:
```rust
let mut count = 0;
if let Coin::Quarter(state) = coin {
    println!("State quarter from {:?}!", state);
} else {
    count += 1;
}
```

## Package, Crates and Modules

>* Packages: A Cargo feature that lets you build, test, and share crates
>* Crates: A tree of modules that produces a library or executable
>* Modules and use: Let you control the organization, scope, and privacy of paths
>* Paths: A way of naming an item, such as a struct, function, or module

### Packages and Crates

A crate is a binary or a library.
The crate root is a source file that the Rust compiler starts from and makes up the root module of the create.
A package is one or more crates that provide a set of functionality. A package contains a Cargo.toml file that describes how to build those crates.

A package must contain zero or one library crates, it can contain many binary crates, but it must contain at least one crate, either binary or a
library crate.

```shell
$ cargo new my-project
     Created binary (application) `my-project` package
$ ls my-project
Cargo.toml
src
$ ls my-project/src
main.rs
```

The convention Rust follows for binary crates is that the main file will be located at `src/main.rs`, this is the create root of package with the same name.
If a package contains a `src/lib.rs`, this is the crate root of a library with the same name. Rust passes the files under the crate root to `rustc` when
building.

In the example above, we have a package that only contains `src/main.rs`, meaning it only contains a binary crate named `my-project`.
A package containing both `src/main.rs` and `src/lib.rs`, will have 2 crates both with same name as the package.

A crate groups functionality together in a scope. The `rand` crate provides generation of random numbers and is used the following way:
```rust
use rand::Rng;
```

Here we use a trait named `Rng`, but we can also declare our own `Rng`, since `rand`'s `Rng` is namespaced with `::`.
So our `Rng` will be just `Rng` and `rand`'s will be `rand::Rng`.

### Defining Modules to Control Scope and Privacy

Modules let us organize code within the scope of a crate. They also control the privacy of code (public/private).

Creating a new library project:
```
cargo new --lib restaurant
```

```rust
mod front_of_house {
    mod hosting {
        fn add_to_waitlist() {}

        fn seat_at_table() {}
    }

    mod serving {
        fn take_order() {}

        fn serve_order() {}

        fn take_payment() {}
    }
}
```

Here we create a module `front_of_house` which represent the "front" of the restaurant (servers and hosts). A module is defined with
the `mod` keyword.

By using modules we can group related stuff together, such as functions, traits, enums and more without worrying about collisions
and organize our code better.

The module tree for the restaurant will look like this:
```
crate
 └── front_of_house
     ├── hosting
     │   ├── add_to_waitlist
     │   └── seat_at_table
     └── serving
         ├── take_order
         ├── serve_order
         └── take_payment
```

`hosting` and `serving` are submodules of `front_of_house`. `crate` is the *crate root* (`src/main.rs` or `src/lib.rs`).


### Paths for Referring to an Item in the Module Tree

>To show Rust where to find an item in a module tree, we use a path in the same way we use a path when navigating a filesystem.
>If we want to call a function, we need to know its path.

>A path can take two forms:
>* An absolute path starts from a crate root by using a crate name or a literal crate.
>* A relative path starts from the current module and uses self, super, or an identifier in the current module.

Absolute paths and relative paths use the namespace notation `::`.
Suppose we want to call `add_to_waitlist` in our `src/lib.rs` with either:
```rust
mod front_of_house {
    mod hosting {
        fn add_to_waitlist() {}
    }
}

pub fn eat_at_restaurant() {
    // Absolute path
    crate::front_of_house::hosting::add_to_waitlist();

    // Relative path
    front_of_house::hosting::add_to_waitlist();
}
```

`crate` refers to the *crate root* and starts the absolute path.
The relative path, starting with `front_of_house` works because we are in fact at the *crate root*.

The code above will not compile however:
```
error[E0603]: module `hosting` is private
 --> src/lib.rs:9:30
  |
9 |       crate::front_of_house::hosting::add_to_waitlist();
  |                              ^^^^^^^ this module is private
  |

```

Everything in Rust modules is private by default. Items in parent modules can't access the child's private itmes, but it would work vice-versa.
We can expose private items to the parent using the `pub` keyword.

```rust
mod front_of_house {
    pub mod hosting {
        fn add_to_waitlist() {}
    }
}

pub fn eat_at_restaurant() {
    // Absolute path
    crate::front_of_house::hosting::add_to_waitlist();

    // Relative path
    front_of_house::hosting::add_to_waitlist();
}
```
This fix would still fail however.

```
 --> src/lib.rs:9:37
  |
9 |     crate::front_of_house::hosting::add_to_waitlist();
  |                                     ^^^^^^^^^^^^^^^

error[E0603]: function `add_to_waitlist` is private
  --> src/lib.rs:12:30
   |
12 |     front_of_house::hosting::add_to_waitlist();
   |                              ^^^^^^^^^^^^^^^

```

Making `hosting` public allows us to access the module, but its items are still private. We have to make `add_to_waitlist` public as well.

```rust
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
}

pub fn eat_at_restaurant() {
    // Absolute path
    crate::front_of_house::hosting::add_to_waitlist();

    // Relative path
    front_of_house::hosting::add_to_waitlist();
}
```

This works. `front_of_house` isn't public, `eat_at_restaurant` is defined is defined in the same module (they are siblings),
thus it can refer `front_of_house`.


#### Starting Relative Paths with super

It is possible to start a relative path referring to the parent module with the `super` keyword.
This is similar to `..` in the filesyste.

Suppose we need to have the chef fix the incorrect order, but the chef is part of the `back_of_house`
model, while `serve_order` is a `front_of_house` function.
```rust
fn serve_order() {}

mod back_of_house {
    fn fix_incorrect_order() {
        cook_order();
        super::serve_order();
    }

    fn cook_order() {}
}
```

`super` in this case is the parent module of `back_of_house`, and in this case it just refers to `crate`.
The advantage of using `super` in this case is that it allows us the flexibility of moving this to another
module without having to change anything, unlike if we left it with `crate`.

#### Making Structs and Enums Public

`pub` can also be used to mark fields within structs as public.

```rust

mod back_of_house {
    pub struct Breakfast {
        pub toast: String,
        seasonal_fruit: String,
    }

    impl Breakfast {
        pub fn summer(toast: &str) -> Breakfast {
            Breakfast {
                toast: String::from(toast),
                seasonal_fruit: String::from("peaches"),
            }
        }
    }
}

pub fn eat_at_restaurant() {
    // Order a breakfast in the summer with Rye toast
    let mut meal = back_of_house::Breakfast::summer("Rye");
    // Change our mind about what bread we'd like
    meal.toast = String::from("Wheat");
    println!("I'd like {} toast please", meal.toast);

    // The next line won't compile if we uncomment it; we're not allowed
    // to see or modify the seasonal fruit that comes with the meal
    // meal.seasonal_fruit = String::from("blueberries");
}
```
Here only `toast` public, as we don't want the user to have control of `seasonal_fruit`.

enums marked with `pub` however, always have all their variants public. Since an enum represents a single
value at a time, it doesn't make much sense making only some of the values available.

### Bringing Paths into Scope with the use Keyword

Using paths like we previously did is long and annoying. Instead, like in other languages, we
have a keyword to bring modules or even specific items into the scope, in Rust it's called `use`.

```rust
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
}

use crate::front_of_house::hosting;

pub fn eat_at_restaurant() {
    hosting::add_to_waitlist();
    hosting::add_to_waitlist();
    hosting::add_to_waitlist();
}
```

`use` is similar to `import` in Java and Python. Bringing in functions should be done by `use`ing their module.
However, the idiomatic way to bring in enums and structs is to `use` them.

```rust
use std::collections::HashMap;

fn main() {
    let mut map = HashMap::new();
    map.insert(1, 2);
}
```

This won't work when we have two items with the same name. In this case the keyword `as` can be used.
```rust

use std::fmt::Result;
use std::io::Result as IoResult;

fn function1() -> Result {
    // --snip--
}

fn function2() -> IoResult<()> {
    // --snip--
}
```

#### Re-exporting Names with `pub use`

The name brought into the scope by `use` is private, we can make it public for others to use by adding the
`pub` keyword before.

```rust
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
}

pub use crate::front_of_house::hosting;

pub fn eat_at_restaurant() {
    hosting::add_to_waitlist();
    hosting::add_to_waitlist();
    hosting::add_to_waitlist();
}
```

>By using pub use, external code can now call the add_to_waitlist function using hosting::add_to_waitlist. If we hadn’t specified pub use, the eat_at_restaurant function could call hosting::add_to_waitlist in its scope, but external code couldn’t take advantage of this new path.

>Re-exporting is useful when the internal structure of your code is different from how programmers calling your code would think about the domain. For example, in this restaurant metaphor, the people running the restaurant think about “front of house” and “back of house.” But customers visiting a restaurant probably won’t think about the parts of the restaurant in those terms. With pub use, we can write our code with one structure but expose a different structure. Doing so makes our library well organized for programmers working on the library and programmers calling the library.
To be fair, I don't fully understand this, hopefully this will become clearer over time.

#### Using External Packages

To use `rand` in our project, we added this line to `Cargo.toml`:
```rust
[dependencies]
rand = "0.5.5"
```

Cargo will download the package with its dependencies from crates.io.
`use rand::Rng;` is how we would bring it to our usage:

```rust
use rand::Rng;

fn main() {
    let secret_number = rand::thread_rng().gen_range(1, 101);
}
```

#### Using Nested Paths to Clean Up Large use Lists

We wrap `use` the same package or module into a single a line to shorten long `use` lists:
```rust
// --snip--
use std::cmp::Ordering;
use std::io;
// --snip--
```

```rust
// --snip--
use std::{cmp::Ordering, io};
// --snip--
```

#### The Glob Operator

We can bring in all public items by using `*`

```rust
use std::collections::*
```

NOT RECOMMENDED (as in any other language).
It's usually useful for tests testing eveyrthing in a module.

### Separating Modules into Different Files

Instead of cramming modules into single file, we can separate them into different files.

Following-up on the previous example we can move `front_of_house` into `src/front_of_house.rs`
and in `src/lib.rs` use it like this:
```rust
mod front_of_house;

pub use crate::front_of_house::hosting;

pub fn eat_at_restaurant() {
    hosting::add_to_waitlist();
    hosting::add_to_waitlist();
    hosting::add_to_waitlist();
}
```

Using a semi-colon instead of a block will load the contents from another file with the same name.


## Collections

3 commonly used collections in Rust are:
* *vector* - store variable number of values next to each other in the memory
* *string* - a collection of characters
* *hash map* - associate a values with keys

### Vectors

To create a new vector
```rust
    let v: Vec<i32> = Vec::new();
```

The type has to be specified because there are no elements added, so Rust cannot infer the type automatically.
Usually the values will be known so we can use the macro `vec!`:
```rust
    let v = vec![1, 2, 3];
```

`v` is effectively a `Vec<i32>`.

Updating a vector:
```rust
let mut v = Vec::new();

v.push(5);
v.push(6);
```

Since we insert values that are `i32`, Rust is able to infer `v`'s type.

Obvious:
```rust
{
    let v = vec![1, 2, 3, 4];

    // do stuff with v
} // <- v goes out of scope and is freed here
```

To read the elements of a vector:
```rust
let v = vec![1, 2, 3, 4, 5];

let third: &i32 = &v[2];
println!("The third element is {}", third);

match v.get(2) {
    Some(third) => println!("The third element is {}", third),
    None => println!("There is no third element."),
}
```

So essentially we have 2 ways:
1. Access the index `&v[2]`;
2. Use `.get`, this will return an `Option<&T>` which we can `match` for `None` and `Some`.

Using option 1 will panic and crash if we try to access an index that does not exist. So if we cannot know for certain an index exists
it is best to use `.get` which will let us handle the situation gracefully.

```rust
let mut v = vec![1, 2, 3, 4, 5];

let first = &v[0];

v.push(6);

println!("The first element is: {}", first);
```

This will fail with an error. Because `let first = &v[0]` performs an *immutable* borrow, `v.push(6)` performs a *mutable* borrow
and finally we have another *immutable borrow* with `println!`.
Note: this would have worked without `println!` because the borrow checker is able to recognize that no borrows occur later.

But why does `.push` make a *mutable borrow*, that is because a push can trigger a reallocation of the vector if we reached
the threshold (again, similar to an ArrayList and other collections in Java), so this call can mutate the vector and move it
to a different location in the memory.

#### Iterating

This is a simple iteration, it uses an *immutable borrow*

```rust
let v = vec![100, 32, 57];
for i in &v {
    println!("{}", i);
}
```

If we want to perform mutations on `v` we'd do:
```rust
let mut v = vec![100, 32, 57];
for i in &mut v {
    *i += 50;
}
```

Since `i` is a reference to the location of `v[i]` in the memory, we need to *dereference* it with `*` to follow the pointer to
the location and mutate the value. Will be covered later as well.

#### Using an Enum to Store Multiple Types

Generally vectors will contain data of the same type since Rust has to know how much memory to allocate, this is also error-prone
as having different types in a vector will not allow to safely iterate and perform operation, as not all operation are necessarily
valid for each type the vector will store.

But, we have a way around it with enums!
```rust
enum SpreadsheetCell {
    Int(i32),
    Float(f64),
    Text(String),
}

let row = vec![
    SpreadsheetCell::Int(3),
    SpreadsheetCell::Text(String::from("blue")),
    SpreadsheetCell::Float(10.12),
];
```

This allows us store multiple types and have `match` to ensure operation safety, as match is exhaustive. There are cases where
enums won't help, because we don't know ahead of time the entire list of types we need. Traits will help with that and will be
covered later.

From the docs:
```
    You want to collect items up to be processed or sent elsewhere later, and don't care about any properties of the actual values being stored.
    You want a sequence of elements in a particular order, and will only be appending to (or near) the end.
    You want a stack.
    You want a resizable array.
    You want a heap-allocated array.
```

```rust
let mut stack = Vec::new();

stack.push(1);
stack.push(2);
stack.push(3);

while let Some(top) = stack.pop() {
    // Prints 3, 2, 1
    println!("{}", top);
}
```

### Strings

TL;DR: Strings are super complicated

The `String` type is a growable, mutable, owned, UTF-8 encoded type.

#### Creating a New String

```rust
let mut s = String::new();
```

Usage:
```rust
let data = "initial contents";

let s = data.to_string();

// the method also works on a literal directly:
let s = "initial contents".to_string();
```

`to_string` is available for any type implementing the `Display` trait.

#### Updating a String

We can grow a `String` with `push_str`:
```rust
let mut s = String::from("foo");
s.push_str("bar"); // s is now foobar
```
`push_str` accepts a `&str`, so it does not take ownership.
```rust
let mut s1 = String::from("foo");
let s2 = "bar";
s1.push_str(s2);
println!("s2 is {}", s2); // works!
```

Appending a single character:
```rust
let mut s = String::from("lo");
s.push('l'); // lol
```

#### Concatenation with the + Operator or the format! Macro

We can concatenate strings with `+`
```rust
let s1 = String::from("Hello, ");
let s2 = String::from("world!");
let s3 = s1 + &s2; // note s1 has been moved here and can no longer be used
```

The `+` operator uses the `add` method, which has signature similar to `fn add(self, s: &str) -> String`. In this case, `self` is `s1`.
It is passed without `&`, so `add` takes ownership of it.
`s2` is passed as `&str`, and not `&String`, this works because of *deref coercion* which will be explained later, but this effectively
turns `&s2` into `&s2[..]`. As a result `s2` will remain valid.
To sum up: `let s3 = s1 + &s2;` takes ownership of `s1` and appends `s2` to it, and then returns the ownership.

But the `+` can be get clunky:
```rust
let s1 = String::from("tic");
let s2 = String::from("tac");
let s3 = String::from("toe");

let s = s1 + "-" + &s2 + "-" + &s3;
```
So we have better options like the `format!` macro which return a `String`.
```rust
let s = format!("{}-{}-{}", s1, s2, s3);
```
Ah, much better!

#### Indexing into Strings

If we want to access a specific character in a `String`, we might do something like:
```rust
let s1 = String::from("hello");
let h = s1[0];
```

This, however, will fail.
```
  |
3 |     let h = s1[0];
  |             ^^^^^ `std::string::String` cannot be indexed by `{integer}`
  |
  = help: the trait `std::ops::Index<{integer}>` is not implemented for `std::string::String`
```

Rust does not support indexing on strings.

##### Internal Representation

A `String` is a wrapper over a `Vec<u8>`.
```rust
let hello = String::from("Hola");
```
In this case `len` will be 4.

But in this case (hello in Russian):
```rust
let hello = String::from("Здравствуйте");
```
We might expect `len` to be 12, but it will actually be 24! This is because of how UTF-8 works.
In the Russian example each character takes up 2 bytes, unlike the English example taking up only 1 byte per character.
So trying to access character by their index won't work reliably, since there is no way to tell what to return exactly.
If only a single byte is returned, we would not get a meaningful character.

```
Another point about UTF-8 is that there are actually three relevant ways to look at strings from Rust’s perspective: as bytes, scalar values, and grapheme clusters (the closest thing to what we would call letters).
```

```
If we look at the Hindi word “नमस्ते” written in the Devanagari script, it is stored as a vector of u8 values that looks like this:

[224, 164, 168, 224, 164, 174, 224, 164, 184, 224, 165, 141, 224, 164, 164,
224, 165, 135]
```

```
That’s 18 bytes and is how computers ultimately store this data. If we look at them as Unicode scalar values, which are what Rust’s char type is, those bytes look like this:
['न', 'म', 'स', '्', 'त', 'े']
```

```
There are six char values here, but the fourth and sixth are not letters: they’re diacritics that don’t make sense on their own. Finally, if we look at them as grapheme clusters, we’d get what a person would call the four letters that make up the Hindi word:

["न", "म", "स्", "ते"]
```

Another reason Rust does not allow string indexing is that it is expected to be an O(1) operation, but this guarantee cannot be made,
because in UTF-8 this would require traversing the string ahead of time and check for valid characters.

#### Slicing Strings

This, too, is a problem:
```
let hello = "Здравствуйте";

let s = &hello[0..4];
```
Since each character is two bytes, and `s` is 4, we will get `Зд`. But if we try to do `&hello[0..1]`, which is only 1 byte and does not make
a single valid character:
```
thread 'main' panicked at 'byte index 1 is not a char boundary; it is inside 'З' (bytes 0..2) of `Здравствуйте`',
```
Rust will panic.

Instead we should:

#### Methods for Iterating Over Strings

We can iterate over a string using the `chars` method:
```rust
for c in "नमस्ते".chars() {
    println!("{}", c);
}
```
This will produce 6 values:
```
न
म
स
्
त
े
```
We can also get the raw bytes with `bytes()`:
```rust
for b in "नमस्ते".bytes() {
    println!("{}", b);
}
```

Will produce 18 bytes:
```
224
164
// --snip--
165
135
```

However getting the actual grapheme clusters is complicated and not provided by the standard library.

