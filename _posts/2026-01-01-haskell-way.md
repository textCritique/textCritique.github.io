---
layout: post
title: Haskell Way
date: 2026-01-01 16:27 +0530
description:
image:
category: [notes, languages, haskell]
tags: [haskell, functional programming]
author: textCritique
published: true
sitemap: true
---

## setting up on dev environment

```bash
curl --proto '=https' --tlsv1.2 -sSf https://get-ghcup.haskell.org | sh
```
It will ask install `ghcup`, `ghc`, `stack`, `cabal` by default and ask you if you want to install pre-releases (I simply answered no to those). You will also want `hls` for integration with vscode (or any other editor of your choice).

## the beginning

In haskell, there is no concept of statements. Everything in haskell is an expression. An expression has a value and a type.

| **Expression** | **Value** | **Type** |
|----------------|-----------|----------|
| True       | True  | Bool |
| "adi"      | "adi" | Char |
| "adi" ++ " raj" | "adi raj" | Char |
| not False  | True  | Bool  |


Expressions consists of functions applied to arguments. Arguments  can be function application or function itself.
In haskell, function application we write the name of function followed by arguments with space between distinct argumnets. So, `f a b ` is equivalent to `f(a, b)` in C-style languages. Parenthesis serve different purpose here; such as determining the order of the function application.

```haskell
f g (h 1)
f (g h) 1
f (g h 1)
f (g (h 1))
f g h 1
```

is equivalent to following in the C-style languages:

```c
f(g, h(1))
f(g(h), 1)
f(g(h,1))
f(g(h(1)))
f(g, h, 1)
```

Some functions are  given special treatment as operator and given special character like **+** for addition, **++** for string concatenation. So, operators are generally binary and require arguments between them. Also, precendence of operators are less than other functions.
Like

```haskell
f a + g a
f ( a + g b)
```
is equivalent to following C-style expression

```c
f(a) + g(b)
f(a, g(b))
```

> In haskell, function application is left associative that means `f g h 1` is equivalent to `(((f g) h) 1)`.
{: .prompt-info}

### some primitives types

| Type                   | Use                                    | Operations             | Literals                        |
|------------------------|----------------------------------------|------------------------|---------------------------------|
| Int                    | Number type (signed, 64-bit)           | +, -, *, div, mod      | -3,3,0                          |
| Integer                | Unbounded number type                  | +, -, *, div, mod      | 9999999999999999999, -2, 0, 3   |
| Double                 | Floating point number 64 bit           | +, -, *, /             |  3.14, 3.12e2, 1.2              |
| String (aka [ Char ])  | List of character                      | ++, reverse            | "a", "adi" , ""                 |
| Bool                   | Truth values                           | \|\|, &&, not          | Truth, False                    |
