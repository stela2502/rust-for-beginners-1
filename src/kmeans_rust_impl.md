# Implementing k-Means in Rust (Flat Vec Version)

In this chapter we implement **k-means completely** using the `DataTable` from before.
To make the data access less error prone we should implement an `idx(row,col)` function in out data object.
Do you dare to do that without help? The function should simply return row * self.cols + col. Let's do this!


<details>
<summary><b>Spoiler: Show idx() implementation</b></summary>

```rust
impl DataTable {
    fn idx(&self, row: usize, col: usize) -> usize {
        row * self.cols + col
    }

    pub fn get(&self, row: usize, col: usize) -> f64 {
        self.data[self.idx(row, col)]
    }
}
```
</details>
---

## 1. Distance Function

We start with Euclidean distance between rows:

```rust
fn euclidean(table: &DataTable, a: usize, b: usize) -> f64 {
    let mut sum = 0.0;

    for k in 0..table.cols {
        let d = table.get(a, k) - table.get(b, k);
        sum += d * d;
    }

    sum.sqrt()
}
```

---

# 2. Representing Centroids

Centroids are just vectors:

```rust
type Centroid = Vec<f64>;
```

<details>

<summary><b>A short note on `type` and separating data from results</b></summary>

In Rust, the `type` keyword lets you create a **type alias** – a new name for an existing type – so `type Centroid = Vec<f64>;` simply says “this vector of numbers represents a centroid”, without changing how it is stored or how fast it is. We deliberately keep centroids separate from `DataTable`, because `DataTable` is the **raw input data** (rows with names and identity), while centroids are **derived results** of the algorithm that have no row names and form a smaller `k × D` model in the same feature space. Keeping these as different types teaches a clean mental model: your original measurements live in one well-defined structure, and everything computed from them (like centroids and cluster assignments) lives in clearly separate result types. This separation makes your code easier to reason about, easier to extend later, and helps beginners avoid the common mistake of mixing “data” and “model” into the same container just because the numbers happen to have the same dimension.

```rust
// Give meaning to the numeric vector:
type Centroid = Vec<f64>;      // one centroid (a point in feature space)
type Centroids = Vec<Centroid>; // all centroids, i.e. the model
```
</details>
---

# 3. Initialize Centroids

Pick the first `k` rows:

```rust
fn init_centroids(table: &DataTable, k: usize) -> Vec<Centroid> {
    let mut centroids = Vec::new();

    for i in 0..k {
        let mut c = Vec::new();
        for col in 0..table.cols {
            c.push(table.get(i, col));
        }
        centroids.push(c);
    }

    centroids
}
```

---

# 4. Assign Clusters

```rust
fn assign_clusters(table: &DataTable, centroids: &[Centroid]) -> Vec<usize> {
    let mut assignment = vec![0; table.rows];

    for i in 0..table.rows {
        let mut best = 0;
        let mut best_dist = f64::INFINITY;

        for (j, c) in centroids.iter().enumerate() {
            let mut sum = 0.0;

            for k in 0..table.cols {
                let d = table.get(i, k) - c[k];
                sum += d * d;
            }

            let dist = sum.sqrt();

            if dist < best_dist {
                best_dist = dist;
                best = j;
            }
        }

        assignment[i] = best;
    }

    assignment
}
```

---

# 6. Update Centroids

```rust
fn update_centroids(table: &DataTable, assignment: &[usize], k: usize) -> Vec<Centroid> {
    let mut sums = vec![vec![0.0; table.cols]; k];
    let mut counts = vec![0usize; k];

    for i in 0..table.rows {
        let cl = assignment[i];
        counts[cl] += 1;

        for col in 0..table.cols {
            sums[cl][col] += table.get(i, col);
        }
    }

    for j in 0..k {
        if counts[j] > 0 {
            for col in 0..table.cols {
                sums[j][col] /= counts[j] as f64;
            }
        }
    }

    sums
}
```

---

# 7. Convergence Check

```rust
fn converged(old: &[Centroid], new: &[Centroid], tol: f64) -> bool {
    let mut shift = 0.0;

    for j in 0..old.len() {
        for k in 0..old[0].len() {
            let d = old[j][k] - new[j][k];
            shift += d * d;
        }
    }

    shift.sqrt() < tol
}
```

---

# 8. Full Algorithm

```rust
fn kmeans(table: &DataTable, k: usize, max_iter: usize, tol: f64) -> Vec<usize> {
    let mut centroids = init_centroids(table, k);
    let mut assignment = vec![0; table.rows];

    for _ in 0..max_iter {
        let new_assignment = assign_clusters(table, &centroids);
        let new_centroids = update_centroids(table, &new_assignment, k);

        if converged(&centroids, &new_centroids, tol) {
            println!("Converged.");
            return new_assignment;
        }

        centroids = new_centroids;
        assignment = new_assignment;
    }

    assignment
}
```

---

# 9. Hook it into main.rs

```rust
let clusters = kmeans(&table, 3, 100, 1e-4);

for (i, c) in clusters.iter().enumerate() {
    println!("Row {} -> cluster {}", i, c);
}
```

---

# Summary

You now implemented:

✅ Euclidean distance  
✅ assignment  
✅ centroid update  
✅ convergence  
✅ full k-means

---

# Next

k-means++ initialization or scaling.
