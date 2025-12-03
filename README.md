# Rust for Bioinformatics — Beginner Tutorial

This repository contains a **hands‑on Rust tutorial** focused on scientists and bioinformaticians who want to learn Rust from the ground up with practical examples.

The tutorial is written using **mdBook** and published as a static website via GitHub Pages.

---

## 📖 Read the Tutorial Online

You can view the rendered HTML version here:

➡️ **https://YOUR_GITHUB_NAME.github.io/YOUR_REPO_NAME**

(Replace with your actual GitHub Pages URL once enabled.)

---

## 📥 Download & Modify Locally

If you want to work with this tutorial locally:

### 1. Clone the repository
```bash
git clone https://github.com/YOUR_GITHUB_NAME/YOUR_REPO_NAME.git
cd YOUR_REPO_NAME
```

---

## 🦀 Install Rust

Rust is required to build and serve the book locally.

### Windows / Linux / macOS
Install Rust using the official installer:

➡️ https://rust-lang.org/tools/install

Then verify:
```bash
rustc --version
cargo --version
```

---

## 📚 Install mdBook

mdBook is the tool used to build and serve the tutorial.

```bash
cargo install mdbook
```

Verify:
```bash
mdbook --version
```

---

## 🚀 Serve the Tutorial Locally

Run this inside the project folder:

```bash
mdbook serve
```

Then open your browser:

```
http://localhost:3000
```

All changes to Markdown files are reloaded automatically.

---

## 🛠 Build Static HTML Output

To generate the static HTML version manually:

```bash
mdbook build
```

The site will be generated into the `book/` directory.

---

## ✍️ Editing the Tutorial

All content lives in:

```
src/
SUMMARY.md   # Table of contents
*.md         # Chapters
```

Add new chapters by:
1. Creating a new `.md` file
2. Registering it in `src/SUMMARY.md`

---

## 🤝 Contributions Welcome

Pull requests are very welcome:

- Fix typos
- Clarify explanations
- Add examples
- Improve structure

---

## 📜 License

This project is released under an open license (add yours here).

---

Happy hacking 🧬🦀
