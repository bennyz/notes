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
  struct Person{}
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
