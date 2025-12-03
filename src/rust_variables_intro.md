# Variables in Rust (Hands-On and Practical)

Let’s forget theory for a moment and just *touch* Rust.

Here is the simplest valid Rust program:

```rust
fn main() {
    let x = 5;
    let y = 7;
    let z = x * y;

    println!("{x} * {y} = {z}");
}
```

You already learned something important just by reading this:

```
let name = value;
```

That is how **every variable** starts in Rust.

---

## 1. Variables are immutable by default

This might surprise you:

```rust
let x = 5;
x = 10;   // ❌ COMPILER ERROR
```

Rust will not allow this.

Why?

Because **by default, variables are read-only**.

To allow modification, you must say it explicitly:

```rust
let mut x = 5;
x = 10;   // ✅ works
```

This forces you to *think about change* instead of accidentally overwriting data.

---

## 2. Types exist — but Rust usually figures them out for you

Rust is strongly typed, but it does **not** force you to write types everywhere.

You can write:

```rust
let x = 5;
```

Rust magically understands:

> "Oh, that is an integer."

Most of the time, you let the compiler do the work.

But if you want to be precise:

```rust
let x: u8 = 5;
let y: i32 = -7;
let z: usize = 42;
```

### Integer types in a nutshell

| Type | Meaning |
|------|--------|
| `u8` | unsigned 8-bit integer |
| `i32` | signed 32-bit integer |
| `usize` | index-sized integer |
| `isize` | signed index-sized |

---

## 3. Rust does not mix types silently 🚨

This program **does not compile**:

```rust
let x: u8 = 5;
let y: u16 = 7;
let z = x * y;   // ❌
```

Rust refuses.

Because mixing numeric types silently causes bugs.

Explicit conversion:

```rust
let z = (x as u16) * y;
```

---

## 4. Strings are real objects

Two important string types:

### String slice (`&str`)

```rust
let name = "Alice";
```

### Owned `String`

```rust
let name = String::from("Alice");
```

Modify text:

```rust
let mut word = String::from("Hello");
word.push_str(" World");
```

---

## 5. Collections

### Vector

```rust
let numbers = vec![1, 2, 3, 4];
```

Iterate:

```rust
for n in &numbers {
    println!("{n}");
}
```

### HashMap

```rust
use std::collections::HashMap;

let mut ages = HashMap::new();
ages.insert("Alice", 32);
ages.insert("Bob", 27);
```

Lookup:

```rust
println!("{}", ages["Alice"]);
```

---

## 6. Ownership

```rust
let a = String::from("Hello");
let b = a;
println!("{a}"); // ❌
```

Ownership moved.

Duplicate explicitly:

```rust
let b = a.clone();
```

---

## 7. Compiler is your teacher

Example error:

```
error[E0308]: mismatched types
expected u8, found u16
```

Explain it:

```bash
rustc --explain E0308
```

---

# Summary

✅ Variables  
✅ Mutability  
✅ Types  
✅ Strings  
✅ Vectors & Maps  
✅ Ownership  
✅ Helpful compiler

Rust forces correctness early.
