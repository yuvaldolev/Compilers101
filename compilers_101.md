---
theme: default
header: 'Compilers 101 &nbsp; / Yuval Dolev'
paginate: true
---

# **Compilers 101**

---

# A Quick Note

- All code examples are taken from RustyC: my C compiler written in Rust
- Can be found on my GitHub
- Work in progress
- ;)

---

# Introduction to Compilers

---

## What is a Compiler?

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

## Common Passes

1. Lexical Analysis
2. Syntax Analysis (Parsing)
3. Semantic Analysis
4. Code Generation

---

## More-Advanced Passes

- Intermediate representation generation (sometimes there are multiple of these)
- Pre-processing
- Monomorphization

---

## 1. Lexical Analysis

---

### I/O

- Input: Source code
- Output: List of tokens

---

### Details

- Basic understanding of the source code
- Tokens
- Type (kind)
- Location information preservation (span)
- Multi-stage lexing - more on this later

---

### Code

```rust
struct Token {
    kind: TokenKind,
    span: Span,
}
```

---

### Code

```rust
enum TokenKind {
    Number(NumberToken),
    BinaryOperator(BinaryOperatorToken),
    OpenDelimiter(DelimiterToken),
    CloseDelimiter(DelimiterToken),
    Eof,
}
```

---

## 2. Syntax Analysis (Parsing)

---

### I/O

- Input: List of tokens
- Output: Abstract syntax tree (AST)

---

### Details

- More meaningful understanding of **items** in the source code
- ASTs
- Recursive descent
- Operator precedence

---

### Code

```rust
struct Node {
    kind: NodeKind,
    span: Span,
    left: Option<Box<Node>>,
    right: Option<Box<Node>>,
}
```

----

### AST

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

### AST

- Structs / classes
    - Fields
        - Identifier
        - Default value (Depends on the programming language)

----

### AST

- Statics / constants
    - Identifier
    - Expression

---

## 3. Semantic Analysis

---

### I/O

- Input: AST
- Output: AST
- ???

---

### Details

- Deep understanding of code semantics
- Type checking
- Flow control checking
- Ownership validation - more on this later
- And so on...

---

## 4. Code Generation

---

### I/O

- Input: Typed AST (or IR in more-advanced compilers)
- Output: Assembly (older compilers) / Machine code (newer compilers)

---

### Details

- The last pass in the compilation process
- AST iteration: top down
- Code generation: bottom up
- Convert complex items to basic machine-code (or assembly) instructions

---

# Example!

- Let's walk-through how a compiler that compiles math expressions to assembly works
- We'll see the output of each of the passes except for the Semantic Analysis pass

---

## The Expression

- (34 + 5) * 2

---

## 1. Lexical Analysis

---

### Stage 1 - Raw Token Lexing

```rust
RawToken { kind: OpenParenthesis, length: 1 }
RawToken { kind: Number, length: 2 }
RawToken { kind: Whitespace, length: 1 }
RawToken { kind: Plus, length: 1 }
RawToken { kind: Whitespace, length: 1 }
RawToken { kind: Number, length: 1 }
RawToken { kind: CloseParenthesis, length: 1 }
RawToken { kind: Whitespace, length: 1 }
RawToken { kind: Star, length: 1 }
RawToken { kind: Whitespace, length: 1 }
RawToken { kind: Number, length: 1 }
RawToken { kind: Eof, length: 0 }
```

---

### Stage 2 - Lexing

```rust
Token { kind: OpenDelimiter(Parenthesis), span: Span { low: 0, high: 1 } }
Token { kind: Number(NumberToken { value: 34 }), span: Span { low: 1, high: 3 } }
Token { kind: BinaryOperator(Plus), span: Span { low: 4, high: 5 } }
Token { kind: Number(NumberToken { value: 5 }), span: Span { low: 6, high: 7 } }
Token { kind: CloseDelimiter(Parenthesis), span: Span { low: 7, high: 8 } }
Token { kind: BinaryOperator(Star), span: Span { low: 9, high: 10 } }
Token { kind: Number(NumberToken { value: 2 }), span: Span { low: 11, high: 12 } }
```
---

## 2. Syntax Analysis (Parsing)

---

```rust
Node {
  kind: Multiply,
  span: Span { low: 0, high: 12 },
  left: Node {
    kind: Add,
    span: Span { low: 1, high: 7 },
    left: Node { kind: Number(NumberNode { value: 34 }), span: Span { low: 1, high: 3 } },
    right: Node { kind: Number(NumberNode { value: 5 }), span: Span { low: 6, high: 7 } },
  },
  right: Node { kind: Number(NumberNode { value: 2 }), span: Span { low: 11, high: 12 } },
}
```

---

## 3. Code Generation

---

```assembly
.text

.global _main
_main:
  mov x0, #2
  stp x0, xzr, [sp, #-0x10]!
  mov x0, #5
  stp x0, xzr, [sp, #-0x10]!
  mov x0, #34
  ldp x1, xzr, [sp], #0x10
  add x0, x0, x1
  ldp x1, xzr, [sp], #0x10
  mul x0, x0, x1
  ret
```

---

# The Rust Compiler

---

## A Quick Warning

- Basic knowledge of Rust is assumed from this point on

---

## The Rust Programming Language

- A statically compiled language in a similar role as C++
- Supports many platforms and architectures
- Used for a wide range of devices

---

## Unique Benefits of Rust

- Compile time memory safety
- No undefined runtime behavior
- Modern language features

--- 

## rustc - The Rust Compiler

- Self-hosted
- Uses a unique design that allows it to support the unique characteristics of Rust benefits
- Special in 2 ways (quoted from the rustc dev guide)
    - Does things to your code that other compilers don't do (e.g. borrow checking)
    - Has a lot of unconventional implementation choices (e.g. queries)

---

## Bootstrapping

---

### Overview

- How do self-hosted compilers work?
- They use a process named bootstrapping
- Bootstrapping is the process of using a compiler to compile itself

---

## Stages of bootstrapping

- Stage 0: the pre-compiled compiler
- Stage 1: from current code, by an earlier compiler
- Stage 2: the truly current compiler
- Stage 3: the same-result test

---

## Passes

---

### Overview  

1. Invocation
2. Lexing and parsing
3. HIR lowering
4. MIR lowering
5. Code Generation

---

### What About Semantic Analysis???

- Performed throughout all passes (more specifically during IR lowering)!

---

## 1. Invocation

- crate: rustc_driver

---

### Overview

- rustc is invoked on a Rust source program
- The work that the compiler needs to perform is defined by command-line options
- The rustc executable call may be indirect through the use of cargo

---

### Command Line Options

- Enable nightly-only features (-Z flags)
- Perform check-only builds
- Emit LLVM-IR rather than executable machine code

---

## 2. Lexing and parsing

- crates:
    - rustc_lexer
    - rustc_parse

---

### Low-level lexing

- Raw Rust source text is analyzed by a low-level lexer
- Source text is turned into a stream of atomic source code units known as tokens
- Support for Unicode character encoding

---

### High-level lexing

- The token stream passes through a higher-level lexer
- Prepares for the next stage of the compile process 

---

### Parsing

- Translates the token stream from the lexer into an Abstract Syntax Tree (AST)
- Uses a recursive descent (top-down) approach to syntax analysis

---

## 3. HIR lowering

- crate: rustc_hir

---

### Overview

- Converts the AST to High-Level Intermediate Representation (HIR)
- More compiler-friendly representation of the AST
- This process is called "lowering"
- Involves a lot of desugaring of things like loops and async fn

---

### Type Inference

- The HIR is used to perform Type Inference 
- The process of automatic detection of the type of an expression

---

### Trait Solving

- The process of pairing up an impl with each reference to a trait

---

### Type Checking

- Process of converting the types found in the HIR (hir::Ty)
- Converted into the internal representation used by the compiler (Ty<'tcx>)
- Used to verify type safety and correctness of types used in the program

---

## 4. MIR lowering

- crates
    - rustc_middle
    - rustc_borrowck

---

### Overview

- HIR is lowered to Mid-level Intermediate Representation (MIR)

---

### Borrow Checking

- The borrow check is Rust's "secret sauce"
- It is tasked with enforcing a number of properties:
    - That all variables are initialized before they are used
    - That you can't move the same value twice
    - That you can't move a value while it is borrowed
    - That you can't access a place while it is mutably borrowed
    - That you can't mutate a place while it is immutably borrowed
    - etc

---

### Borrow Checking

- The borrow checker operates on the MIR
- Doing borrow checking on MIR has several advantages:
    - The MIR is far less complex than the HIR
    - Using the MIR enables "non-lexical lifetimes" - derived from the control-flow graph

---

### Borrow Checker Phases

1. Creates a local copy of the MIR - modified in the coming steps in-place for optimization
2. Performs a number of dataflow analyses that compute what data is moved and when
3. Performs second type check across the MIR
4. Does a region inference - computes the values of each region
5. Computes the "borrows in scope" at each point
6. Finally, performs a second walk over the MIR - evaluating its actions and reporting errors

---

### Optimizations

- Many optimizations are performed on the MIR
- MIR is very convenient for optimizations because it is still generic
- Improves the code that's later generated

---

### Monomorphization

- Rust's implementation of Generic code
- Makes copies of all the generic code with the type parameters replaced by concrete types
- Done by collecting a list of what concrete types to generate code for - happens at the MIR level

---

## 5. Code Generation

crate: rustc_codegen_llvm

---

### Overview

- Turns higher level representations of source into an executable binary
- rustc uses LLVM for code generation

---

### LLVM - A Very Quick Introduction

- A collection of modular and reusable compiler and toolchain technologies
- In particular, the LLVM project contains a pluggable compiler backend
- Used by many compiler projects, including the clang C compiler and our beloved rustc

---

### LLVM IR

- The first step in the Code Generation pass
- Converts MIR to LLVM Intermediate Representation (LLVM IR)
- It is basically assembly code with additional low-level types and annotations added
- This is where the MIR is actually monomorphized

---

### LLVM Passes

- Optimizations - a lot of them!
- Machine code emission

---

### Linkage

- The different libraries / binaries are then linked together to produce the final binary

---

## Design

---

### Intermediate representations

- As with most compilers, rustc uses some intermediate representations (IRs)
- Used to facilitate computations
- In general, working directly with the source code is extremely inconvenient

---

### IRs Used by rustc

- Token stream
- Abstract Syntax Tree (AST)
- High-level IR (HIR)
- Typed HIR (THIR)
- Middle-level IR (MIR)
- LLVM IR

---

### Queries

- rustc uses a query system which is unlike most textbook compilers
- Compilers are usually which are organized as a series of passes execute sequentially
- rustc does this to make incremental compilation possible
- All the major steps in rustc are organized as a bunch of queries that call each other
- The results of the queries are cached on disk
- This is how incremental compilation works

---

### Queries - Example

- We have the HIR for a function
- We use queries to ask for the LLVM IR for that HIR
- This drives the generation of optimized MIR
- Which drives the borrow checker
- Which which drives the generation of MIR
- And so on
- Each of these steps can be eliminated if its query cache exists on disk

---

### ty::Ty

- Types are really important in Rust
- They form the core of a lot of compiler analyses
- The main type (in the compiler) that represents types (in the user's program) ty::Ty
- rustc uses this to perform all type-related operations during compilation!

---

### Parallelism

- Compiler performance is a problem that worked on by the rustc team
- One aspect of that is parallelizing rustc itself
- Currently, there is only one part of rustc that is parallel by default: codegen
- There are lots of efforts to parallelize the rest of rustc
- This is generally a hard problem

---

# Thank You!
