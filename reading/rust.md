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

*ptr* is a pointer to the address of the first character
*len* is the amount of memory used by *s1*
*cap* is how much it can grow without an extra allocation

When *s1* is assigned to *s2* we get another structure, similar to the one on the left, only for *s2*.
The pointer and the metadata are copied, but no the data pointed by the variable!

Since *drop* is called at the end of the block, Rust will attempt to free s1 and then s2, but since the they
have a pointer to the same location on the heap, it might free the same memory area twice (*double free*)!
This leads to undefined behavior which can cause data corruption and security issues.

Luckily, Rust protects us from such atrocities, so instead of copying the pointer along with the metadata,
Rust will *move* the data from *s1* to *s2*, meaning *s1* is no longer valid

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

Rust has a *Copy* trait the we can implement for types, but it won't let derive it if the type has implmented *Drop*
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

