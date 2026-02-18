---
layout: post
title: Relearning OCaml
date: 2026-02-16 21:59 +0530
description: Notes on OCaml
image:
category: [notes, languages]
tags: [ocaml, programming language]
author: textCritique
published: true
sitemap: true
---

I have learned OCaml from the books of John Whitington. I have not programmed in OCaml for a long time. So I may have forgotten many features.
This is another attempt to relearn this fine language from different perspective. I will be following **Sylvain Conchon & Jean-Christophe Filliâtre [2025]**.

## Installation

I have followed installation from official OCaml. And for associated libraries were installed from ocaml installation script. TODO: add link to installation script and official website.

### hello world

```ocaml
let () = print_string "Long time no see OCaml!\n"
```
{:file="hello.ml"}

Following is compiled using *ocamlc*. There is also *ocamlnat* for native executable and *ocaml* interpreter.
 
```console
ocamlc hello.ml
ls
a.out*  hello.cmi  hello.cmo  hello.ml
./a.out 
Long time no see OCaml!  
```

There is also enhanced **REPL** called `utop`. Like interpreter, it can be launched from any terminal and executing `utop` and exited using `ctrl+d`. One of the enhancement of utop is tab suggestion. Tab autocomplete will work only if there is only one suggestion. Otherwise we need navigate using `alt+ <-` and `alt+ ->` and confirm the choice by pressing `alt+ down-arrow-key`.

Other useful command of utop is stashing, `#utop_stash "<filename>"` which will save OCaml commands along with their results in a textfile.

There is dedicated build-tool called `dune` for automating OCaml compilation. It is already installed using above shell script.


## 14 little programs (or First Steps with OCaml)


### Leap Year (1st program)

first version will be taking input interactively:

```ocaml

let yr = read_int ()
let is_leap =
  (yr mod 4 = 0 && yr mod 100 <> 0) || yr mod 400 = 0
let msg = if is_leap then "is" else "is not"
let () = Printf.printf "%d %s leap year\n" yr msg


```
{:file="leap_year.ml"}

```console

> ocamlc leap_year.ml -o leap_year
> ./leap_year 
2026
2026 is not leap year  
```
Things to note:

1. `=` tests for equality if not used with `let`.
2. logical or is `||` and logical and is `&&` like **C** but logical negation is `not`. There are evaluated *lazily* from left to right.
3. `let () = something` is an expresion that evaluates right hand side and checks if it returns nothing , aka `()` unit in OCaml parlance.
4. There is no `main` entry point for program. All the statement is evaluated from top to bottom.

Commandline version:

```ocaml

let yr = int_of_string Sys.argv.(1)
let is_leap =
  (yr mod 4 = 0 && yr mod 100 <> 0) || yr mod 400 = 0
let msg = if is_leap then "is" else "is not"
let () = Printf.printf "%d %s leap year\n" yr msg


```
{:file="leap_year.ml"}

```console

❯❯❯ ./leap_year 2016
2016 is leap year  
```

Note:

1. To use features from the other *module*, we have to write module and variable name separated by dot. Above we have used `Sys.argv` to retrive array of strings `argv` from `Sys` module. If module is not in the standard library then we need to import it.
2. Array element is accessed by index using following systax - `<array>.(<index>)`.
3. `int_of_string` belongs to module `Stdlib`, so need to specify module name in this case.

Usually, when working with toplevel (REPL), inferred type is shown. There is another way to find out:

```console

❯ ocamlc -i leap_year.ml
val yr : int
val is_leap : bool
val msg : string  
```

`int` is the processor native interger. A integer constant can be written in decimal, binary (prefixed `0b`), octal (prefixed `0o`) or hexadecimal (prefixed `0x`). So, decimal constant `15` can be written as `0o17` in octal, `0b1111` in binary and `0xF` in hexadecimal. For clarity, numbers can be separated using `_`.
Example:


```console
utop # 0xff_ff + 99_99;;
- : int = 75534
```

We can specify we want to use `int32` (or `int64`) by suffix `l` (or `L`), such as `42l` and `999_999_999L`.



Character is represented using `char` type. It is written within single quote. Non-printable character is specified using `\` like `\n` and `\\`. We can specify char using their ASCII code in decimal (`\ddd`) and hexadecimal (`\xdd`).
Example:

```console
# 'a';;
- : char = 'a'
# '\n';;
- : char = '\n'
# '\\';;
- : char = '\\'
# '\x7e';;
- : char = '~'
# '\127';;
- : char = '\127'
```

We can find ASCII code or char given code using two functions: `Char.code` and `Char.chr`.

The string is represented by `string` type and is double quoted `""`.
Examples:

```console

# "✖ ✱";;
- : string = "✖ ✱"
# let s = "12345";;
val s : string = "12345"
# s.[3];;
- : char = '4'
# "123\123123";;
- : string = "123{123"
# String.length s;;
- : int = 5
# "1+1="^"5";;
- : string = "1+1=5"
```

### monte-carlo method (2nd program)

We will be approximating value of pi using this method. Idea is to find probability of point to lie inside the quarter circle inscribed within square of unit one, if point is randomly assigned anywhere inside the unit square. We know thoretically it is equal to `pi/4`.
We will get `pi` by multiplying empiracal value by 4.

![Quarter Circle inside Unit Circle](/assets/images/quarter_circle.png)
_Pi approximation using Monte-Carlo method_


```ocaml

let n = int_of_string Sys.argv.(1)

let () =
  let points_inside_circle = ref 0 in
  for _ = 1 to n do
    let x = Random.float 1.0 in
    let y = Random.float 1.0 in
    if x *. x +. y*.y <= 1.0 then
    points_inside_circle := !points_inside_circle + 1
  done;
  let pi = 4.0 *. float !points_inside_circle /. float n in
  Printf.printf "%f\n" pi
```

```console

~/c/o/first-steps ❯❯❯ ./approx_pi 100
3.040000
~/c/o/first-steps ❯❯❯ ./approx_pi 500
3.136000
~/c/o/first-steps ❯❯❯ ./approx_pi 1000
3.140000
~/c/o/first-steps ❯❯❯ ./approx_pi 99999
3.148511
```

Note:
1. There is only one `float` type of 64bit which follows *IEEE 754* standard.
2. All floating point numbers should end with `.` if nothing after decimal, except scientific notation (eg `1e6`).
3. There are corresponding floating point arithemetic operator like `+.`, `-.`, `/.` , `*.` and `**`.
4. We cannot operate on `int` and `float` simultaneously in an expression. We need to convert all numbers to int or float. `truncate` and `float` function can be used to conversion. E.g. :
`42 /. float 3` and `truncate (exp 1.)`


`let-in` defines scope of variables. The variable defined before `in` are visible after it but variable defined after it are only visible after it and when expression ends those variable goes out of the scope.

Example:

```console

─( 20:53:17 )─< command 0 >─────────────────────────────────{ counter: 0 }─
utop # let x = x + 5;;
Error: Unbound value x
─( 20:53:17 )─< command 1 >─────────────────────────────────{ counter: 0 }─
utop # let x = 6 in x*6 ;;
- : int = 36
─( 20:55:56 )─< command 2 >─────────────────────────────────{ counter: 0 }─
utop # let x = 2 in let y = x * 2 in let z = w * y in let w = 2;;
Error: Syntax error
─( 20:56:26 )─< command 3 >─────────────────────────────────{ counter: 0 }─
utop # let x = 2 in let y = x * 2 in let z = w * y in let w = 2 in w * 2;;
Error: Unbound value w
─( 20:57:53 )─< command 4 >─────────────────────────────────{ counter: 0 }─
utop # (let x = 1 in x*2) + x;;
Error: Unbound value x
```

Expression of `for` loop is evaluated only once. This happens before entering loop's body. Example:

```console
utop # for i = (Printf.printf "a"; 4) downto (Printf.printf "e"; 0) do
         Printf.printf "%d" i
       done;;
ae43210- : unit = ()  
```

### drawing a cardioid (3rd program)

The cardiod is a curve that represents the trajectory of a fixed point of circle which is rolling on another circle of same radius.
It is defined by following parameteric equation:

![eq](/assets/images/cardiod_eq.png)
_Cardiod Equation_

where `a` is radii of the circles.


create a dune project:

```console

❯❯❯ dune init project cardioid                  
Entering directory '/home/i3/c/ocaml/first-steps/cardioid'
Success: initialized project component named cardioid
```

create `cardioid.ml` file inside the root of project folder with following content:

```ocaml

open Graphics

let () = open_graph " 300x200"

let radius = 50.
let n = 200

let () =
  moveto 200 150;
  for i = 0 to n do
    let theta = atan 1. *. float i *. 8. /. float n in
    let factor = radius *. (1. -. sin theta) in
    lineto (150 + truncate (factor *. cos theta))
           (150 + truncate (factor *. sin theta))
  done;
  ignore (read_key ())
```

Compile and run from the root folder using `dune exec ./cardioid.exe`.

![Cardioid](/assets/images/cardioid.png)
_Cardioid image_


### the mandelbrot set (4th program)

The Mandelbrot set is defined as the set of points (x, y) for which following sequences do not tend to infinity in absolute value.

![recursive sequences](/assets/images/mandelbrot_eq.png)
_Sequences defining Mandelbrot set_

It is known that sequence tend to infinity as soon as `x_n**2 + y_n**2 > 4`.

```ocaml

open Graphics

let width = 800
let height = 800

(* no of terms to compute of sequence *)
let k = 100

let norm2 x y = x *. x +. y *. y

(* checks if (x, y) points belong to mandelbrot set*)
let mandelbrot a b =
  let rec mandel x y i =
    if i = k || norm2 x y > 4. then i = k
    else
      let x' = x *. x -. y *. y +. a in
      let y' = 2. *. x *. y +. b in
      mandel x' y' (i+1)
  in
  mandel 0. 0. 0

(* checks pixel if it lies in mandelbrot set then colors that pixel black otherwise pixel is left as it is*)
let draw () =
  for w = 0 to width - 1 do
    for h = 0 to height - 1 do
      let a = 4. *. float w /. float width -. 2. in
      let b = 4. *. float h /. float height -. 2. in
      if mandelbrot a b then plot w h
    done
  done

let () =
  let dim = Printf.sprintf " %dx%d" width height in
  open_graph dim;
  draw ();
  ignore (read_key ())

```

Compiling and executing we get

```console
ocamlopt -I `ocamlfind query graphics` graphics.cmxa mandelbrot.ml -o mandelbrot
./mandelbrot
```

![image of mandelbrot set](/assets/images/mandelbrot_out.png)
_Output_

I had similar program( TODO: add link to program) which had following output.

![image of mandelbrot set](/assets/images/mandelbrot_alt.png)
_Visualization of Mandelbrot Set_

Note:
1. `Printf.sprintf` can be used to return formatted string.
2. Function follows similar scoping rules as the variables.
3. Recursive function definition has `rec` before function's name.
4. Mutually recursive function must be defined simultaneously using the keyword `and`. Example:

```ocaml

let rec f x = ... g ...
and     g x = ... f ...

```
