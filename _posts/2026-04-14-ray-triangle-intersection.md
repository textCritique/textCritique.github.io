---
layout: post
title: Ray-triangle intersection
date: 2026-04-14 14:46 +0530
description: Math prerequisite for ray tracing a triangle.
image:
  path: ./assets/images/raytraced_triangle.png
  alt: a raytraced scene consisting of only triangles
category: [notes, graphics]
tags: [math, graphics, raytracing]
author: textCritique
math: true
published: true
sitemap: true
---

A ray will intersect a triangle if there exist $$t$$ such that the
point

$$
P = O + t\vec{D}
\label{ray_eq}
$$

lies on the surface of the triangle.

Here, $$O$$ is origin of the ray (not necessarily $$(0,0,0)$$) and $$\vec{D}$$ is the direction vector of the ray.

For defining surface of the triangle, we will use barycentric coordinates. Any point on the plane of the triangle is of the form

$$
a + \beta (b-a) + \gamma (c-a)
\label{triangle_expr}
$$

where

- $$a$$, $$b$$ and $$c$$ the 3D coordinate of vertices A, B and C of the triangle.

When

$$ \beta > 0 \wedge \gamma > 0 \wedge \beta + \gamma < 1 $$

Then the point defined by \eqref{triangle_expr} lies inside the triangle.

Combining \eqref{triangle_expr} and \eqref{ray_eq}, we get

$$
\begin{aligned}
x_o + t x_d &= x_a + \beta (x_b - x_a) + \gamma (x_c - x_a) \\
y_o + t y_d &= y_a + \beta (y_b - y_a) + \gamma (y_c - y_a) \\
z_o + t z_d &= z_a + \beta (z_b - z_a) + \gamma (z_c - z_a)
\end{aligned}
$$

Rearranging we get

$$
\begin{aligned}
\beta (x_a - x_b) + \gamma (x_a - x_c) + t x_d &= x_a - x_o \\
\beta (y_a - y_b) + \gamma (y_a - y_c) + t y_d &= y_a - y_o \\
\beta (z_a - z_b) + \gamma (z_a - z_c) + t z_d &= z_a - z_o
\end{aligned}
$$

In matrix equation we can express it as

$$
\begin{bmatrix}
 x_a - x_b & x_a - x_c &  x_d \\
 y_a - y_b & y_a - y_c &  y_d \\
 z_a - z_b & z_a - z_c &  z_d
\end{bmatrix}
\begin{bmatrix}
\beta \\
\gamma \\
t
\end{bmatrix}
=
\begin{bmatrix}
 x_a - x_o \\
 y_a - y_o \\
 z_a - z_o
\end{bmatrix}
\label{matrix_eq}
$$

We can solve this system of equation using cramer's rule. But we want to avoid recomputing the terms, so we will expand the determinant carefully.

First, we rewrite \eqref{matrix_eq} as

$$
\begin{bmatrix}
 a & d & g \\
 b & e & h \\
 c & f & i
\end{bmatrix}
\begin{bmatrix}
\beta \\
\gamma \\
t
\end{bmatrix}
=
\begin{bmatrix}
j \\
k \\
l
\end{bmatrix}
\label{meta_eq}
$$

Now, computing determinants in terms elements of \eqref{meta_eq}

$$
\begin{aligned}
\delta &= a(ei - hf) + h(gf - di) + c(dh - eg) \\
\delta_{\beta} &= j(ei-hf) + k(gf - di) + l(dh - eg) \\
\delta_{\gamma} &= i(ak - jb) + h(jc - al) + g(bl - kc) \\
\delta_t &= -[f(ak - jb) + e(jc - al) + d(bl - kc)]
\end{aligned}
$$

If there is no solution then either triangle is degenerate or ray is almost parallel to the triangle.
