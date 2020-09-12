## A minimal Rust kernel

The VGA text buffer is located at address `0xb8000`. It is accessible via memory-mapped I/O (MMIO), By reading and writing to this address, we don't access RAM, but the VGA hardware itself.

```rust
static HELLO: &[u8] = b"Hello World!";

#[no_mangle]
pub extern "C" fn _start() -> ! {
    let vga_buffer = 0xb8000 as *mut u8; // Convert the address into a raw pointer

    for (i, &byte) in HELLO.iter().enumerate() {
        unsafe {
            *vga_buffer.offset(i as isize * 2) = byte; // Access memory at the relevant offset and place the byte representing the character
            *vga_buffer.offset(i as isize * 2 + 1) = 0xb; // The second byte represents the color (cyan in this case)
        }
    }

    loop {}
}
```
## VGA Text Buffer

### Volatile
Because we use MMIO to access the buffer, we only write to it without reading. This can make Rust optimize and omit the writes since it would think they are unnecessary. To avoid this optmization we can use volatile (similar to how the it works in Java).

In Rust we can use the [volatile](https://docs.rs/volatile/0.3.0/volatile/) package.

### Spinlocks

We cannot use the stdlib mutex since we don't have support for locking in our kernel. Instead we can use spinlocks which just try to lock over and over but don't need kernel support. This can be achieved using the `spin` crate.
