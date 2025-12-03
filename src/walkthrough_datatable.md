# Walking Through the DataTable Code (Line by Line)

In this section we walk slowly and carefully through the **entire `DataTable` implementation**.
This is the heart of your program. Understanding this file means you understand how Rust stores data.

---

# 1. Importing filesystem and IO tools

```rust
use std::fs::File;
use std::io::{BufRead, BufReader};
```

These two lines bring in tools from Rust’s standard library:

- `File` → open files from disk
- `BufReader` → read files efficiently, line by line
- `BufRead` → gives us `.lines()` to iterate over file lines

Rust requires imports explicitly — nothing is magically available.

---

# 2. Defining the DataTable structure

```rust
pub struct DataTable {
    pub rows: usize,
    pub cols: usize,
    pub rownames: Vec<String>,
    pub data: Vec<f64>,
}
```

This defines a **data structure**.

We store:

- `rows` → number of rows
- `cols` → number of columns
- `rownames` → first column (e.g. gene names)
- `data` → numeric values stored in one flat vector

Rust does not have *classes* — it has **structs** and `impl` blocks.

This design is intentional:

We store the table as:

```
row-major order (flattened matrix)
```

Indexing is done by:

```
row * cols + col
```

Which makes numeric computation fast later.

---

# 3. Constructor: creating an empty table

```rust
pub fn new() -> Self {
    Self {
        rows: 0,
        cols: 0,
        rownames: Vec::new(),
        data: Vec::new(),
    }
}
```

This function creates a new empty table.

Notes:

- `Self` refers to `DataTable`
- `Vec::new()` creates empty vectors
- We initialize dimensions with `0`

Rust does not allow uninitialized memory. Everything is always valid.

---

# 4. Loading a file

```rust
pub fn read_file(path: &str, sep: char) -> Self {
```

Function parameters:

- `path` → path to file
- `sep` → column separator (e.g. `\t` for TSV, `,` for CSV)

Return value:
```
Self = DataTable
```

---

# 5. Open and wrap the file

```rust
let file = File::open(path).expect("Cannot open file");
let reader = BufReader::new(file);
```

- `File::open()` returns a `Result`
- `.expect()` crashes gracefully with a readable message
- `BufReader` improves performance for large files

---

# 6. Setting up state

```rust
let mut table = DataTable::new();
let mut header = true;
```

We prepare:

- an empty table
- a flag to skip the header row

Rust forces explicit mutability:

`mut` → this value will change.

---

# 7. Reading line by line

```rust
for line in reader.lines() {
    let line = line.expect("Read error");
```

Why `expect()` again?

Because:

```
line is Result<String, Error>
```

Rust never ignores failure.

---

# 8. Skipping header

```rust
if header {
    header = false;
    continue;
}
```

We drop the first line and move on.

---

# 9. Parsing a row

```rust
let mut first = true;
let mut col_count = 0;
```

Flags:

- `first` → first column = row name
- `col_count` → how many numeric columns did we see?

---

# 10. Splitting each cell

```rust
for mut val in line.split(sep) {
```

- `.split(sep)` breaks the line
- `mut val` because we want to overwrite it
- Each `val` is `&str`

---

# 11. First column: row names

```rust
if first {
    table.rownames.push(val.to_string());
    first = false;
}
```

The first field is the row label.

---

# 12. Parsing numeric values

```rust
val = val.trim();

let v = match val.parse::<f64>() {
    Ok(n) => n,
    Err(_) => 0.0,
};
```

Rust will NOT guess.
Rust will NOT fake conversion.
Rust forces correctness.

If parsing fails:
→ we replace with `0.0`.

---

# 13. Store values

```rust
table.data.push(v);
col_count += 1;
```

We append values in row-major order.

---

# 14. Set column count once

```rust
if table.cols == 0 {
    table.cols = col_count;
}
```

We set column count only for the first row.

---

# 15. Increase row count

```rust
table.rows += 1;
```

---

# 16. Return the object

```rust
table
```

Final expression = return value.

No `return` keyword needed.

---

# 17. Random access: get value

```rust
pub fn get(&self, row: usize, col: usize) -> f64 {
    self.data[row * self.cols + col]
}
```

This computes the index into the flat array.

Memory layout:

```
[ row0 | row1 | row2 | ... ]
```

---

# 18. Print the table

```rust
pub fn print(&self) { ... }
```

We print row by row using `get()`.

---

# What you just learned

✅ struct and impl  
✅ ownership and mutability  
✅ error handling  
✅ Result types  
✅ memory layout  
✅ parsing input  
✅ index computation  

---

# Why this matters

Everything advanced depends on this:

- distance computation
- k-means
- PCA
- scaling
- dot products

If you understand this file — you understand the backbone of Rust-based numeric computing.

---

# Next

Next chapter:
**Distance metrics and vector math in Rust**
