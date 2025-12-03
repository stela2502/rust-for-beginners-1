# K-Means Clustering — A Practical and Mathematical Introduction

In this chapter you will learn:

- what k-means clustering actually does
- the mathematical formula behind every step
- how to translate these formulas into Rust functions
- what *each* step in the algorithm corresponds to in code

This chapter prepares you for **implementing k-means from scratch in Rust.**

---

# 1. What is k-means?

k-means is an **unsupervised clustering algorithm**.

You give it:

- a table of numeric data points
- a number `k` (how many clusters you want)

k-means finds:

> k groups of points where distances inside groups are small  
> and distances between groups are large.

Example applications:

- gene expression clustering
- dimensionality reduction preprocessing
- image compression
- pattern detection

---

# 2. The Data Model (what we cluster)

Each row of your `DataTable` is a vector:

\[
x_i = (x_{i1}, x_{i2}, ..., x_{in})
\]

Where:

- `i` = row index
- `n` = number of columns (features)

Your table:

```
DataTable:
  rows  = number of points
  cols  = vector length (dimension)
  data  = flattened matrix
```

---

# 3. Distance Function

k-means is based entirely on **distance**.

We start with Euclidean distance.

For two points

\[
x = (x_1, x_2, ..., x_n) \\
y = (y_1, y_2, ..., y_n)
\]

Distance is:

\[
d(x,y) = \sqrt{
(x_1 - y_1)^2 +
(x_2 - y_2)^2 +
\cdots +
(x_n - y_n)^2
}
\]

---

# Rust function goal

```rust
fn euclidean(a: &[f64], b: &[f64]) -> f64
```

---

# 4. Clusters and Centroids

Each cluster has a **centroid** (center point):

\[
\mu_j = (\mu_{j1}, \mu_{j2}, ..., \mu_{jn})
\]

Centroids are computed as the **mean of all assigned points**:

\[
\mu_{jk} = \frac{1}{|C_j|} \sum_{x \in C_j} x_k
\]

Where:

- $C_j$: points assigned to cluster j
- $|C_j|$: size of the cluster
- $k$: column index

---

# Rust function goal

```rust
fn recompute_centroid(points: &[usize], data: &DataTable) -> Vec<f64>
```

---

# 5. The Algorithm Loop

K-means works by repeatedly improving centroids.

---

## Step 1: Initialize centroids

We randomly select `k` points:

\[
\mu_1, \mu_2, ..., \mu_k
\]

Goal:

```rust
fn init_centroids(data: &DataTable, k: usize) -> Vec<Vec<f64>>
```

---

## Step 2: Assignment

Assign each point to the nearest centroid:

\[
cluster(x_i) = \arg\min_j d(x_i, \mu_j)
\]

Rust function:

```rust
fn assign_clusters(data: &DataTable, centroids: &[Vec<f64>]) -> Vec<usize>
```

---

## Step 3: Update centroids

Recompute cluster centers:

\[
\mu_j = \frac{1}{|C_j|} \sum_{x \in C_j} x
\]

Rust function:

```rust
fn update_centroids(assignments: &[usize], data: &DataTable, k: usize) -> Vec<Vec<f64>>
```

---

## Step 4: Convergence check

Algorithm stops when:

- cluster assignment stops changing
- centroid movement becomes negligible
- max iterations reached

Centroid shift:

\[
\Delta = \sum_j ||\mu_j^{(old)} - \mu_j^{(new)}||
\]

Rust goal:

```rust
fn has_converged(old: &[Vec<f64>], new: &[Vec<f64>], tol: f64) -> bool
```

---

# 6. Full Algorithm (Mathematical Overview)

### Pseudocode

```
choose k centroids randomly
loop:
    assign each point to nearest centroid
    recompute centroids
    if centroids didn't move:
        stop
```

---

# 7. Objective Function (what k-means minimizes)

The objective is **within-cluster variance**:

\[
J = \sum_{j=1}^k \sum_{x \in C_j} ||x - \mu_j||^2
\]

K-means tries to minimize this.

---

# 8. Problems and Limitations

### Sensitive to initialization
Bad seeds → bad clusters

### Requires k upfront
You must choose `k` manually.

### Sensitive to scale
Raw data ≠ normalized data

We will fix that soon.

---

# 9. How this maps to Rust structure

You will implement:

| Step | Rust function |
|------|--------------|
| distance | `euclidean()` |
| cluster assignment | `assign_clusters()` |
| centroid mean | `update_centroids()` |
| checking stop | `has_converged()` |
| full algorithm | `kmeans()` |

---

# 10. Your Learning Goals

After this chapter, you should:

✅ understand what clusters ARE  
✅ know what centroids ARE  
✅ understand distance mathematically  
✅ see each mathematical definition as code  
✅ be ready to implement k-means  
✅ understand why Rust is ideal for this  
✅ know where bugs usually hide  

---

# Next Chapter

We write:

> **Distance functions in Rust and vector math**
