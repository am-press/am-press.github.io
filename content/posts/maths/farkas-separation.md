---
author: "Pantelis Sopasakis"
title:  From Separation Theorems to Farkas' Lemma
date: 2022-08-30
description: From the Hahn-Banach theorem to the three separating theorems
summary: From the Hahn-Banach theorem to the three separating theorems
math: true
series: ["Mathematix"]
tags: ["Convex Analysis", "Functional Analysis"]
---

<p>In <a href="../hbt-to-separating"  target="_blank">this post</a> we presented a number of separating theorems on topological vector spaces. Here we will make use of the <a href="../hbt-to-separating#second-sep-thm" target="_blank">second separation theorem</a>, which in the case of finite-dimensional spaces is dubbed the <em>hyperplane separation theorem</em> and the assumption of a nonempty interior can be dropped.</p>

> We recommend that the reader reads first the following posts
> - [From Hahn-Banach to Separating Theorems](../hbt-to-separating)
> - <a href="../support-functions" target="_blank">Support functions</a> of convex sets

## Separation Theorem
<p>Let us start by stating the following result.</p>

<div style="border-style:solid;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="hyperplane-sep-thm">
<p><strong>Theorem 1 (Hyperplane separation theorem).</strong> Let \(K, L\) be two convex sets in ${\rm I\!R}^n$ and \(K \cap L = \emptyset\). Then there is a nonzero vector $c \in {\rm I\!R}^n$ such that</p>
<p>$$\sup_{x\in K} c^\intercal x {}\leq{} \inf_{x\in L} c^\intercal x.\tag{1}$$</p>
</div>

<p>Note that the supremum on the left hand side of Equation (1) is the support function of $K$ evaluated at $c$ and the right hand side is</p>
<p>$$\inf_{x\in L} c^\intercal x = -  \sup_{x\in L} -c^\intercal x = -\delta_{L}^{*}(-c).$$</p>
<p>The separation condition of Theorem 1 can be equivalently written as: there is $c \in {\rm I\!R}^n$ such that</p>
<p>$$\delta_{K}^{*}(c) \leq -\delta_{L}^{*}(-c).\tag{2}$$</p>
<p>Since $\delta_{L}^{*}(-c) = \delta_{-L}^{*}(c)$, Equation (2) can be written as</p>
<p>$$\delta_{K}^{*}(c) \leq -\delta_{-L}^{*}(c).\tag{3}$$</p>
<p>In the case where both $K$ and $L$ are cones, Equation (3) is equivalent to</p>
<p>$$\delta_{K^\circ}(c) \leq -\delta_{(-L)^{\circ}}(c) \Leftrightarrow c \in K^\circ \cap (-L)^\circ.\tag{4}$$</p>


## Polar of finitely generated cone

<p>Let $A\in{\rm I\!R}^{m\times n}$ and its columns be</p>
<p>$$A = \begin{bmatrix}a_1 & \cdots & a_n\end{bmatrix}.$$</p>
<p>We say that the set $C$ is a finitely generated cone, generated by the vectors $a_1, \ldots, a_n$ if it is</p>
<p>$$\begin{aligned}C {}={}& \mathrm{cone}(\{a_1, \ldots, a_p\}) \\ {}={}&\left\{ \sum_{i=1}^{n} y_i a_i {}:{} y_i \geq 0, i=1,\ldots, n\right\} \\ {}={}& \{A^\intercal y {}:{} y\geq 0\}.\end{aligned}$$</p>
<p>In other words, we take $C$ to be the set of conic combinations (or <em>nonnegative</em> combinations) of the vectors $a_1, \ldots, a_n$.</p>

<p>Next, let us recall from our previous post on <a href="../support-functions" target="_blank">support functions</a> (and in particular,  <a href="../support-functions#prop15" target="_blank">Proposition 1.5</a>) that if $K$ is a finitely generated cone, then</p>
<p>$$\begin{aligned}\{A^\intercal y {}:{} y\geq 0\}^{\circ} {}={}& \{x : a_i^\intercal x \leq 0\, i=1,\ldots, p\} \\ {}={}&\{x : Ax \leq 0\}\tag{5a}.\end{aligned}$$</p>
<p>Since $K$ is a convex closed cone, we have $K^{\circ \circ} = K$ [<a href="#cite:rock72" title="Rockafellar, Convex Analysis">1, Thm 14.5</a>]</p>
<p>$$\{x : Ax \leq 0\}^{\circ} {}={} \{A^\intercal y {}:{} y\geq 0\}. \tag{5b}$$</p>


<div id="fig0">
<img src="/farkas-polar.png" alt="Polar of cone"  style="width: 60%; margin-left: auto;margin-right: auto;">

<p><em><strong>Figure 0.</strong> The polar of $\{x : Ax \leq 0\}$ is the cone $\{A^\intercal y {}:{} y\geq 0\}$ (and vice versa).</em></p>
</div>


## Farkas' lemma
<p>We have actually already proven Farkas' lemma, but let us have a look at its standard formulation:</p>


<div style="border-style:solid;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="farkas-lemma">
<p><strong>Lemma 2 (Farkas' lemma).</strong> Let $A\in{\rm I\!R}^{m\times n}$ and $b\in{\rm I\!R}^{n}$. Then, exactly one of the following is true</p>
<ol>
  <li>There is $x{}\in{}{\rm I\!R}^{n}$ such that $Ax=b$, $x\geq 0$</li>
  <li>There is $y{}\in{}{\rm I\!R}^{m}$ such that $A^\intercal y \geq 0$ and $b^\intercal y < 0$</li>
</ol>
</div>

<p>Let us start by giving a sketch of the proof that will help us gain a geometric understanding of the lemma. </p>

<p>In <a href="#fig1">Figure 1</a> we see a situation where the second condition of the lemma is satisfied. The light-blue shaded are is the set of all $y$ such that $b^\intercal y < 0$ and the grey shaded cone is the set of $A^\intercal y$ for some $y\geq 0$. We see that the two sets have a nonempty intersection.</p>

<div id="fig1">
<img src="/farkas-2.png" alt="The second condition of Farkas' lemma is satisfied"  style="width: 50%; margin-left: auto;margin-right: auto;">

<p><em><strong>Figure 1.</strong> Condition 2 is satisfied.</em></p>
</div>

<p>In <a href="#fig2">Figure 2</a> we see a situation where the second condition is not satisfied. In particular, the aforementioned sets are disjoint. As a result, by virtue of the <a href="#hyperplane-sep-thm">hyperplane separation theorem</a>, we can separate them by a linear function.</p>

<div id="fig2">
<img src="/farkas-not2.png" alt="The second condition of Farkas' lemma is not satisfied"  style="width: 50%; margin-left: auto;margin-right: auto;">

<p><em><strong>Figure 2.</strong> Condition 2 is not satisfied.</em></p>
</div>

<p>It remains to see under what conditions the two sets are separated. We will do this in the proof of Farkas' lemma below.</p>


<p><em>Proof of Farkas' lemma.</em> We start by showing that it is not possible that both conditions are satisfies simultaneously. Suppose instead that both conditions are satisfied. Then, on the one hand</p>
<p>$$y^\intercal A x = y^\intercal b < 0,$$</p>
<p>and on the other hand</p>
<p>$$y^\intercal A x = (A^\intercal y)^\intercal x \geq 0,$$</p>
<p>which is a contradition.</p>
<p>$(1 \Rightarrow \text{not }2).$ We will show that if condition 2 does not hold, then condition 1 holds. Define the sets $K=\{y : A^\intercal y \geq 0\}$ and $L = \{y : b^\intercal y < 0\}$. To say that 2 does not hold is to say that $K$ and $L$ are disjoint. From the hyperplane separation theorem, there is a vector $c$ such that</p>
<p>$$\delta_{K^\circ}(c) \leq -\delta_{(-L)^\circ}(c).$$</p>
<p>This implies that $c \in K^\circ \cap (-L)^\circ$, and the reader can check that this is equivalent to</p>
<p>$$\begin{cases}c = Ax, x \geq 0\\ c = \alpha b, \alpha \geq 0 \end{cases}$$</p>
<p>Implying that $Ax = \alpha b$ with $x\geq 0$ and $\alpha \geq 0$, which is equivalent to $Ax = b$ for some $x\geq 0$ (which is the first condition).</p>
<p>$(2 \Rightarrow 1).$ Likewise. $\blacksquare$</p>

## References

<ol>
  <li id="cite:rock72">R.T. Rockafellar, Convex Analysis, Princeton University Press, 1972</li>
</ol>
