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

In the memory it will look something like this
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
and since `main()` uses it after function call, we cannot take ownership of it.

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

In the example the method borrows `self` immutably, but it can also get ownership by passing `self` without `&` or borrow mutably with `&mut self`.

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

To access the `UsState` value, we can add another variable
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

So how do we properly handle the two options of `Option<T>`?
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

Using `_` in a `match` expressions allows us to match "all other values" at once:
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
A package is one or more crates that provide a set of functionality. A package contains a `Cargo.toml` file that describes how to build those crates.

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

Everything in Rust modules is private by default. Items in parent modules can't access the child's private items, but it would work vice-versa.
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
This is similar to `..` in the filesystem.

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
It's usually useful for tests testing everything in a module.

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


### Hash Maps

The hash map type is `HashMap<K, V>` (just like Java!).

#### Creating a new hash map

```rust
use std::collections::HashMap;

let mut scores = HashMap::new();

scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Yellow"), 50);
```

Unlike `Vec` `HashMap<K, V>` has no `vec!` equivalent.
All keys must have the same type, as well as the values.

Another way of creating hash maps is `collect` (just like streams in Java!)
```rust
use std::collections::HashMap;

let teams = vec![String::from("Blue"), String::from("Yellow")];
let initial_scores = vec![10, 50];

let mut scores: HashMap<_, _> =
    teams.into_iter().zip(initial_scores.into_iter()).collect();
```
`zip` creates a tuple that pairs each element in each vector with the corresponding element (just like Python).
In this case it will be [("Blue", 10), ("Yellow", 50)]

The type annotation `HashMap<_, _>`, `.collect` can return many different data types, so we have to tell Rust which one we're expecting.
But we can use the `_` placeholder since Rust can infer the key-value types based on the types in the vectors.

#### Hash Maps and Ownership

As usual, types implementing the `Copy` trait (`i32`) will be copied into the hash map, while owned values (`String`) will be moved.

```rust
use std::collections::HashMap;

let field_name = String::from("Favorite color");
let field_value = String::from("Blue");

let mut map = HashMap::new();
map.insert(field_name, field_value); // field_name, field_value are moved and using them later will not compile
```

References will not be moved, but the reference has to be valid as long as the hash map.


#### Accessing Values in a Hash Map

We access map values with `.get()`
```rust
use std::collections::HashMap;

let mut scores = HashMap::new();

scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Yellow"), 50);

let team_name = String::from("Blue");
let score = scores.get(&team_name);
```

`score` will `Some(10)` because `.get()` returns an `Option<&V>`.

We can iterate over map values:
```rust
use std::collections::HashMap;

let mut scores = HashMap::new();

scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Yellow"), 50);

for (key, value) in &scores {
    println!("{}: {}", key, value);
}
// Yellow: 50
```

#### Updating a Hash Map

```rust
use std::collections::HashMap;

let mut scores = HashMap::new();

scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Blue"), 25);

println!("{:?}", scores);
```

Multiple calls to `.insert` will override the previous value (just like `.put` in Java).

If we want to insert only if the value is missing (`putIfAbsent` in Java) we can use `entry` with `or_insert`:
```rust
use std::collections::HashMap;

let mut scores = HashMap::new();
scores.insert(String::from("Blue"), 10);

scores.entry(String::from("Yellow")).or_insert(50);
scores.entry(String::from("Blue")).or_insert(50);

println!("{:?}", scores);
```

`.entry()` returns an `Entry` enum. `.or_insert()` returns a mutable reference to the value if it exists, otherwise inserts it and then
returns the mutable reference to it.

##### Updating a Value Based on the Old Value

To update a value:
```rust
use std::collections::HashMap;

let text = "hello world wonderful world";

let mut map = HashMap::new();

for word in text.split_whitespace() {
    let count = map.entry(word).or_insert(0);
    *count += 1;
}

println!("{:?}", map);
```

As mentioned before, `.or_insert()` returns a mutable reference `&mut V`, by dereferencing we can make changes to the reference returned.


By default `HashMap` uses [SipHash](https://www.131002.net/siphash/siphash.pdf), it's not particularly fast, but it provides good security,
especially against DoS. The used hash function can be customized with the `BuildHasher` trait.


## Error Handling

Rust has two main categories of errors: recoverable and unrecoverable.
For recoverable errors Rust has the `Result<T, E>` type, and for unrecoverable errors, the `panic!` macro.

### panic!

```
When the panic! macro executes, your program will print a failure message, unwind and clean up the stack, and then quit
```

By default panic! unwinds and cleans up the stack which can take some time. It is also possible to abort immediately and leave
the cleaning to the OS by adding a profile in `Cargo.toml`:
```toml
[profile.release]
panic = 'abort'
```

```rust
fn main() {
    panic!("crash and burn");
}
```

The output will be:
```
$ cargo run
   Compiling panic v0.1.0 (file:///projects/panic)
    Finished dev [unoptimized + debuginfo] target(s) in 0.25s
     Running `target/debug/panic`
thread 'main' panicked at 'crash and burn', src/main.rs:2:5
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace.
```

By default the backtrace will not be printed, we can enable it with `RUST_BACKTRACE=1`:
```
RUST_BACKTRACE=1 cargo run
```

### Recoverable Errors with Result

Rust does not have exception, so if we want to choose how to handle errors, we have `Result<T, E>` which looks like this:
```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

`T` represent the returned value type, while `E` represents the error type.

```rust
use std::fs::File;

fn main() {
    let f = File::open("hello.txt");
}
```
Here `open` returns a `Result<T, E>` if we want to check the value we can use `match`:
```rust
use std::fs::File;

fn main() {
    let f = File::open("hello.txt");

    let f = match f {
        Ok(file) => file,
        Err(error) => panic!("Problem opening the file: {:?}", error),
    };
}
```

`Result` is part of the prelude, so there is no need to use `Result::Ok`.

#### Matching on Different Errors

In many cases we would want to handle specific errors differently:
```rust
use std::fs::File;
use std::io::ErrorKind;

fn main() {
    let f = File::open("hello.txt");

    let f = match f {
        Ok(file) => file,
        Err(error) => match error.kind() {
            ErrorKind::NotFound => match File::create("hello.txt") {
                Ok(fc) => fc,
                Err(e) => panic!("Problem creating the file: {:?}", e),
            },
            other_error => {
                panic!("Problem opening the file: {:?}", other_error)
            }
        },
    };
}
```

`io::Error` is the struct returned by `File::open`'s `Err`. The `io::Error` struct has a `kind` method, which returns
the [`ErrorKind`](https://doc.rust-lang.org/std/io/enum.ErrorKind.html) enum.

Using `match` can get quite verbose, luckily `Result<T, E>` has methods accepting a closure:
```rust
use std::fs::File;
use std::io::ErrorKind;

fn main() {
    let f = File::open("hello.txt").unwrap_or_else(|error| {
        if error.kind() == ErrorKind::NotFound {
            File::create("hello.txt").unwrap_or_else(|error| {
                panic!("Problem creating the file: {:?}", error);
            })
        } else {
            panic!("Problem opening the file: {:?}", error);
        }
    });
}
```
`unwrap` generally just tries to grab the `T` of `Result`, if it errors it will panic (`Option<T>` has the same method).
`unwrap_or_else` allows to use a closure to handle the error, this is similar to `Optional#orElse` in Java.

#### Shortcuts for Panic on Error: unwrap and expect

```rust
use std::fs::File;

fn main() {
    let f = File::open("hello.txt").unwrap();
}
```

This is an example of unwrap, if `Result` matches the `Ok` variant, it will return the value `Ok` holds (`T`). Otherwise, it will panic like this
if the file does not exist:
```
thread 'main' panicked at 'called `Result::unwrap()` on an `Err` value: Error {
repr: Os { code: 2, message: "No such file or directory" } }',
src/libcore/result.rs:906:4
```

Another method we can use is `expect`, except it lets us add an error message for the panic:
```rust
use std::fs::File;

fn main() {
    let f = File::open("hello.txt").expect("Failed to open hello.txt");
}
```

```
thread 'main' panicked at 'Failed to open hello.txt: Error { repr: Os { code:
2, message: "No such file or directory" } }', src/libcore/result.rs:906:4
```

#### Propagating Errors

Sometimes we don't want to handle the error in a function, and instead let the caller handle the errors:
```rust
use std::fs::File;
use std::io;
use std::io::Read;

fn read_username_from_file() -> Result<String, io::Error> {
    let f = File::open("hello.txt");

    let mut f = match f {
        Ok(file) => file,
        Err(e) => return Err(e),
    };

    let mut s = String::new();

    match f.read_to_string(&mut s) {
        Ok(_) => Ok(s),
        Err(e) => Err(e),
    }
}
```

However, Rust has a much more concise way of propagating errors this way: The `?` operator.
The same `read_username_from_file` using the `?`:
```rust

use std::fs::File;
use std::io;
use std::io::Read;

fn read_username_from_file() -> Result<String, io::Error> {
    let mut f = File::open("hello.txt")?;
    let mut s = String::new();
    f.read_to_string(&mut s)?;
    Ok(s)
}
```
If `Result` is `Ok`, the underlying value will be returned and the program will continue. If `Result` is `Err`, the function will return it
to the caller immediately.

The difference between `?` and `match` is `?` runs the `from` function on the errors, defined by the `From` trait. Which means the error returned
will be converted to the type of error returned by the function, `io::Error` in the example above.

This can be made even shorter:
```rust

use std::fs::File;
use std::io;
use std::io::Read;

fn read_username_from_file() -> Result<String, io::Error> {
    let mut s = String::new();

    File::open("hello.txt")?.read_to_string(&mut s)?;

    Ok(s)
}
```
We can call functions immediately on `?`.

The above can be written even shorter:
```rust
use std::fs;
use std::io;

fn read_username_from_file() -> Result<String, io::Error> {
    fs::read_to_string("hello.txt")
}
```

The `?` operator can only be used on functions that return `Result<T, E>` or `Option<T>`, or any type that implements `std::ops::Try`.

If we want to return from `main`, for which the return value is unit `()`, we can do:
```rust
use std::error::Error;
use std::fs::File;

fn main() -> Result<(), Box<dyn Error>> {
    let f = File::open("hello.txt")?;

    Ok(())
}
```

This might be useful when calling another crate's `main`.

we can use traits to make input validation more sane:
```rust
pub struct Guess {
    value: i32,
}

impl Guess {
    pub fn new(value: i32) -> Guess {
        if value < 1 || value > 100 {
            panic!("Guess value must be between 1 and 100, got {}.", value);
        }

        Guess { value }
    }

    pub fn value(&self) -> i32 {
        self.value
    }
}
```

This is in reference to the guessing game. Having to check this multiple time can get pretty ugly pretty fast.
Instead using `Guess::new()` would make it more reusable and clear.


## Generic Types, Traits, and Lifetimes

Generics allow us to run the same code on different concrete values (just like in other language, looking at you Java!).
We have already seen many types taking advantage of them: `Option<T>`, `Result<T, E>`, `HashMap<K, V>` and more.

### Generic Data Types

To use generics in a method we can do:
```rust
fn largest<T>(list: &[T]) -> T {
```

It means this two functions:
```rust
fn largest_i32(list: &[i32]) -> i32 {
    let mut largest = list[0];

    for &item in list {
        if item > largest {
            largest = item;
        }
    }

    largest
}

fn largest_char(list: &[char]) -> char {
    let mut largest = list[0];

    for &item in list {
        if item > largest {
            largest = item;
        }
    }

    largest
}

fn main() {
    let number_list = vec![34, 50, 25, 100, 65];

    let result = largest_i32(&number_list);
    println!("The largest number is {}", result);

    let char_list = vec!['y', 'm', 'a', 'q'];

    let result = largest_char(&char_list);
    println!("The largest char is {}", result);
}
```
Could be rewritten with with `fn largest<T>(list: &[T]) -> T {`

It would initially fail with:
```
binary operation `>` cannot be applied to type `T`
```

To allow the usage of `>` we need to ensure `T` implements `std::cmd::PartialOrd`. More on that later.


#### Struct Definitions

We can use generics in struct definitions as well:
```rust
struct Point<T> {
    x: T,
    y: T,
}

fn main() {
    let integer = Point { x: 5, y: 10 };
    let float = Point { x: 1.0, y: 4.0 };
}
```

We can't do stuff like this obviously:
```rust
struct Point<T> {
    x: T,
    y: T,
}

fn main() {
    let wont_work = Point { x: 5, y: 4.0 };
}
```
`x` and `y` must be the same type.

If we want to allow different types we can add in another generic parameter:
```rust
struct Point<T, U> {
    x: T,
    y: U,
}

fn main() {
    let both_integer = Point { x: 5, y: 10 };
    let both_float = Point { x: 1.0, y: 4.0 };
    let integer_and_float = Point { x: 5, y: 4.0 };
}
```

#### In Enum Definitions

We already know of `Option<T>` defined like this:
```rust
enum Option<T> {
    Some(T),
    None,
}
```

#### In Method Definitions

We can use generics in method definitions too using `impl<T>`:
```rust
struct Point<T> {
    x: T,
    y: T,
}

impl<T> Point<T> {
    fn x(&self) -> &T {
        &self.x
    }
}

fn main() {
    let p = Point { x: 5, y: 10 };

    println!("p.x = {}", p.x());
}
```

We can also implement methods only for specific types of `Point<T>`:
```rust
impl Point<f32> {
    fn distance_from_origin(&self) -> f32 {
        (self.x.powi(2) + self.y.powi(2)).sqrt()
    }
}
```

Instances of `Point<T>` where `T` is not `f32`, will not have this method available.

We can also use different generic parameters than what we have in the struct we are creating the method for:
```rust
struct Point<T, U> {
    x: T,
    y: U,
}

impl<T, U> Point<T, U> {
    fn mixup<V, W>(self, other: Point<V, W>) -> Point<T, W> {
        Point {
            x: self.x,
            y: other.y,
        }
    }
}

fn main() {
    let p1 = Point { x: 5, y: 10.4 };
    let p2 = Point { x: "Hello", y: 'c' };

    let p3 = p1.mixup(p2);

    println!("p3.x = {}, p3.y = {}", p3.x, p3.y);
}
```

Here `other` can receive a different type than `self`. And we return a new type where `T` is the struct's and
`W` is `other`'.

The code will print `p3.x = 5, p3.y = c`.

Rust uses *monomorphization*, which means it replaces the generics parameters with the concrete types during
compilation, thus ensuring there will be no runtime penalty for using them.


### Traits: Defining Shared Behavior

Traits are a similar concept to interfaces in Java, but not exactly. They help us define shared behavior between
types and define bounds for generic type. For example, std::cmp::PartialOrd to ensure the generic type we choose
can use the `>` operator.

#### Defining a Trait

To define a trait we use the `trait` keyword:
```rust
pub trait Summary {
    fn summarize(&self) -> String;
}
```

This is similar to the interface definition in Java.

To implement the trait for multiple types:
```rust
pub struct NewsArticle {
    pub headline: String,
    pub location: String,
    pub author: String,
    pub content: String,
}

impl Summary for NewsArticle {
    fn summarize(&self) -> String {
        format!("{}, by {} ({})", self.headline, self.author, self.location)
    }
}

pub struct Tweet {
    pub username: String,
    pub content: String,
    pub reply: bool,
    pub retweet: bool,
}

impl Summary for Tweet {
    fn summarize(&self) -> String {
        format!("{}: {}", self.username, self.content)
    }
}

```

Here we have two type `NewsArticle` and `Tweet`, and each implements the `Summary` trait.
To use `summarize` we simply call like any other function:
```rust
let tweet = Tweet {
    username: String::from("horse_ebooks"),
    content: String::from(
        "of course, as you probably already know, people",
    ),
    reply: false,
    retweet: false,
};

tweet.summarize();
```

To implement an external trait, it is required to first bring it into scope (which requires the trait to be public).
For example `use crate_name::Summary;`

We can only implement a trait for a type if either of them is local to our crate, for example:
internal types like `Vec` and traits like `Display` are not local to use, they are both from std lib.
Otherwise people could break other's implementation by implementing a trait they don't own on a type they don't own,
this is because Rust would not be able to tell which implementation to use.

#### Default Implementations

Just like in Java 8+ interfaces, Rust has default implementations for traits.
Similar to defining a trait we can just add the implementation instead of just specifying the signature:
```rust
pub trait Summary {
    fn summarize(&self) -> String {
        String::from("(Read more...)")
    }
}
```

To use it we specify an empty `impl` block for `NewsArticle`:
```rust
impl Summary for NewsArticle {}
```

But we can easily call `summarize` on `NewsArticle` despite not having an implementation for it. The default one will be called.

Default implementations methods can call non-default methods:
```rust
pub trait Summary {
    fn summarize_author(&self) -> String;

    fn summarize(&self) -> String {
        format!("(Read more from {}...)", self.summarize_author())
    }
```

To use it we can do:
```rust
impl Summary for Tweet {
    fn summarize_author(&self) -> String {
        format!("@{}", self.username)
    }
}
```

However, we cannot call the default implementation from an overriding implementation of the same method.


#### Traits as Parameters

Like interfaces in Java, we can pass traits as parameters to functions:
```rust
pub fn notify(item: impl Summary) {
    println!("Breaking news! {}", item.summarize());
}
```

This lets us call a trait method without relying on a concrete implementation.

#### Trait Bound Syntax

`impl Trait` as parameter is a syntactic sugar for:
```rust
pub fn notify<T: Summary>(item: T) {
    println!("Breaking news! {}", item.summarize());
}
```

We can have two parameters implementing `Summary`:
```rust
pub fn notify(item1: impl Summary, item2: impl Summary) {
```

If we want to force both to be the same one we can do:
```rust
pub fn notify<T: Summary>(item1: T, item2: T) {
```

#### Specifying Multiple Trait Bounds with the + Syntax

We can specify multiple trait bounds if we need a type to implement multiple traits:
```rust
pub fn notify(item: impl Summary + Display) {
```

This would force the passed type to implement both the `Summary` and the `Display` type.
Can also be specified on generic types:
```rust
pub fn notify<T: Summary + Display>(item: T) {
```

#### Clearer Trait Bounds with where Clauses

Trait bounds for functions with many generic parameters can get messy pretty quick:
```rust
fn some_function<T: Display + Clone, U: Clone + Debug>(t: T, u: U) -> i32 {
```

Instead we can use `where`:
```rust
fn some_function<T, U>(t: T, u: U) -> i32
    where T: Display + Clone,
          U: Clone + Debug
{
```
Ah, much better!


#### Returning Types that Implement Traits

We can return an "interface" from functions:
```rust
fn returns_summarizable() -> impl Summary {
```
This would allow to return either `Tweet` or `NewsArticle` from `returns_summarizable`, but not both.
Implementing `returns_summarizable` with code that can return either `Tweet` or `NewsArticle` will fail to compile, but we will see later
how it is possible to write such function.


#### Fixing the largest Function with Trait Bounds

In the previous chapter we had an issue with `largest` function when using a generic instead of implementing it twice for `char` and for `i32`.
The compiler would not let us do that since the generic parameter may not necessarily implement `std::cmp::PartialOrd` which is required to use
the `>` operator. Luckily with bounds we can enforce that only types that implement the trait will be accepted:
```rust
fn largest<T: PartialOrd>(list: &[T]) -> T {
    let mut largest = list[0];

    for &item in list {
        if item > largest {
            largest = item;
        }
    }

    largest
}

```

Rust will still be angry at this:
```
2 |     let mut largest = list[0];
  |                       ^^^^^^^
  |                       |
  |                       cannot move out of here
  |                       move occurs because `list[_]` has type `T`, which does not implement the `Copy` trait
  |                       help: consider borrowing here: `&list[0]`
```

`largest = list[0]` will perform a *move*, this will be a problem for vectors with owned types, stack-allocated types like i32 and char
implement `Copy` and are not moved.

So we can fix it by adding another trait bound:
```rust
fn largest<T: PartialOrd + Copy>(list: &[T]) -> T {
    let mut largest = list[0];

    for &item in list {
        if item > largest {
            largest = item;
        }
    }

    largest
}
```

But we won't be able to use vectors of owned types like `String`, if we want to, we can return a reference to &T instead, to avoid giving `largest`
ownership:
```rust
fn largest<T: PartialOrd>(list: &[T]) -> &T {
    let mut largest = &list[0]; //<-- we borrow &list[0]

    for item in list { // item is now &T and not T
        if item > largest {
            largest = &item;
        }
    }

    &largest // returning a reference
}
```

#### Using Trait Bounds to Conditionally Implement Methods

We can choose to conditionally implement methods based on the concrete type:
```rust

use std::fmt::Display;

struct Pair<T> {
    x: T,
    y: T,
}

impl<T> Pair<T> {
    fn new(x: T, y: T) -> Self {
        Self { x, y }
    }
}

impl<T: Display + PartialOrd> Pair<T> {
    fn cmp_display(&self) {
        if self.x >= self.y {
            println!("The largest member is x = {}", self.x);
        } else {
            println!("The largest member is y = {}", self.y);
        }
    }
}
```
`Pair<T>` always implements `new`, but it implements `cmp_display` only if the concrete type `T` implements the traits
`Display` and `PartialOrd`.

We can conditionally implement for any type that implements a trait:
```rust
impl<T: Display> ToString for T {
    // --snip--
}
```
The standard library implements `ToString` on any type implementing `Display`. This allows us to call `to_string` on any type that can be
"displayed", like integers.


### Validating References with Lifetimes

Just like types are often inferred by the compiler, the lifetimes of references can be inferred as well. But just like with types,
that is not always the case and sometimes we need to help the compiler.


#### Preventing Dangling References with Lifetimes

The main purpose of lifetimes is to prevent dangling pointers
```rust
    {
        let r;

        {
            let x = 5;
            r = &x; // r is assigned a reference to x which gets dropped at the end of this block
        }

        println!("r: {}", r); // here r holds an invalid reference, since it was dropped previously
    }
```
This code would not compile.

How does the compiler knows this code is wrong?

#### The Borrow Checker

The compiler has a *borrow checker*:
```rust
    {
        let r;                // ---------+-- 'a
                              //          |
        {                     //          |
            let x = 5;        // -+-- 'b  |
            r = &x;           //  |       |
        }                     // -+       |
                              //          |
        println!("r: {}", r); //          |
    }                         // ---------+
```
The `'b` lifetime is much shorter than `'a`. We can fix it by doing:
```rust
    {
        let x = 5;            // ----------+-- 'b
                              //           |
        let r = &x;           // --+-- 'a  |
                              //   |       |
        println!("r: {}", r); //   |       |
                              // --+       |
    }                         // ----------+
```
Now `r`'s lifetime `'a` is valid the entire time `x`'s lifetime `'b` is.


# Generic Lifetimes in Functions

Consider the example function:
```rust
fn get_int_ref() -> &i32 {
    let a = 1;
    &a
}
```

This will not compile since `a` goes out of scope, and because `get_int_ref` receives no references, the compiler knows that it is created
inside the function, and as a result we get a dangling pointer to the dropped `a`.

This, for example, will work:
```rust
fn get_int_ref(a: &i32) -> &i32 {
    a
}
```
It works because `a` comes from outside of the function and is valid before the function call due to its wider scope, and as a result
will remain valid after the function completes.

The Rust compiler automatically assigns the lifetime parameters, so the actual function looks like this:
```rust
fn get_int_ref<a'>(a: &'a i32) -> &'a i32 {
```
So we don't have to actually specify the lifetime parameters.

It gets complicated if we have the following function:
```rust
fn get_int_ref(a: &i32, b: &i32) -> &i32 {
    if a > b {
        a
    }

    b
}
```

We would get an error:
```
1 | fn get_int_ref(a: &i32, b: &i32) -> &i32 {
  |                   ----     ----     ^ expected named lifetime parameter
```

Because this is implicitly converted to:
```rust
fn get_int_ref<a', b'>(a: &a' i32, b: &b' i32) -> &i32 {
```

And __either__ references could be return, the compiler cannot do borrow checking, since it doesn't know whether `a'` or `b'` will be returned
in this case and cannot guarantee one will not outlive the other.

To fix this, we tell Rust they have the same lifetime:
```rust
fn get_int_ref<a'>(a: &a' i32, b: &a' i32) -> &a' i32 {
    ...
}
```
This way we can guarantee one reference will not outlive the other and the borrow checker can safely validate.

```rust
fn get_int_ref<a', b'>(a: &a' i32, b: &b' i32) -> &i32 {
    if a > b {
        a
    }

    b
}

fn main() {
    let a: i32;                           // -------------+-- 'a
    let b: i32;                           // -------------+-- 'b
                                          //              |
    println!("{}", get_int_ref(&a, &b));  // ----+-- 'c   |
                                          // -------------+
}

```

#### Lifetime Annotations in Struct Definitions

If we want to hold references in structs we are going to have to use the lifetime annotation:
```rust
struct ImportantExcerpt<'a> {
    part: &'a str,
}

fn main() {
    let novel = String::from("Call me Ishmael. Some years ago...");
    let first_sentence = novel.split('.').next().expect("Could not find a '.'");
    let i = ImportantExcerpt {
        part: first_sentence,
    };
}
```

This means `ImportantExcerpt` has to live as long as `part` lives.

#### The Static Lifetime

There is special lifetime that is called `'static`, what is special about it is that it lives the entire duration of the program.
```rust
let s: &'static str = "I have a static lifetime.";
```
This is usually relevant for constants.

```rust
use std::fmt::Display;

fn longest_with_an_announcement<'a, T>(
    x: &'a str,
    y: &'a str,
    ann: T,
) -> &'a str
where
    T: Display,
{
    println!("Announcement! {}", ann);
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
```
Since lifetimes are a type generic, they are declared in the same angle brackets as `T`.


## Automated Tests

### How to Write Test

```
   1. Set up any needed data or state.
   2. Run the code you want to test.
   3. Assert the results are what you expect.
```

#### The Anatomy of a Test Function

A test in Rust is a function annotated with `#[test]`. To run tests we use `cargo test`.

Running:
```shell
$ cargo new adder --lib
```

It will generate the following test in `lib.rs`:
```rust
#[cfg(test)]
mod tests {
    #[test]
    fn it_works() {
        assert_eq!(2 + 2, 4);
    }
}
```

Rust test can be measured, ignored, filtered. Rust can also test code appearing in the API documentation.

#### Checking Results with the assert! Macro

The `assert!` macro checks whether certain expression evaluates to `true`:
```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    fn can_hold(&self, other: &Rectangle) -> bool {
        self.width > other.width && self.height > other.height
    }
}
```

```rust
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn larger_can_hold_smaller() {
        let larger = Rectangle {
            width: 8,
            height: 7,
        };
        let smaller = Rectangle {
            width: 5,
            height: 1,
        };

        assert!(larger.can_hold(&smaller));
    }
}
```

We have to do `use super::*;` to bring in the parent module into scope.

#### Testing Equality with the assert_eq! and assert_ne! Macros

We don't have to test only boolean values, we can also test equality between objects with the `assert_eq!` and `assert_ne!` macros:
```rust
pub fn add_two(a: i32) -> i32 {
    a + 2
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn it_adds_two() {
        assert_eq!(4, add_two(2));
    }
}
```

`assert_ne!` simply does the opposite and checks whether two objects aren't equal.

#### Adding Custom Failure Messages

We can add custom messages to the assert macros to be displayed when the test fails:
```rust
#[test]
fn greeting_contains_name() {
    let result = greeting("Carol");
    assert!(
        result.contains("Carol"),
        "Greeting did not contain name, value was `{}`",
        result
    );
}
```

It would display the failure this way:
```
---- tests::greeting_contains_name stdout ----
thread 'main' panicked at 'Greeting did not contain name, value was `Hello!`', src/lib.rs:12:9
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace.
```

#### Checking for Panics with should_panic

We can check if code that is supposed to panic under certain condition does indeed panic with #[should_panic]:
```rust
pub struct Guess {
    value: i32,
}

impl Guess {
    pub fn new(value: i32) -> Guess {
        if value < 1 || value > 100 {
            panic!("Guess value must be between 1 and 100, got {}.", value);
        }

        Guess { value }
    }
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    #[should_panic]
    fn greater_than_100() {
        Guess::new(200);
    }
}
```
We can also ensure the failure message is the on we expect with the `expected` attribute:
```rust
// --snip--
impl Guess {
    pub fn new(value: i32) -> Guess {
        if value < 1 {
            panic!(
                "Guess value must be greater than or equal to 1, got {}.",
                value
            );
        } else if value > 100 {
            panic!(
                "Guess value must be less than or equal to 100, got {}.",
                value
            );
        }

        Guess { value }
    }
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    #[should_panic(expected = "Guess value must be less than or equal to 100")]
    fn greater_than_100() {
        Guess::new(200);
    }
}
```

#### Using Result<T, E> in Tests

The tests we wrote until now panicked when they failed, but we can also use `Result<T, E>` instead of panicking:
```rust

#[cfg(test)]
mod tests {
    #[test]
    fn it_works() -> Result<(), String> {
        if 2 + 2 == 4 {
            Ok(())
        } else {
            Err(String::from("two plus two does not equal four"))
        }
    }
}
```
This allows us to use the `?` operator in the body of the test.

### Controlling How Tests Are Run

#### Running Tests in Parallel or Consecutively

By default tests run in parallel by multiple threads.
To make sure tests are run sequentially:
```
$ cargo test -- --test-threads=1
```

#### Showing Function Output

Rust captures all standard output in tests, if we want to see the output:
```
$ cargo test -- --show-output
```

#### Running Specific tests

We can pass `cargo test` the name of the test function to make it run only one test:
```
$ cargo test one_hundred
```
Only one test can be specified this way.

To filter multiple tests we can do:
```
$ cargo test add
```
This will run all tests with `add` in their name.

#### Ignoring Some Tests Unless Specifically Requested

We can ignore specific tests with the `#[ignore]` annotation.

If we want to run ignored tests we can do:
```
$ cargo test -- --ignored
```


### Test Organization

The Rust convention is to put the tests in the same file as the code under tests.
The tests are in a separate module called `tests` by convention and marked as `#[cfg(test)]`
This directive tells Rust not to compile the module during `cargo build`.


#### Testing Private Functions

To test private function we need to bring the code under test into the module: `use super::*;`

#### Integration Tests

Integration tests will be under the `tests/` directory next to `src`. Each file under `tests/`
is compiled is a separate create, so we need to bring each module we test into scope:
```rust
use adder;

#[test]
fn it_adds_two() {
    assert_eq!(4, adder::add_two(2));
}
```

## I/O Project

Get supplied args:
```rust
env::args().collect();
```

Only works with valid UTF-8, for invalid we need to use: `std::env::args_os`

Reading text from file:
```rust
fs::read_to_string(filename)
        .expect("Something went wrong reading the file");
```
`tread_to_string` return `Result<T, E>` and `expect` unwraps it with a custom message for error, returning the `Ok` value.

We define the search function the following way:
```rust
pub fn search<'a>(query: &str, contents: &'a str) -> Vec<&'a str> {
    vec![]
}
```
We have to define a lifetime parameter to tell the compiler which reference we refer to.
In this case the strings within the returned `Vec` will be slices of `contents`, so only `contents` is annotated with `&'a`.

The standard implementation of `search` would be:
```rust
let mut results = Vec::new();

for line in contents.lines() {
    if line.contains(query) {
        results.push(line);
    }
}

results
```

Instead we can take advantage of iterators:
```rust
    content
        .lines()
        .filter(|line| line.contains(query))
        .collect::<Vec<&str>>
```
Ah, so concise!

Reading environment variables:
```rust
use std::env;
env::var("VAR_NAME").is_err() // is_err will be true if the variable is not set
// We can also match on it, as it returns Result<T, E>

match env::var("VAR_NAME") {
    Ok(val) => // ok,
    Err(e) => // handle error
}
```

To print messages to stderr instead of stdout:
```rust
eprintln!("Couldn't parse the arguments {}", err);
```

## Functional Language Features: Iterators and Closures

### Closures

Closures are anonymous functions that can be passed around like regular variables.

#### Creating an Abstraction of Behavior with Closures

We want to write a program that generates a workout based on factors, the workout generation is a relatively heavy computation:
```rust
use std::thread;
use std::time::Duration;

fn simulated_expensive_calculation(intensity: u32) -> u32 {
    println!("calculating slowly...");
    thread::sleep(Duration::from_secs(2));
    intensity
}
```

The main function calling to `generate_workout`:
```rust
fn main() {
    let simulated_user_specified_value = 10;
    let simulated_random_number = 7;

    generate_workout(simulated_user_specified_value, simulated_random_number);
}
```

Now we can call `simulated_expensive_calculation`:
```rust
fn generate_workout(intensity: u32, random_number: u32) {
    if intensity < 25 {
        println!(
            "Today, do {} pushups!",
            simulated_expensive_calculation(intensity)
        );
        println!(
            "Next, do {} situps!",
            simulated_expensive_calculation(intensity)
        );
    } else {
        if random_number == 3 {
            println!("Take a break today! Remember to stay hydrated!");
        } else {
            println!(
                "Today, run for {} minutes!",
                simulated_expensive_calculation(intensity)
            );
        }
    }
}
```

In this case we always use the same intensity, but it doesn't matter.

We also call `simulated_expensive_calculation` multiple times, and that is not good.

#### Refactoring Using Functions

We can it the following way instead:
```rust
fn generate_workout(intensity: u32, random_number: u32) {
    let expensive_result = simulated_expensive_calculation(intensity);

    if intensity < 25 {
        println!("Today, do {} pushups!", expensive_result);
        println!("Next, do {} situps!", expensive_result);
    } else {
        if random_number == 3 {
            println!("Take a break today! Remember to stay hydrated!");
        } else {
            println!("Today, run for {} minutes!", expensive_result);
        }
    }
}
```

But... we call the expensive calculation every time! But we don't need this in every if-else clause.
Instead we can do:

#### Refactoring with Closures to Store Code

```rust
fn generate_workout(intensity: u32, random_number: u32) {
    let expensive_closure = |num| {
        println!("calculating slowly...");
        thread::sleep(Duration::from_secs(2));
        num
    };

    if intensity < 25 {
        println!("Today, do {} pushups!", expensive_closure(intensity));
        println!("Next, do {} situps!", expensive_closure(intensity));
    } else {
        if random_number == 3 {
            println!("Take a break today! Remember to stay hydrated!");
        } else {
            println!(
                "Today, run for {} minutes!",
                expensive_closure(intensity)
            );
        }
    }
}
```
(We again call the expensive computation twice though)
This is similar to Javascript, Python (even `Function<K, V>` in Java).

#### Closure Type Inference and Annotation

Unlike regular function, closures do not need explicit type annotation on parameters and return values, since they
are called within a narrow context and the compiler can infer the types from the calls.

It is, however, possible to annotate the types of a closure:
```rust
let expensive_closure = |num: u32| -> u32 {
        println!("calculating slowly...");
        thread::sleep(Duration::from_secs(2));
        num
    };
```

This is a comparison of function and possible syntax for closures:
```rust
fn  add_one_v1   (x: u32) -> u32 { x + 1 }
let add_one_v2 = |x: u32| -> u32 { x + 1 };
let add_one_v3 = |x|             { x + 1 };
let add_one_v4 = |x|               x + 1  ;
```

However, if we specify the closure using v3/v4 we have to use it, otherwise the code will note compile:
```rust
let expensive_closure = |num| {
    println!("calculating slowly...");
    num
};
println!("{}", 2);
```
Will fail with:
```
  |
4 |     let expensive_closure = |num| {
  |                              ^^^ consider giving this closure parameter a type
```

Since the compiler is unable to infer the type without a calling code.
The compiler will use the first usage to infer the type, so doing the following will fail as well:
```rust
fn main() {
    let expensive_closure = |num| {
        println!("calculating slowly...");
        num
    };

    println!("{}", expensive_closure(2));
    println!("{}", expensive_closure("2"));
}
```
```
error[E0308]: mismatched types
  --> src/main.rs:10:38
   |
10 |     println!("{}", expensive_closure("2"));
   |                                      ^^^ expected integer, found `&str`
```

#### Storing Closures Using Generic Parameters and the Fn Traits

Going back to the workout generation program, we still have the problem of running the expensive closure twice.
What we can do, is hold the closure in a `struct` and lazily evaluate it (or: memoization).

We need to use the `Fn` trait to specify the closure. All closures at least one of: `Fn`, `FnMut`, or `FnOnce`.
So the generic bound in our case would be: `Fn(u32) -> u32`.

Our cache struct will like:
```rust
struct Cacher<T>
where
    T: Fn(u32) -> u32,
{
    calculation: T,
    value: Option<u32>,
}
```

By making `T` bound with `Fn`, we specify it's a closure. So any closure going into the `calculation` field
will need to have the same signature, receive `u32` and return `u32`.

Note: we can also pass regular functions (without capturing) with `Fn` as function implements it too:
```rust
struct Foo<B :Barable> {
        bar: B,
        callback: fn(&B),
    }
```

The `value` field will hold the calculation result. It will be `None` until we called executed `calucaltion`.

To implement the caching we'd do:
```rust
impl<T> Cacher<T>
where
    T: Fn(u32) -> u32,
{
    fn new(calculation: T) -> Cacher<T> {
        Cacher {
            calculation,
            value: None,
        }
    }

    fn value(&mut self, arg: u32) -> u32 {
        match self.value {
            Some(v) => v,
            None => {
                let v = (self.calculation)(arg);
                self.value = Some(v);
                v
            }
        }
    }
}
```

To use our new `Cacher`:
```rust
fn generate_workout(intensity: u32, random_number: u32) {
    let mut expensive_result = Cacher::new(|num| {
        println!("calculating slowly...");
        thread::sleep(Duration::from_secs(2));
        num
    });

    if intensity < 25 {
        println!("Today, do {} pushups!", expensive_result.value(intensity));
        println!("Next, do {} situps!", expensive_result.value(intensity));
    } else {
        if random_number == 3 {
            println!("Take a break today! Remember to stay hydrated!");
        } else {
            println!(
                "Today, run for {} minutes!",
                expensive_result.value(intensity)
            );
        }
    }
}
```

Here, only the first call to `expensive_result.value()` will perform the calculation and store the result in `value`.
However this implementation of `Cacher` is wrong, as it assumes we always use the same argument. Using different arguments
will always return the same result.

So we should use a `HashMap` instead:
```rust
struct Cacher<T>
where
    T: Fn(u32) -> u32,
{
    calculation: T,
    values: HashMap<u32, u32>,
}

impl<T> Cacher<T>
where
    T: Fn(u32) -> u32,
{
    fn new(calculation: T) -> Cacher<T> {
        Cacher {
            calculation,
            values: HashMap::new(),
        }
    }

    fn value(&mut self, arg: u32) -> u32 {
        match self.values.get(&arg) {
            Some(v) => *v,
            None => {
                let val = (self.calculation)(arg);
                self.values.insert(arg, val);
                val
            }
        }
    }
}
```
TODO: Use generics

#### Capturing the Environment with Closures

Unlike functions, closures can access variables from the scope they're defined in.
```rust
fn main() {
    let x = 4;

    let equal_to_x = |z| z == x;

    let y = 4;

    assert!(equal_to_x(y));
}
```
`x` is not passed to `equal_to_x` nor defined inside of it, but we can still access it.

The same with functions won't work:
```rust
fn main() {
    let x = 4;

    fn equal_to_x(z: i32) -> bool {
        z == x
    }

    let y = 4;

    assert!(equal_to_x(y));
}
```

Will fail with:
```
error[E0434]: can't capture dynamic environment in a fn item
 --> src/main.rs:5:14
  |
5 |         z == x
  |              ^
  |
  = help: use the `|| { ... }` closure form instead
```

Accessing the environment from closures has memory overhead which is usually unnecessary when using functions, thus they are not
allowed to this.

Closures access the environment in 3 ways: take ownership, mutable borrow and immutable borrow.
These options are encoded in traits:
* `FnOnce` - takes ownership, "Once" means it cannot take ownership more than once
* `FnMut` - mutable borrow
* `Fn` - immutable borrow
This is based on how the compiler infers the closure body.

All closures implement `FnOnce` because they can be called at least once.

`equal_to_x` is `Fn` because it only needs to read `x`.
We can force ownership by using the `move` keyword:
```rust
let equal_to_x = move |z| z == x;
```
This is useful with threads when we want the thread to take ownership.

### Processing a Series of Items with Iterators

Iterator allows us to perform tasks on items sequentially. Like in most languages they are lazy, meaning they are not used until they are consumed.

In Rust the `iter()` method usually returns us an iterator. This allows us to iterate with `for ... in ...`
```rust
let v1 = vec![1, 2, 3];

let v1_iter = v1.iter();
```

An iterator saves us the need for using annoying indices.

#### The Iterator Trait and the next Method

Iterators implement the `Iterator` trait from the standard library:
```rust
pub trait Iterator {
    type Item;

    fn next(&mut self) -> Option<Self::Item>;

    // methods with default implementations elided
}
```
`type Item` means we need to define an `Item` type to be used by `next`. The `next()` method is the only one needs to be implemented by an iterator.
```rust
#[test]
fn iterator_demonstration() {
    let v1 = vec![1, 2, 3];

    let mut v1_iter = v1.iter();

    assert_eq!(v1_iter.next(), Some(&1));
    assert_eq!(v1_iter.next(), Some(&2));
    assert_eq!(v1_iter.next(), Some(&3));
    assert_eq!(v1_iter.next(), None);
}
```

`v1_iter` has to be mutable as the calls to `next` consume the iterator and thus are changing it.
It is not needed when using `for` loops that take ownership as they do this behind the scenes.

```rust
    let v1 = vec![1, 2, 3];

    let v1_iter = v1.iter();

    for val in v1_iter {
        println!("Got: {}", val);
    }
```
Here the `for` loop take ownership of `val`, but `val` is reference, so it takes ownership of the reference, not the actual value.
Same as writing:
```rust
for val in &v1 {
    ...
}
```

If we want an iterator to take ownership of the vector and return owned values we can use `into_iter`.
If we want to iterate over mutable references we can use `iter_mut`.

#### Methods that Consume the Iterator

The `Iterator` has a number of methods implemented, some of these call `next` which is why we are required to implement it.
The methods calling `next` consume the iterator. One common example is `sum` which calls `next` over and over consuming the entire iterator.

```rust
#[test]
fn iterator_sum() {
    let v1 = vec![1, 2, 3];

    let v1_iter = v1.iter();

    let total: i32 = v1_iter.sum();

    assert_eq!(total, 6);
}
```

`v1_iter` can no longer be used after the call to `sum` as it's been consumed.

#### Methods that Produce Other Iterators

Other methods in `Iterator` change the iterator into different kinds of iterator. They are called *iterator adapters*.
A common example of one is `map` which changes the `Item` into a different one.

```rust
let v1: Vec<i32> = vec![1, 2, 3];

v1.iter().map(|x| x + 1);
```
This calls a closure and produces a new iterator where each value is +1.
This however does nothing, as iterators are lazy and do anything until consumed by `next`.

So to consume the iterator we have to call a method like `sum` or more appropriately `collect`:
```rust
let v1: Vec<i32> = vec![1, 2, 3];

let v2: Vec<_> = v1.iter().map(|x| x + 1).collect();

assert_eq!(v2, vec![2, 3, 4]);
```
This will collect the new iterator produced by `map` into a new vector.

#### Using Closures that Capture Their Environment

`filter` is another `Iterator` method that takes a closure producing `true` or `false`. It will either include or exclude a value from the resulting
iterator based on the result.

```rust
#[derive(PartialEq, Debug)]
struct Shoe {
    size: u32,
    style: String,
}

fn shoes_in_my_size(shoes: Vec<Shoe>, shoe_size: u32) -> Vec<Shoe> {
    shoes.into_iter().filter(|s| s.size == shoe_size).collect()
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn filters_by_size() {
        let shoes = vec![
            Shoe {
                size: 10,
                style: String::from("sneaker"),
            },
            Shoe {
                size: 13,
                style: String::from("sandal"),
            },
            Shoe {
                size: 10,
                style: String::from("boot"),
            },
        ];

        let in_my_size = shoes_in_my_size(shoes, 10);

        assert_eq!(
            in_my_size,
            vec![
                Shoe {
                    size: 10,
                    style: String::from("sneaker")
                },
                Shoe {
                    size: 10,
                    style: String::from("boot")
                },
            ]
        );
    }
}
```

`shoes.into_iter().filter(|s| s.size == shoe_size).collect()` captures `shoe_size` from the environment.
By calling `into_iter` we take ownership of the `shoes` vector, and we return a new vector only with the elements which passed the filter,
the original `shoes` vector is dropped.


#### Creating Our Own Iterators with the Iterator Trait

Let's create a counting iterator which will add increase a value by one on each call:
```rust
struct Counter {
    count: u32,
}

impl Counter {
    fn new() -> Counter {
        Counter { count: 0 }
    }
}

impl Iterator for Counter {
    type Item = u32;
    fn next(&mut self) -> Option<Self::Item> {
        self.count += 1;
        Some(self.count)
    }
}

fn main() {
    let mut c = Counter::new();
    println!("{}", c.next().unwrap());
    println!("{}", c.next().unwrap());
    println!("{}", c.next().unwrap());
}
```
We need to set `Item` to `u32` because this is the type we work with, more on that later.
The problem with the code above is that it will increasing and might reach the max value of `u32`.

We can fix it by changing `next` to:
```rust
fn next(&mut self) -> Option<Self::Item> {
    if self.count < 1000 {
        self.count += 1;
        Some(self.count)
    } else {
        None
    }
}
```

We can also use other methods on our iterator now:
```rust
fn main() {
    let mut c = Counter::new();
    let sum: u32 = Counter::new()
            .map(|a| a * 2)
            .sum();
    println!("{}", sum);
}
```

#### I/O project changes

We will pass `std::env::Args` to `Config::new` instead of passing a vector of `&String` which forces us to use `clone()`.
We also need to change the signature of `Config::new` to: `pub fn new(mut args: env::Args) -> Result<Config, &'static str>`,
`args` needs to be declared `mut` since calling `next()` on the iterator changes it, and the `mut` has to be in the signature
since this is where the "variable" is declared.


## Smart Pointers

Smart Pointers are pointers that have additional metadata and capabilities.
In Rust references are pointers that only borrow data, but Smart Pointers, in many cases, own the data.
Smart Pointers are structs that implement the `Deref` and `Drop` traits.

The common Smart Pointers will be covered:
* `Box<T>` - allocating values on the heap.
* `Rc<T>` - reference counting type that enables multiple owners.
* `Ref<T>` - enforces borrowing rules in runtime instead of compile time.

### Using Box<T> to Point to Data on the Heap

`Box<T>` allows us to store data on the heap rather than on the stack, what remains on the stack is a pointer to the data.
When would we want to use them?

*    When you have a type whose size can’t be known at compile time and you want to use a value of that type in a context that requires an exact size
*    When you have a large amount of data and you want to transfer ownership but ensure the data won’t be copied when you do so
*    When you want to own a value and you care only that it’s a type that implements a particular trait rather than being of a specific type

#### Using a Box<T> to Store Data on the Heap

To store a simple i32 on the heap we'd do:
```rust
fn main() {
    let b = Box::new(5);
    println!("b = {}", b);
}
```
This is a pointless example though, there is no reason for us to store integers on the heap in most cases.

#### Enabling Recursive Types with Boxes

A more useful example is for types we can't tell the size for in compile time, especially recursive types like a linked list.
For example, let's define a cons list (construct list from lisp).
Each item in a cons list contains two elements: the value of the current item and the next item.

We'd try do define it like this:
```rust
enum List {
    Cons(i32, List),
    Nil,
}
```

And use it like this:
```rust
use crate::List::{Cons, Nil};

fn main() {
    let list = Cons(1, Cons(2, Cons(3, Nil)));
}
```
We try to construct the list (1, 2, 3), with the Nil at the end signaling the end of the list.
But Rust is angry:
```
1 | enum List {
  | ^^^^^^^^^ recursive type has infinite size
2 |     Cons(i32, List),
  |               ---- recursive without indirection
  |
  = help: insert indirection (e.g., a `Box`, `Rc`, or `&`) at some point to make `List` representable
```

Rust can't tell how much size to allocate for this type.

#### Computing the Size of a Non-Recursive Type
```rust
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(i32, i32, i32),
}
```
An enum is type where only one type is used at a time, so Rust only needs to allocate as much space as the largest variant takes,
in this case `ChangeColor` uses 3 `i32`.
And unlike in this example, in the cons list, Rust can't tell the size in compile time.

#### Using Box<T> to Get a Recursive Type with a Known Size

By using an "indirection" like the error message suggests, Rust will only store a pointer, rather than the value itself.

```rust
enum List {
    Cons(i32, Box<List>),
    Nil,
}
```

And to use it we'd do:
```rust
use crate::List::{Cons, Nil};

fn main() {
    let list = Cons(1, Box::new(Cons(2, Box::new(Cons(3, Box::new(Nil))))));
}
```

Since Rust knows how much space a pointer needs we no longer have the problem we had, Rust will allocate the size of i32 + the pointer.

`Box<T>` implements the `Deref` trait which allows values of this type to be treated like references.

### Treating Smart Pointers Like Regular References with the `Deref` Trait

Implementing the `Deref` trait allows us to use `*` on Smart Pointers.

#### Following the Pointer to the Value with the Dereference Operator

A regular reference is a type of pointer, we can use `*` to follow the pointer and access the value.
```rust
fn main() {
    let x = 5;
    let y = &x;

    assert_eq!(5, x);
    assert_eq!(5, *y);
}
```

Here `y` holds a reference to `x`, to access the value `y` references, we need to dereference.
Doing `assert_eq!(5, y)` will fail because we compare and `i32` with `&i32`.

#### Using `Box<T>` Like a Reference

We can do the same thing we did with `Box<T>`:
```rust
fn main() {
    let x = 5;
    let y = Box::new(x);

    assert_eq!(5, x);
    assert_eq!(5, *y);
}
```

This works the same because `Box<T>` implements `Deref`.

#### Defining Our Own Smart Pointer

Creating `MyBox<T>`:
```rust
struct MyBox<T>(T); // tuple struct

impl<T> MyBox<T> {
    fn new(x: T) -> MyBox<T> {
        MyBox(x)
    }
}
```

If we try to run the same code with `MyBox`:
```rust
fn main() {
    let x = 5;
    let y = MyBox::new(x);

    assert_eq!(5, x);
    assert_eq!(5, *y);
}
```
It will fail:
```
error[E0614]: type `MyBox<{integer}>` cannot be dereferenced
  --> src/main.rs:14:19
   |
14 |     assert_eq!(5, *y);
   |                   ^^
```
Because `MyBox` does not implement `Deref`.

#### Treating a Type Like a Reference by Implementing the `Deref` Trait

To implement `Deref` we'd do:
```rust
impl<T> Deref for MyBox<T> {
    type Target = T;

    fn deref(&self) -> &T {
        &self.0
    }
}
```

This makes Rust run `*(y.deref())` and since `deref` return a `&` type, `*` works fine.
The reason we `deref` returns a reference and not the value itself is that returning the value would
move it out of `self` and we usually don't want ownership when working with references.

#### Implicit `Deref` Coercions with Functions and Methods

*Deref coercion* is a convenience Rust performs that only works on types that implement `Deref`, it converts a type into a reference to the type.
A common example is `&String` to `&str`, because `String` implements the `Deref` trait by returning `str`. *Deref coercion* happens automatically
when we pass a reference to a type's value that doesn't match parameter type in the function signature.

```rust
fn hello(name: &str) {
    println!("Hello, {}!", name);
}

fn main() {
    let m = MyBox::new(String::from("Rust"));
    hello(&m);
}
```
This works because *Deref coercion* converts `&String` to `&str`.
Rust calls `deref` on `m` which returns a `&String`, `Deref` is already implemented for `String`, so another `deref` is called which returns `&str`.

Without *Deref coercion* it would look like this:
```rust
fn main() {
    let m = MyBox::new(String::from("Rust"));
    hello(&(*m)[..]);
}
```

#### How Deref Coercion Interacts with Mutability

`Deref` overrides `*` behavior for immutable references.
To override the behavior for immutable reference we would implement `DerefMut`.

Rust does deref coercion when it finds types and trait implementations in three cases:

* From `&T` to `&U` when `T: Deref<Target=U>`
* From `&mut T` to `&mut U` when `T: DerefMut<Target=U>`
* From `&mut T` to `&U` when `T: Deref<Target=U>`

The first 2 cases are the same and straightforward, convert immutable to immutable and mutable to mutable.
The first case allows us to convert a mutable reference to an immutable one, but not vice-versa.

