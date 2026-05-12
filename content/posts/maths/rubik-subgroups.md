---
author: "Pantelis Sopasakis"
title:  "Subgroups of Rubik's cube group"
date: 2025-09-10
description: "Subgroups of Rubik's cube group"
summary: "Subgroups of Rubik's cube group"
math: true
series: ["Mathematix"]
tags: ["Algebra", "Group Theory"]
collapsible: true
---

In this post we look at Rubik's cube group and look for subgroups which are isomorphic to a simple group (such as $A_n$ for $n\geq 5$), or a dihedral group, and happen to have "simple" generators, which can be expressed as commutators of a few rotations and products thereof. 

Firstly, we define the commutator of $x, y\in G$ as 
$$[x, y]=xyx^{-1}y^{-1}.$$
We also define the conjugate of $x$ by $y$ as $x^y = yxy^{-1}$.
When it comes to Rubik's cube note that for $x,y\in \{R, L, F, B, U, D\}$ we have the following useful identities
- $[x^2, y^2]=(x^2y^2)^2$
- $[x^2, yx]=[x^2, y]$
- $[x^2, [y^2, x^2]] = (x^2y^2)^4$
- $[x, [y^2, x^2]]=[x, y^2x^2y^2]=[x, (x^2)^{y^2}]$ 


Next, we present some results having limited our search within the subgroups 
- $\langle F, U^2, R^2, B^2, L^2, D^2\rangle$ (of size 19,508,428,800) 
- $\langle F, U, R^2, B^2, L^2, D^2 \rangle$ (of size 21,119,142,223,872,000).


## Dihedral groups

| $D_n$     | Dihedral subgroup                                           |
| --------- | ----------------------------------------------------------- |
| $D_8$     | $\left\langle\textcolor{blue}{[F, [FU, U^2]]}, \textcolor{red}{[F, [F, R^2]]}\right\rangle$    |
| $D_{10}$  | $\left\langle \textcolor{blue}{[F^2, [F, U]]}, \textcolor{red}{[F, [RL, F^2]]}\right\rangle$   |
| $D_{12}$  | $\left\langle\textcolor{blue}{[L, [FU, F^2]]}, \textcolor{red}{[L, [L, F^2]]}\right\rangle$    |
| $D_{16}$  | $\left\langle \textcolor{blue}{[LUF, [F^2, LU]]}, \textcolor{red}{[F, [F, R^2]]}\right\rangle$ |
| $D_{20}$  | $\left\langle \textcolor{blue}{[F, [L^2, FL]]},\textcolor{red}{[U, [U, R^2]]}\right\rangle$    |
| $D_{24}$  | $\left\langle \textcolor{blue}{[U^2, [R^2, F]]}, \textcolor{red}{U^2}\right\rangle$            |
| $D_{30}$  | $\left\langle \textcolor{blue}{[RF, [R^2, RF]]}, \textcolor{red}{R^2}\right\rangle$            |
| $D_{40}$  | $\left\langle \textcolor{blue}{[FL, [R^2, FR]]}, \textcolor{red}{F^2}\right\rangle$            |
| $D_{48}$  | $\left\langle \textcolor{blue}{[LUF, [F^2, LU]]}, \textcolor{red}{[U, [RL, U^2]]}\right\rangle$|
| $D_{60}$  | $\left\langle \textcolor{blue}{[FL, [R^2, FR]]}, \textcolor{red}{U^2}\right\rangle$            |
| $D_{80}$  | $\left\langle \textcolor{blue}{[LUF, [F^2, LU]]}, \textcolor{red}{[U, [U^{-1}, L^2]]}\right\rangle$ |
| $D_{120}$ | $\left\langle \textcolor{blue}{[UL, [U, R^2]]}, \textcolor{red}{[RL, [RL, U^2]]}\right\rangle$ |


![rubik-d8](/rubik-d8.png)

## Alternating subgroups


| Isomorphic to | Subgroup examples                                          | # subgroups in $G$ |
| ------------- | ---------------------------------------------------------- | ------------------------------- |
| A4            | $\left\langle \textcolor{blue}{[F, [R^2, U^2]]}, \textcolor{red}{[F^2, [R^2, U^2]]}\right\rangle$                   | 12,933,043,200                  |
| A5            | $\left\langle \textcolor{blue}{[F, [R^2, F^2]]}, \textcolor{red}{F^2}\right\rangle$                                 | 62,078,607,360                  |
| A6            | $\left\langle \textcolor{blue}{[U, [U^2, LU]]}, \textcolor{red}{[UR, [U, L^2]]}\right\rangle$                       | 144,850,083,840                 |
| A7            | $\left\langle \textcolor{blue}{[L^2, [R^2, RU]]}, \textcolor{red}{[F, [R^2, U^2]]}\right\rangle$                    | 5,832                           |
| A8            | $\left\langle \textcolor{blue}{[F, [U^2, LU]]}, \textcolor{red}{[R^2, [U^2, F^2]]}\right\rangle$                    | 2,187                           |
| A9            | $\left\langle \textcolor{blue}{[F, [F^2, [UR, RF]]]}, \textcolor{red}{[U, [U^2, [B, FR]]]}\right\rangle$            | 56,320                          |

## General linear

<p>$$\begin{aligned}
\mathrm{GL}(2, 4)
{}\cong{}&
\left\langle[L, [R^2, RF]], [RL, [R^{2}, U^{2}]]\right\rangle
\\
{}\cong{}&
\left\langle[F, [R^2, RU]], [F^2, [R^2, U^2]]\right\rangle
\\
{}\cong{}&
\left\langle[F, [R^2, RU]], [F, [R^2, U^2]]\right\rangle
\end{aligned}$$</p>

## Projective special linear
We have some projective special linear subgroups such as 
$$\left\langle[R, [R, U^2]], [R^2, [LU, F^2]]\right\rangle\cong \mathrm{PSL}(3, 2).$$

## Klein four
$$K_4 {}\cong{} \left\langle[RL, [RL, U^2]], [RL, [RL, F^2]]\right\rangle$$
