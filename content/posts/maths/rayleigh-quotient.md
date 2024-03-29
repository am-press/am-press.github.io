---
author: "Pantelis Sopasakis"
title: Rayleigh quotient
date: 2022-07-19
description: Some notes on the Rayleigh quotient
summary: Some notes on the Rayleigh quotient
math: true
series: ["Mathematix"]
tags: ["Variational Analysis", "Convex Analysis", "Directional derivative", "Optimization"]
showtoc: false
---


<p>This will be a short blog post on the Rayleigh quotient of a symmetric matrix, \( A\), which is defined as</p>

$$R_A(x) = \frac{x^\intercal A x}{\|x\|^2},$$ 

<p>for \( x \in\mathbb{R}^n\), with \( x \neq 0\).</p>

## Some properties


<p>Firstly let us prove the following useful preliminary result [1, Section 2.1, Exercise 6 (p19)]:</p>

<div style="border-style:solid;border-width:1.5px;padding: 10px 0px 0px 10px; margin-bottom: 10px">
<p><strong>Lemma 1.</strong> Let the function \( f: \mathbb{R}^n\setminus\{0\} \to \mathbb{R}\) be continuous, satysfying \( f(\lambda x) = f(x)\) for all \( \lambda > 0\) and \( x \in\mathbb{R}^n\), with \( x \neq 0\). Then \( f\) has a minimiser.</p>
</div>

<p><em>Proof.</em> Note that the range of the function is \( f(\mathbb{R} \setminus \{0\}) = f(S_1)\), where \( S_1 = \{x \in \mathbb{R}^n {}:{} \|x\| = 1\}\), which is a compact set. Since \( f\) is continuous, \( f(S_1)\) is a compact subset of \( \mathbb{R}\), therefore it has a minimum. \( \Box\)</p>

<p>As a result, the Rayleigh quotient of a symmetric matrix \( A\) has a minimiser.</p>

<p>Let us now determine the directional derivative of the Rayleight quotient. Here we use the definition of the directional derivative (along a direction \( d\)) as stated in [1, Section 2.1]. The directional derivative of a function \( f: \mathbb{R}^n\to\mathbb{R}\) along a direction \( d\in\mathbb{R}^n\) is given by</p>

$$\begin{aligned}f'(x; d) = \lim_{t\downarrow 0}\frac{f(x + td)-f(x)}{t}.\end{aligned}$$

<p>Note that this is not the same as a <a href="https://mathworld.wolfram.com/DirectionalDerivative.html" target="_blank">common definition</a> of the directional derivative where we take "\( h \to 0\)", instead of \( h \downarrow 0\). If \( f\) is directionally differentiable along all directions \( d\in\mathbb{R}^n\) and there is a vector \( \nabla f(x)\) such that \( f'(x; d) = \langle \nabla f(x), d\rangle\) then we say that \( f\) is Gâteaux-differentiable at \( x\). A lot of interesting facts about directional derivatives can be found <a href="https://maunamn.wordpress.com/13-directional-derivatives-of-convex-functions/" target="_blank">in this blog post</a> by Nguyen Mau Nam.</p>

<div style="border-style:solid;border-width:1.5px;padding: 10px 0px 0px 10px; margin-bottom: 10px">
<p>Proposition 2 (Directional Derivative of Rayleight Quotient).The directional derivative of \( R_A\) along a direction \( d \in \mathbb{R}^n\), \( d \neq 0\) at a point \( x\in\mathbb{R}^n\), with \( x \neq 0\) is given by</p>
<p>$$\begin{aligned} R_A'(x; d) = \frac{2}{\|x\|^4}\left( \|x\|^2 x^\intercal A d - x^\intercal A x x^\intercal d \right).\end{aligned}$$</p>
</div>


<p><em>Proof.</em> It is </p>

<p>$$\begin{aligned}R_A'(x; d) {}={}& \lim_{t{}\downarrow{}0} \frac{1}{t}\left(\frac{(x+td)^\intercal A (x+td)}{\|x+td\|^2} - \frac{x^\intercal A x}{\|x\|^2}\right) \\ {}={}& \lim_{t{}\downarrow{}0} \frac{1}{t}\left(\frac{\|x\|^2 (x^\intercal A x + t^2 d^\intercal A d + 2tx^\intercal A d) - x^\intercal A x \|x+td\|^2}{\|x+td\|^2 + \|x\|^2}\right) \\ {}={}& \ldots \\ {}={}&  \lim_{t{}\downarrow{}0} \frac{2(x^\intercal A d \|x\|^2 - x^\intercal d x^\intercal A x) + t \cdot (\|x\|^2 d^\intercal A d - x^\intercal A x \|d\|^2)}{\|x\|^4 + t \cdot 2x^\intercal d \|x\|^2 + t^2 \cdot \|d\|^2 \|x\|^2} \\ {}={}& \frac{2(x^\intercal A d \|x\|^2 - x^\intercal d x^\intercal A x)}{\|x\|^4},\end{aligned}$$</p>

<p>which completes the proof. \( \Box\)</p>

<p>For that we can tell that </p>

<div style="border-style:solid;border-width:1.5px;padding: 10px 0px 0px 10px; margin-bottom: 10px">
<p><strong>Proposition 3 (Gradient of Rayleigh Quotient).</strong> The Rayleigh Quotient is Gâteaux-differentiable over \( \mathbb{R}^n \setminus \{0\}\) with gradient</p>


$$\begin{aligned} \nabla R_A'(x) {}={}& \frac{2}{\|x\|^4}(\|x\|^2 Ax - \|x\|_A^2 x) \\ {}={}& \frac{2}{\|x\|^2}(Ax - R_A(x) x),\end{aligned}$$

<p>where we have used the notation \( \|x\|_A^2 = x^\intercal A x\) (this is not a norm unless \( A\) is positive definite as well).</p>
</div>

<p>Again, since \( R_A(\mathbb{R} \setminus \{0\})\) is compact, \( R_A\) admits at least one minimum and maximum value over \( S_1\). In fact, if \( x^\star\) is a minimiser with \( \|x^\star\| = 1\), then any point \( x = \lambda x^\star\) with \( \lambda > 0\) is also a minimiser. By Fermat's theorem, at any minimiser \( x^\star\) and maximiser \( x^{\star\star}\) the gradient of the Rayleigh quotient vanishes, i.e., </p>

$$\begin{aligned} Ax = R_A(x) x,\end{aligned}$$

<p>which means that \( x\) and \( Ax\) are colinear. In other words, \( x^\star\) and \( x^{\star\star}\) are eigenvectors of \( A\) and \( R_A(x^\star)\), \( R_A(x^{\star\star})\) are the corresponding eigenvalues.</p>

<p>It is evident that</p>

$$\begin{aligned} \max_{x\neq 0}R_A(x) {}={}& \lambda_{\max}(A),\\  \min_{x\neq 0}R_A(x) {}={}& \lambda_{\min}(A). \end{aligned}$$

<p>One last thing. Let us now focus on the following optimisation problem</p>

<p>$$\begin{aligned} \mathrm{Minimise}_{\|x\|^2 = 1} x^\intercal A x, \end{aligned}$$</p>

<p>where \( A\) is a symmetric matrix. The Lagrangian is \( L: \mathbb{R}^n \times \mathbb{R} \to \mathbb{R}\) given by</p>

<p>$$\begin{aligned} L(x, \lambda) = x^\intercal A x + \lambda (\|x\|^2 - 1), \end{aligned}$$</p>

<p>where \( \nabla_x L(x, \lambda) = 2Ax - 2\lambda x\). By setting this to zero we see that \( Ax = \lambda x\), so we conclude that \( x\) must be an eigenvector of \( A\) (in particular, it should have unit norm) and \( \lambda\) is the corresponding (needless to say, real) eigenvalue.</p>



## References

[1] JM Borwein and AS Lewis, Convex Analysis and Nonlinear Optimization, Canadian Mathematical