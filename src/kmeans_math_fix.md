# K-Means Clustering — Mathematical Introduction (mdBook Compatible)

This version uses **mdBook-compatible math formatting** (single `$` and `$$` blocks).

---

# 1. What is k-means?

k-means is an **unsupervised clustering algorithm**.

You provide:
- a numeric data matrix
- a number `k` (number of clusters)

The algorithm finds:

> Clusters where distances within clusters are minimal and distances between clusters are maximal.

---

# 2. Data Representation

Each row in the data table is a vector:

$$
x_i = (x_{i1}, x_{i2}, ..., x_{in})
$$

Where:
- $i$ = data point index
- $n$ = number of columns (features)

---

# 3. Distance Function (Euclidean)

For two vectors:

$$
x = (x_1, x_2, ..., x_n)
$$

$$
y = (y_1, y_2, ..., y_n)
$$

Distance:

$$
d(x,y) = \sqrt{\sum_{k=1}^{n} (x_k - y_k)^2}
$$

Rust goal:

```rust
fn euclidean(a: &[f64], b: &[f64]) -> f64
```

---

# 4. Clusters and Centroids

Each cluster has a centroid:

$$
\mu_j = (\mu_{j1}, \mu_{j2}, ..., \mu_{jn})
$$

Centroid definition:

$$
\mu_{jk} = \frac{1}{|C_j|} \sum_{x \in C_j} x_k
$$

Where:

- $C_j$: points assigned to cluster j  
- $|C_j|$: size of the cluster  
- $k$: column index

Rust goal:

```rust
fn recompute_centroid(points: &[usize], data: &DataTable) -> Vec<f64>
```

---

# 5. Algorithm Steps (Mathematical Form)

## Step 1: Initialize Centroids

Choose $k$ initial points:

$$
\mu_1, \mu_2, ..., \mu_k
$$

Rust goal:

```rust
fn init_centroids(data: &DataTable, k: usize) -> Vec<Vec<f64>>
```

---

## Step 2: Assign Points

Each point is assigned to nearest centroid:

$$
cluster(x_i) = \arg\min_j d(x_i, \mu_j)
$$

Rust goal:

```rust
fn assign_clusters(data: &DataTable, centroids: &[Vec<f64>]) -> Vec<usize>
```

---

## Step 3: Update Centroids

$$
\mu_j = \frac{1}{|C_j|} \sum_{x \in C_j} x
$$

Rust goal:

```rust
fn update_centroids(assignments: &[usize], data: &DataTable, k: usize) -> Vec<Vec<f64>>
```

---

## Step 4: Convergence

Stop when centroids no longer shift significantly.

Centroid movement:

$$
\Delta = \sum_{j=1}^{k} || \mu_j^{(old)} - \mu_j^{(new)} ||
$$

Rust goal:

```rust
fn has_converged(old: &[Vec<f64>], new: &[Vec<f64>], tol: f64) -> bool
```

---

# 6. Objective Function

K-means minimizes **within-cluster sum of squares**:

$$
J = \sum_{j=1}^{k} \sum_{x \in C_j} || x - \mu_j ||^2
$$

---

# 7. Pseudocode

```
initialize centroids
repeat:
    assign points
    recompute centroids
until converged
```

---

# 8. Common Problems

- Bad initialization
- Wrong number of clusters
- Different feature scales

Fixes:
- k-means++ (later)
- normalization
- silhouette score

---

# 9. Mapping mathematics to Rust

| Math | Rust |
|------|------|
| $d(x,y)$ | `euclidean()` |
| $\arg\min$ | `assign_clusters()` |
| mean | `update_centroids()` |
| movement | `has_converged()` |
| algorithm | `kmeans()` |

---

# Next Chapter

Implement:

> Distance functions in Rust
