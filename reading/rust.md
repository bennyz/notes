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

  // short initializtion
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

  // This creats a *String* from a string literal, it is *mutable*, allocated on the heap, and *owned*
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

Since *drop* is called at the end of the block, Rust will attempt to free s1 and then s2, but since the they
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

Rust has a `Copy` trait the we can implement for types, but it won't let derive it if the type has implmented `Drop`
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

`x` however is stack allocated, so a copy is performed and it is still acessible after
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

