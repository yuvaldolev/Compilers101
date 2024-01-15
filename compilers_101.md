---
theme: default
header: 'Compilers 101 &nbsp; / Yuval Dolev'
paginate: true
---

# **Compilers 101**


---

# A Quick Note

- All code examples are taken from my C compiler written in Rust named RustyC
- Can be found in my GitHub
- Work in progress
- ;)

---

# Introduction to Compilers

---


## What's a Compiler?

- Converts data (typically text) from one form to another
- Most known from programming languages
- Ensures the correctness of the data (transpiler vs. compiler)

---

## Compilers vs. Interpreters

- Compiled languages
- Interpreted languages
- JIT
- Sometimes a bit of both - more on this later

---

# How Do Compilers Work?

---

## Passes

- The different steps through which data traverses during the compilation process

---

### Common Passes

1. Lexical Analysis
2. Syntax Analysis (Parsing)
3. Semantic Analysis
4. Code Generate

---

### More-Advanced Passes

- Intermediate representation generation (sometimes there are multiple of these)
- Pre-processing
- Monomorphization

---

### 1. Lexical Analysis - I/O

- Input: Source code
- Output: List of tokens

---

### 1. Lexical Analysis - Details

- Basic understanding of the source code
- Tokens
- Type (kind)
- Location information preservation (span)

---

### 1. Lexical Analysis - Code

```rust
struct Token {
    kind: TokenKind,
    span: Span,
}
```

---

### 2. Syntax Analysis (Parsing) - I/O

- Input: List of tokens
- Output: Abstract syntax tree (AST)

---

### 2. Syntax Analysis (Parsing) - Details

- More meaningful understanding of **items** in the source code
- ASTs
- Recursive descent
- Operator precedence

---

### 2. Syntax Analysis (Parsing) - Code

```rust
struct Node {
    kind: NodeKind,
    span: Span,
    left: Option<Box<Node>>,
    right: Option<Box<Node>>,
}
```

----

### 2. Syntax Analysis (Parsing) - AST

- Functions
    - Statements
        - Variable declaration
        - Expression
            - Function / method call
            - Condition
            - Loop
            - Literal
            - Binary operation
            - Unary operation

----

### 2. Syntax Analysis (Parsing) - AST

- Structs / classes
    - Fields
        - Identifier
        - Default value (Depends on the programming language)

----

### 2. Syntax Analysis (Parsing) - AST

- Statics / constants
    - Identifier
    - Expression
