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

## control flow

The **control flow** is a  language construct that helps to execute the statements selectively and repeatedly.

### if Expressions

In Rust, conditional expression is not enclosed within `()`. Unlike many languages, if we put any other expression instead of conditional expression, compiler(or interpreter) tries to evaluate (or convert) into boolean value. Rust throws error in this case.
Example:

```rust

fn main() {
    let not_zero = 5;

    if not_zero {
        println!("Not a zero.");
    } else {
        println!("Zero");
    }
}
```

```console

> cargo run
   Compiling control-flow v0.1.0 (/home/i3/c/rust/control-flow)
error[E0308]: mismatched types
 --> src/main.rs:4:8
  |
4 |     if not_zero {
  |        ^^^^^^^^ expected `bool`, found integer

For more information about this error, try `rustc --explain E0308`.
error: could not compile `control-flow` (bin "control-flow") due to 1 previous error
```

The block of code associated with each `if` expression is called **arm**.

### multiple conditions with else if

Example:

```rust
fn main() {
    let n = 6;

    print!("{n} is divisible by");

    if n % 4 == 0 {
        println!(" 4");
    } else if n % 3 == 0 {
        println!(" 3");
    } else if n % 2 == 0 {
        println!(" 2");
    } else {
        println!(" 1");
    }
}
```

```
6 is divisible by 3
```
{: file= "Output"}

Here, 6 is divisble by 3 as well as 2, but this whole conditional will only execute first true statement only as in many languages.

### using if in a let statement

As `if` is an expression, we can use it right side of a let statement to assign the outcome to a variable.

Example:

```rust
use std::io;

fn main() {
    let mut num = String::new();

    println!("Enter a number:");

    io::stdin()
        .read_line(&mut num)
        .expect("Could not read the value");

    let num: i32 = match num.trim().parse() {
        Ok(n) => n,
        Err(_) => {
            println!("Input is not a number!");
            return;
        }
    };

    let num = if num % 2 == 0 { "even" } else { "odd" };

    println!("Entered number is {num}.");
}
```

```
Enter a number:
5
Entered number is odd.
```
{: file= "Output"}

Note: The blocks of code demarcated by `{}` evaluates to last expression in them (and last expression does not end with `;`). Also, all the arms of `if` expression should evaluate to same type. Otherwise, compiler will throw an error. Example:

```rust

fn main() {
    let condition = false;

    let value = if condition { 1 } else { "one" };

    println!("value: {}", value);
}
  
```

error[E0308]: `if` and `else` have incompatible types
 --> src/main.rs:4:43
  |
4 |     let value = if condition { 1 } else { "one" };
  |                                -          ^^^^^ expected integer, found `&str`
  |                                |
  |                                expected because of this

For more information about this error, try `rustc --explain E0308`.


```
{: file = "Output"}


## repeating code with loop

It is a forever loop. We can exit this loop (or any variant of loop ) using `break` statement.
Example.

```rust
fn main() {
    loop {
        println!("forever and ever");
    }
}
```

### returning values from loops

We can return values from the loop and use it as expression. Example:

```rust

fn main() {
    let mut counter = 0;
    let mut accumulator = 1;

    let two_raised_ten = loop {
        accumulator *= 2;
        counter += 1;
        if counter == 10 {
            break accumulator;
        }
    };

    println!("Two raised to power ten: {two_raised_ten}");
}

```

```
Two raised to power ten: 1024  
```
{: file= Output}

Note: `return` can also be used to exit the loop but it has  unintended (or intended) effect of exiting the current function.


### name the loops using loop label

If we have nested loop then `break` and `continue` applies to innermost loop only. We can *label* loop so that we can later specify which one we mean we call `break` and `continue`.

Example:

```rust

fn main() {
    let mut row = 1;

    'outer: loop {
        let mut column = row;

        loop {
            if row == 5 {
                break 'outer;
            }
            print!("{column}    ");
            column += row;

            if column == row * 4 {
                break;
            }
        }
        println!();
        row += 1;
    }
}

```


```
1    2    3    
2    4    6    
3    6    9    
4    8    12 
```
{: file= "Output"}


### conditional loop with while

Example:

```rust

fn main() {
    let mut sum = 0;
    let mut c = 1;
    while c <= 100 {
        sum += c;
        c += 1;
    }

    println!("Sum of whole no upto 100: {sum}");
}
```

```
Sum of whole no upto 100: 5050 
```
{: file= Output}

### looping through a Collection with for

In Rust, there is canonical way to iterate through an array (or similar data structure) throught the use `for` loop. It is considered safer and faster alternative to other loop.
Example:

```rust

fn main() {
    let a = [1, 2, 3, 4, 5];

    for element in a {
        println!("{}", element);
    }
}

```


```
1
2
3
4
5 
```
{: file= "Output"}

There is Python equivalent of range function for iteration through the `for` loop.
Example:

```rust

fn main() {
    for i in (1..4).rev() {
        println!("{i}");
    }
    println!("Liftoff!");
}

```


```
3
2
1
Liftoff! 
```
{: file="Output"}


## ownership

The what makes Rust Rust is the idea of **ownership**. It is the set of rules that dictates how Rust program manages the memory.

In other languages like *C*, memory management works as follows. First there are two different areas of memory : **stack** and **heap**. Any data whose size is known and fixed is placed in the stack (data like numeric literals, string literals and booleans come under this category). Other data items, like string buffers, whose size is dynamic and not known at compile time are placed in the heap (by calling `malloc` or similar).

The way stack works is, whenever we assign a variable or pass data (fixed size and small enough so that it is copied) to function, that data is copied in the stack memory.
When variable goes out of scope or the function returns the data is freed from the stack. All the data item is stored consecutively (except the padding) in the stack. It is follows usual *LIFO* pattern.

The data placed in the heap memory are not stored consecutively and it also has other component in the stack that stores the reference (address of the data in the heap). To allocate data on heap, there is extra step (unlike stack) which consist of finding unused space in memory that is not used by other, is of required size and mark it is in current use; instead of just copying the data in the heap. Also data access is also different, as we first have to get the address and then go to that address to get the data.
The data placed in the heap can only be ever freed when manually done (using `free`). Some garbage collected (GC) based languages free heap data when there is no variable which is pointing to heap data. This comes at cost of runtime overhead.

The system languages like *C* suffers problem which results from poor memory management (of heap) which has to be done manually. If we forget to free the memory that we allocated that will lead to wastage. If we free the memory too early, it will result in undefined behavior. If we do it more than once, it will still result in a bug.

The **ownership** helps us to pair one allocation with one free.

### the ownership rules

1. Each value in Rust has an **owner**
2. There can be only be *one* owner at a time.
3. When the owner goes out of scope, the value will be dropped.

### variable scope

Example:

```rust

fn main() {
    {
        let n = 42;
        println!("Inside the new block scope: {n}");
    }
    // here n is goes out of scope
    println!("Outside the block scope: {n}");
}
```

```
For more information about this error, try `rustc --explain E0425`. 
  --> src/main.rs:7:41
  |
7 |     println!("Outside the block scope: {n}");
  |                                         ^
  |
help: the binding `n` is available in a different scope in the same function
 --> src/main.rs:3:13
  |
3 |         let n = 42;
  |             ^

```
{: file="Output"}

### the string type

We will use string type which uses ownership concept. Following string creates a mutable string which lives on heap ( string literal which is immutable).

> Double colon `::` is used here specify the namespace just like **C++**.
{: .prompt-tip}

```rust
fn main() {
    let mut greet = String::from("hello, ");
    greet.push_str("universe");
    println!("{greet}");
}
```

```
hello, universe 
```
{: file="Output"}

Here, `String::from` allocates the memory on the heap. When runtime system encounter `}`, Rust calls `drop` funtion automatically on each heap variable which frees the memory on the heap.

> Similar pattern of resource disallocation exists in **C++** called **Resource Allocation Is Initialization**.
{: .prompt-info}

### variable and data interacting with Move

In case of the variable of simple data type (which has fixed size and small enough), if we try to reassign it to different variable
it will just copy the contents.
Example:

```rust

fn main() {
    let x = 3;
    let y = x;
    println!("{x} and {y}");
}
```

```
3 and 3 
```
{: file="Output"}

But it in the case of variable pointing to data on the heap, when we reassign original variable to another variable, the ownership to new variable and the original variable goes out of scope.
Example:

```rust

fn main() {
    let original = String::from("rust");
    let moved = original;

    println!("moved = {moved} and original = {original}");
}
```

```
 error[E0382]: borrow of moved value: `original`
 --> src/main.rs:5:47
  |
2 |     let original = String::from("rust");
  |         -------- move occurs because `original` has type `String`, which does not implement the `Copy` trait
3 |     let moved = original;
  |                 -------- value moved here
4 |
5 |     println!("moved = {moved} and original = {original}");
  |                                               ^^^^^^^^ value borrowed here after move
  |
  = note: this error originates in the macro `$crate::format_args_nl` which comes from the expansion of the macro `println` (in Nightly builds, run with -Z macro-backtrace for more info)
help: consider cloning the value if the performance cost is acceptable
  |
3 |     let moved = original.clone();
  |                         ++++++++

For more information about this error, try `rustc --explain E0382`. 
```
{: file="Output"}

The compiler output clearly suggest that `original` variable is no longer valid. It has **moved** to `moved` variable. As we mentioned earlier, Rust frees heap variable when block ends by calling `drop` on each heap variable. But we have more than one variable refering to same heap data, then it will result in multiple frees.This borrowing mechanism avoids that.

> In **Python**, if we assign a variable storing heap data (like List) to another variable, it creates a **shallow** copy. So both variable point to same location in heap. In Rust, there is no such concept. What we have is **move** ownership to another. It has concept of **deep copy** though where heap data is **clone**.
{: prompt-tip}


### scope and assignment

Whenever we try to assign new value to variable that is storing heap data initially, the variable will store new value and previous heap data will be deallocated immediately.


### variables and data interacting with clone

If we want do **deep copy** of heap data along with stack data associated with a variable, we can run `clone` method on it.

```rust
fn main() {
    let s = String::from("ciao");
    let s2 = s.clone();

    println!("s = {s} and s2 = {s2}");
}

```
  
```
s = ciao and s2 = ciao
```
{: file="Output"}

### stack-only data: copy

Whenever we use simple data type, data is copied if we assign a variable to another variable. There is no concept of *shallow* and *deep* copy, so no need to clone.

Example:

```rust

fn main() {
    let t = (5, 'a', "some", 4.3);
    let t2 = t;

    println!("t = {:?}", t);
    println!("t2 = {:?}", t2);
}
```

```
t = (5, 'a', "some", 4.3)
t2 = (5, 'a', "some", 4.3) 
```
{: file="Output"}

This kind of behavior specifically exhibited by types that implements **trait called `Copy`**. Here are common types that implements `Copy`:

- All the integer types.
- All floating types.
- The Boolean type.
- The character type.
- Tuples that consist of type that implement `Copy`.
