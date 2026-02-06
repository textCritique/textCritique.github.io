---
layout: post
title: Getting Rusted
date: 2026-02-03 20:16 +0530
description:
image:
category: [notes, languages, rust]
tags: [rust]
author: textCritique
published: true
sitemap: true
---

Following the instruction from [official guide](https://doc.rust-lang.org/book/ch01-01-installation.html).

## Installation

```bash
$ curl --proto '=https' --tlsv1.2 https://sh.rustup.rs -sSf | sh
```

### check installed version of the compiler

```bash
rustc --version
```

### update and install

```bash
# update to latest version
rustup update
# remove the rust itself
rustup self uninstall
```

### install lsp

for some reason, official install didn't properly install the lsp (i.e. it might be on `$PATH` but do not work), we need to manually install it

```bash
rustup component add rust-analyzer
```
## hello world

```rust
fn main() {
    println!("Hello, world!");
}
```

it can be compiled using `rustc hello.rs`.
By default, rust comes with a formatter and can be used as usual in helix - `:fmt`.

## set up project using cargo

cargo is build-system and package manager too.

To create project using cargo

```bash
cargo new <new_folder>
```
This will create a new folder and toml file with following structure.

```toml

[package]
name = "hello_cargo"
version = "0.1.0"
edition = "2026"

[dependencies]
```
{: file="Cargo.toml"}

Additionally, cargo also creates `.gitignore` file if created outside any git tracked directory. 

If project folder is already created then to initialize a project using do the following:

1. Move the all the source code in a new folder called `src` in the the root directory of the project. For example, if the project folder is `project` then all the source file will be in `project/src`.

2. Execute `cargo init` in the `project` directory.
 What it will do is create a new `Cargo.toml` inside project directory.

## build and run a cargo project

Now project can simply be built using executing `cargo build` inside the project's directory. This will create new folder inside the project's directory called `/target` which contains executable binary along with other stuffs. Moreover, it creates a file called `Cargo.lock` - its purpose is to document dependencies version.

To run, execute `cargo run`. This command can also be used to compile and run in one go.

We have `cargo check` to quickly check if source code can be compiled without compiling it because it is faster than producing the executable.

To compile code with optimization we have build using `cargo build --release`. Compilation is slow but executable is fast.


## External libraies

External libraries is distributed as **crate** (a collection of Rust source files). To use a library crate, add that crate name with version in `Cargo.toml`.

For example,

```toml
[dependencies]
rand = "0.8.5"
```
{: file="Cargo.toml"}

We can now use external library in our project. Commands for building and running is same - `cargo build` and `cargo run`.

It is also updates the `Cargo.lock`.

To use newer version of library, modify versions of the crates in the `Cargo.toml` and execute `cargo update`.

To consult doc of libraries that we are currently using inside our project, run `cargo doc --open` and it will open up browser with local copy of docs which it just downloaded.


## Variable and Mutability

In Rust, varibles are immutable by default. We have special syntax for declaring a mutable varible.

So, trying to modify a mutable varible results in error.
For example:

```rust
fn main() {
    let x = 5;
    println!("The value of x is: {x}");
    x = 3;
    println!("The value of x is: {x}");
}
```

We can declare mutable varible by prefixing variable name with `mut`keyword.
For example:

```rust
fn main() {
    let mut x = 5;
    println!("The value of x is: {x}");
    x = 3;
    println!("The value of x is: {x}");
}
```

## constants

Constants are also immutable and major difference between constant and immutable variable is that constant are declared with type annotation of type and only contain constant expression which can evaluated at compile time.

For example it is decalred as `const SPEED_OF_LIGHT: u32 = 299792458;`.

It has usual scoping rules.

## Shadowing

Variable with the same name can be recreated in the same scope. The previous value of the variable is discarded and new value is used until new variable is created with the same name. This is called shadowing.

For example:

```rust

fn main() {
    let x = 5;
    println!("The value of x is: {x}");
    let x = 3;
    println!("The value of x is: {x}");

    {
        let x = x * 5;
        println!("The value of x is: {x}");
    }

    println!("The value of x is: {x}");
}

```

```
The value of x is: 5
The value of x is: 3
The value of x is: 15
The value of x is: 3
```
{: file = "Output" }

One important thing to note that we are effectively creating a new variable everytime we do shadowing, so we can change type of the variable while reusing the same name. On the other hand if we can change value of the variable but not the type when using mutable variable.

## Data types

In rust, every value is associated with a data type. There are two types of data types: **scalar** and **compound**.

There are four types of scalar:
1. integer
2. float
3. boolean
4. char

### integer

There many variants of ints based on size and signed/unsigned, summarize in the table below.

| Length | Signed | Unsigned |
|--------|--------|----------|
| 8 bit  | `i8`   | `u8`     |
| 16 bit | `i16`  | `u16`    |
| 32 bit | `i32`  | `u32`    |
| 64 bit | `i64`  | `u64`    |
| 128 bit| `i128` | `u128`   |
| Architecture-dependent | isize | usize |

Number literals can be specified in the different bases as summarized in the table below.

| Number literals  | Example |
|------------------|---------|
| Decimal          | 12_345  |
| Hexadecimal      | 0xff    |
| Octal            | 0o17    |
| Binary           | 0b1_000 |
| Byte (u8 only)   | b'A'    |

Things to keep in mind:

1. When rust infers a int, it defaults to i32.
2. If we want specific type, literal can be suffixed with type like so `42u8`.
3. If overflow happens it is handled differntly depending on compilation mode. In **dev mode** , compiler throws an error. In **release mode**, compiler does wrapping (of 2's complement).
4. `_` can be used as visual separator between the digits.
5. Range of n bit signed int is **-(2^(n-1))** to **2^(n-1)-1**.
6. Range of n bit unsigned int is **0** to **2^n -1**.

### float

There are two types of floats: `f32` (32 bit) and `f64`. It is represented according to the IEEE-754 standard.

## numeric operations

The important thing to note that integer division is rounded to nearest integer. Everthing else is as usual. Example:

```rust

fn main() {
    println!("1 + 2 = {}", 1 + 2);
    println!("1 - 2 = {}", 1 - 2);
    println!("5 / 3 = {}", 5 / 3);
    println!("-5 / 3 = {}", (-5) / 3);
    println!("2.3 * 1.7 = {}", 2.3 / 1.7);
    println!("5 % 2 = {}", 5 % 2);
}

```

```

1 + 2 = 3
1 - 2 = -1
5 / 3 = 1
-5 / 3 = -1
2.3 * 1.7 = 1.352941176470588
5 % 2 = 1
```
{: file="Output"}

### boolean

There are two types of boolean: `true` and `false`. Example:

```rust
fn main() {
  let t = true;
  let f: bool = false;
}
```

### char

Rust stores char literals using *unicode* scalar value and is of size 4 bytes (as opposed to 1 byte in C which can only store *ASCII* value). Example:

```rust

fn main() {
    let c = 'z';
    let z: char = 'ℤ'; // with explicit type annotation
    let heart_eyed_cat = '😻';
}
```
Note : A unicode scalar value range from `U+0000` to `U+D7FF` and `U+E000` to `U+10FFFF` inclusive.

### tuple

It is a collection of values of heterogenous data type. It is of fixed size and once declared then it cannot be modified.
We can do patern matching similar to OCaml and Python. Example:

```rust

fn main() {
    let tup: (i32, f64, u8) = (500, 6.4, 1);
    println!("{:?}", tup);

    let t = (1, 42.3, 'A');

    let (x, y, z) = t;
    println!("The value of x = {x}, y = {y}, z = {z}");

    println!("Accessing 1st element of tup = {}", tup.0);
}
 
```

```
(500, 6.4, 1)
The value of x = 1, y = 42.3, z = A
Accessing 1st element of tup = 500  
```
{: file="Output"}

The tuple with no values is called **unit** and written as `()`. Expressions implicitly return this type if they do not return anything.

### array

It is a collection of values of same type and has fixed length.
Example:

```rust

fn main() {
    // simple array initialization
    let a = [1, 2, 3, 4];

    // specifying type and size
    let a: [i32; 5] = [1, 2, 3, 4, 5];

    // specifying array of size 10 with initital value 0
    let a = [0; 10];
}

```

We can access each element like so `a[n]`.


## functions

Rust does not care if function which we are calling inside the `main` is defined after `main` or before it, as long as it is in scope.

This is quite different from **C/C++** where we would have to give function declaration (function prototype) if we want to define function after the `main` and call it in `main`.

Example:

```rust

fn main() {
    println!("Hello, world!");
    some_fun();
}

fn some_fun() {
    println!("Called some_fun()");
}

```

```
Hello, world!
Called some_fun()
```
{: file="Output"}

### parameters

Rust requires type of parameters to be specified. 

Example:

```rust

fn main() {
    some_fun(3);
}

fn some_fun(x: i32) {
    println!("{x} was passed.");
}

```

```
3 was passed.
```
{: file= "Output"}


### statements and expressions

**Statements** are instructions that does something but do not return a value.
**Expressions** are instructions that evaluates to a value. That value is returned.

In Rust, assignment is a statement. So chain assignment is not possible like in C or Python. Something like `let x = (let y = 0)`will result in error.

Examples of expressions include *math expression*, *called function*, *called macro*. A new scope block created with curly brackets is an expression.
Example:

```rust
fn main() {
    let y = {
        let x = 5;
        x * 5
    };

    println!("Value of y: {y}");
}
``` 

```
Value of y: 25
```
{: file= "Output"}


Like OCaml, Rust uses semicolon  `;` to separate statements and expression do not need `;` at the end.
Sufixing expression with `;` turns it into statementi which will return unit type `()`.

### return values of function

When we want to return a value from a function we have annotate it using arrow `->`. Much like OCaml, the last expression in a function body serves as the return value of the function. We could also do `return expression;`.
Example:

```rust

fn main() {
    println!("{}", five());
}

fn five() -> i32 {
    5 // or return 5;
}

``` 

```
5
```
{: file="Output"}
