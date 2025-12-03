# Creating a Rust Package and Loading Data (Hands-On)

In this chapter we **build a real Rust package** that loads a numeric table from disk
and stores it in a data structure we can later use for clustering (e.g. k-means).

You will:

✅ create a Rust package  
✅ write a real data table struct  
✅ load a file  
✅ parse numeric values  
✅ handle errors  
✅ test your code  

This chapter is not theory. You will **type this code**.

---

# 1. Create a new package

Open a terminal:

```bash
cargo new data_table --bin
cd data_table
```

Project structure:

```
data_table/
└── src/
    └── main.rs
```

---

# 2. Add dependencies

Edit `Cargo.toml`:

```toml
[package]
name = "data_table"
version = "0.1.0"
edition = "2021"

[dependencies]
clap = { version = "4", features = ["derive"] }
```

---

# 3. Create module structure

Create two new files:

```
src/
  main.rs
  lib.rs
  data.rs
```

---

# 4. Hook the library into Rust

Edit `src/lib.rs`:

```rust
pub mod data;
```

This tells Rust that `data.rs` is part of your package.

---

# 5. Implement the data table

Edit `src/data.rs`:

```rust
use std::fs::File;
use std::io::{BufRead, BufReader};

/// A simple numeric table
pub struct DataTable {
    pub rows: usize,
    pub cols: usize,
    pub rownames: Vec<String>,
    pub data: Vec<f64>,
}

impl DataTable {
    /// Create a new empty table
    pub fn new() -> Self {
        Self {
            rows: 0,
            cols: 0,
            rownames: Vec::new(),
            data: Vec::new(),
        }
    }

    /// Load a TSV or CSV file
    pub fn read_file(path: &str, sep: char) -> Self {
        let file = File::open(path).expect("Cannot open file");
        let reader = BufReader::new(file);

        let mut table = DataTable::new();
        let mut header = true;

        for line in reader.lines() {
            let line = line.expect("Read error");

            // Skip header line
            if header {
                header = false;
                continue;
            }

            let mut first = true;
            let mut col_count = 0;

            for mut val in line.split(sep) {
                if first {
                    table.rownames.push(val.to_string());
                    first = false;
                } else {
                    val = val.trim();
                    let v = match val.parse::<f64>() {
                        Ok(n) => n,
                        Err(_) => 0.0,
                    };
                    table.data.push(v);
                    col_count += 1;
                }
            }

            if table.cols == 0 {
                table.cols = col_count;
            }

            table.rows += 1;
        }

        table
    }

    pub fn get(&self, row: usize, col: usize) -> f64 {
        self.data[row * self.cols + col]
    }

    pub fn print(&self) {
        println!("rows: {}, cols: {}", self.rows, self.cols);

        for r in 0..self.rows {
            print!("{}\t", self.rownames[r]);
            for c in 0..self.cols {
                print!("{}\t", self.get(r, c));
            }
            println!();
        }
    }
}
```

---

# 6. Explain what just happened

## The struct

```rust
pub struct DataTable {
    pub rows: usize,
    pub cols: usize,
    pub rownames: Vec<String>,
    pub data: Vec<f64>,
}
```

This is your first real **data structure**.

We store:

- dimensions
- names
- flattened numeric matrix (`data`)

This layout is efficient and perfect for k-means later.

---

## Parsing numbers safely

```rust
let v = match val.parse::<f64>() {
    Ok(n) => n,
    Err(_) => 0.0,
};
```

Rust refuses to assume input is valid.
We choose to replace invalid values with `0.0`.

---

## Memory layout

Indexing logic:

```rust
row * cols + col
```

This stores the table **row-major** — just like NumPy and C.

---

# 7. Write the executable

Edit `src/main.rs`:

```rust
use clap::Parser;
use data_table::data::DataTable;

#[derive(Parser)]
struct Opts {
    #[arg(short, long)]
    data: String,

    #[arg(short, long, default_value = "\t")]
    sep: String,
}

fn main() {
    let opts = Opts::parse();
    let sep: char = opts.sep.chars().next().unwrap();

    let table = DataTable::read_file(&opts.data, sep);
    table.print();
}
```

---

# 8. Build the program

```bash
cargo build -r
```

---

# 9. Run on a real file

Example `data.tsv`:

```
gene A B C
g1 1.1 2.3 4.5
g2 4.0 0.2 1.8
```

Run:

```bash
target/release/data_table -d data.tsv
```

You should see the table printed.

---

# 10. Tests (you SHOULD test your code)

Add to `src/data.rs`:

```rust
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn read_mock_table() {
        let input = "x\ta\tb\nc1\t1\t2\nc2\t3\t4";
        std::fs::write("/tmp/test.tsv", input).unwrap();

        let t = DataTable::read_file("/tmp/test.tsv", '\t');
        assert_eq!(t.rows, 2);
        assert_eq!(t.cols, 2);
        assert_eq!(t.get(1, 1), 4.0);
    }
}
```

Run:

```bash
cargo test
```

---

# Summary

You now built:

✅ a real package  
✅ a real data structure  
✅ file parsing  
✅ error handling  
✅ indexing logic  
✅ a test suite  

This is the foundation for:

> clustering, PCA, k-means, distance computation

---

# Next

Next chapter:
**Computing distances and centroids**
