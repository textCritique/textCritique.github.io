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

\begin{equation}
P = O + t\vec{D}
\label{ray_eq}
\end{equation}

lies on the surface of the triangle.

Here, $$O$$ is origin of the ray (not necessarily $$(0,0,0)$$) and $$\vec{D}$$ is the direction vector of the ray.

For defining surface of the triangle, we will use barycentric coordinates. Any point on the plane of the triangle is of the form

\begin{equation}
a + \beta (b-a) + \gamma (c-a)
\label{triangle_expr}
\end{equation}

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

\begin{equation}
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
\end{equation}

We can solve this system of equation using cramer's rule. But we want to avoid recomputing the terms, so we will expand the determinant carefully.

First, we rewrite \eqref{matrix_eq} as

\begin{equation}
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
\end{equation}

Now, computing determinants in terms elements of \eqref{meta_eq}

$$
\begin{aligned}
\Delta &= a(ei - hf) + h(gf - di) + c(dh - eg) \\
\Delta_{\beta} &= j(ei-hf) + k(gf - di) + l(dh - eg) \\
\Delta_{\gamma} &= i(ak - jb) + h(jc - al) + g(bl - kc) \\
\Delta_t &= -[f(ak - jb) + e(jc - al) + d(bl - kc)]
\end{aligned}
$$

If there is no solution then either triangle is degenerate or ray is almost parallel to the triangle.

In implementation, we only compute the value when we need them. Given below is the code which was used raytracer program in the preview image.

```c

// assign parameter t a finite value if triangle is intersected otherwise infinite value
// o is the origin i.e. from where ray originates
// direction is the direction of the ray
// tmin and tmax is the minimum and the maximum range of triangle
// tgb is the array which stores computed value of t, g and b, t stores INF if intersection doesn't fit the conditions 
void instersectTriangle(Point o, Point direction, Triangle tri, double tmin, double tmax, double tgb[]){
  // factoring repeated terms so that same thing is computed once
  double a = tri.a.x  - tri.b.x  ;
  double b = tri.a.y  - tri.b.y  ;
  double c = tri.a.z  - tri.b.z  ;
  double d = tri.a.x  - tri.c.x  ;
  double e = tri.a.y  - tri.c.y  ;
  double f = tri.a.z  - tri.c.z  ;
  double g = direction.x;
  double h = direction.y;
  double i = direction.z;
  double j = tri.a.x - o.x ;
  double k = tri.a.y - o.y ;
  double l = tri.a.z - o.z ;
  // computing frequently occuring terms
  double eihf = e*i - h*f;
  double gfdi = g*f - d*i;
  double dheg = d*h - e*g;
  double akjb = a*k - j*b;
  double jcal = j*c - a*l;
  double blkc = b*l - k*c;
  // determining determinants for applying cramer's rule
  double A_det = a*(eihf ) + b*(gfdi) + c*(dheg);
  double t_det = -(f*(akjb) + e*(jcal) + d*(blkc));
  // t having INF value will act as flag
  tgb[0] = INFINITY;
  // checking if point on the triangle is within the visible range
  double t = t_det/A_det;
   if (t < tmin || t > tmax){
     tgb[0] = INFINITY;
     return;
   }
   double gamma_det = i*(akjb) + h*(jcal) + g*(blkc);
   double gamma = gamma_det/A_det;
   if (gamma < 0 || gamma > 1){
     tgb[0] = INFINITY;
     return;
   }
   double beta_det = j*(eihf) + k*(gfdi) + l*(dheg);
   double beta = beta_det/A_det;
   if (beta < 0 || beta > 1 - gamma){
     tgb[0] = INFINITY;
     return;
   }
  tgb[0] = t;
  tgb[1] = gamma;
  tgb[2] = beta;   
}
  
```
