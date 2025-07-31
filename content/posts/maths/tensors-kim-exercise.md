---
author: "Pantelis Sopasakis"
title:  "Kim: Exercise 3.1.R (Tensors)"
date: 2025-07-31
description: "Tensor trace"
summary: "An interesting exercise from D. Kim, A rough guide to linear algebra"
math: true
series: ["Mathematix"]
tags: ["Algebra"]
collapsible: true
---

<p>This exercise is from D. Kim, A rough guide to linear algebra, see p. 62, [link](https://stanford.edu/~dkim04/writings/LinAlg.pdf), accessed on 31 July 2025.</p>

<div style="border-style:solid;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="thm1">
    <p><strong>Exercise 3.1.R.</strong> 
    Let $V$ be a finite-dimensional vector space. Let $f, g: V\to V$, corresponding to $f, g\in V^*\otimes V$. Then, $g\circ f: V\to V$, linear and corresponds to $g\circ f \in V^* \otimes V$. Show that </p>
    <p>$$g\circ f = (\mathrm{id} \otimes \mathrm{tr} \otimes \mathrm{id})(f\otimes g).$$ </p>
</div>

<h2>Some useful isomorphisms</h2>
<p>Suppose $V$ is a finite-dimensional vector space with basis $B_V = \{e_1, \ldots, e_n\}$ and let the dual basis be $B_{V^*}=\{e_1^*, \ldots, e_n^*\}$.</p>

<p>Here we identify ${\rm Hom}(V, V)$ with $V^* \otimes V$. Each $f\in {\rm Hom}(V, V)$ corresponds to the following element of the tensor product space $V^* \otimes V$</p>
<p>$$\hat{f} = \sum_{i=1}^{n} e_i^* \otimes f(e_i).$$</p>
<p>Conversely, every element of $V^* \otimes V$,</p>
<p>$$q = \sum_{i=1}^{k}a_i \otimes v_i,$$</p>
<p>can be seen as an endomorphism</p>
<p>$$\check{q}(\xi) = \sum_{i=1}^{k} a_i(\xi) v_i,$$</p>
<p>for $\xi \in V$.</p>

<p>Now we can do a little tango: ${\rm End}(V) \overset{\hat{\cdot}}{\to} V^* \otimes V \overset{\check{\cdot}}{\to} {\rm End}(V)$</p> 
<p>$$f(\xi) = \check{\hat{f}}(\xi) = \sum_{i=1}^{n} e_i^*(\xi)f(e_i).$$</p>
<p>Hereafter, with some abuse of notation we will identify $f$ with $\hat{f}$ and $\check{\hat{f}}$ and write just $f$.</p>

<h2>Compositions</h2>

<p>Suppose $f, g\in {\rm End}(V)$ with $g(u) = \sum_{j=1}^{n} e_j^*(u)g(e_j)$ and $f(\xi)=\sum_{i=1}^{n}e_i^*(\xi) f(e_i)$. Then,</p>
<p>$$(g\circ f)(\xi) = \sum_{j=1}^{n} e_j^*\left(\sum_{i=1}^{n}e_i^*(\xi) f(e_i)\right)g(e_j) = \sum_{i, j} e_i^*(\xi)e_j^*(f(e_i))g(e_j).$$</p>

<h2>Solution</h2>

<p>Treating $f\in {\rm Hom}(V,V)$ as an element of $V^*\otimes V$, $f = \sum_i e_i^* \otimes f(e_i)$, and, likewise, $\hat{g} = \sum_i e_i^* \otimes g(e_i)$. Taking a $\otimes$ we have</p>
<p>$$\begin{aligned}
f \otimes g
{}={}& 
\left(\sum_i e_i^* \otimes f(e_i)\right)\left(\sum_i e_i^* \otimes g(e_i)\right) 
\\
{}={}& \sum_{i}\sum_{j} e_i^* \otimes f(e_i) \otimes e_j^* \otimes g(e_j)
\end{aligned}$$</p>
<p>Therefore,</p>
<p>$$\begin{aligned}
(\mathrm{id} \otimes \mathrm{tr} \otimes \mathrm{id})(f\otimes g) 
{}={}& 
\sum_{i}\sum_{j} e_i^* \otimes \underbrace{\mathrm{tr}(f(e_i) \otimes e_j^*)}_{\in k} \otimes g(e_j) \\
{}={}&
\sum_{i}\sum_{j} e_j^*(f(e_i)) \cdot (e_i^*  \otimes g(e_j))
\end{aligned}$$</p>
<p>Here we used the fact that $k \otimes V \cong V$. Next, $e_i^*  \otimes g(e_j)$ is an element of $V^* \otimes V$, so we can treat it as an element of ${\rm End}(V)$. We can write</p>
<p>$$\begin{aligned}
(\mathrm{id} \otimes \mathrm{tr} \otimes \mathrm{id})(f\otimes g)(\xi)
{}={}& 
\sum_{i}\sum_{j} e_j^*(f(e_i)) e_i(\xi)g(e_j) = (g\circ f)(\xi).
\end{aligned}$$</p>
<p>We have shown that $(\mathrm{id} \otimes \mathrm{tr} \otimes \mathrm{id})(f\otimes g) = g\circ f$. $\Box$</p>

