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
```

```rust
  // inferred array
  let a = [1, 2, 3, 4, 5];

  // explicitly typed
  let a: [i32; 5] = [1, 2, 3, 4, 5];

  // short initializtion
  let a = [3; 5]; // [3, 3, 3, 3, 3]
```
