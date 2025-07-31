---
author: "Pantelis Sopasakis"
title:  "Tensor trace"
date: 2025-07-31
description: "Tensor trace"
summary: "In this post we will look at the familiar trace operation in the context of tensors"
math: true
series: ["Mathematix"]
tags: ["Algebra"]
collapsible: true
---

<p>Our familiar trace operator is a linear map with the property $\mathrm{tr}(AB)=\mathrm{tr}(BA)$ for all square matrices $A$ and $B$, or, what is the same $\mathrm{tr}(xy^\intercal)=y^\intercal x$ for all vectors $x$ and $y$. In fact, if $f$ is a linear map with this property, then it is the trace up to scaling.</p>

<p>To generalise this, we start by observing that $y^\intercal x$ can be thought of as (the linear map) $y$ acting on $x$ to return a scalar, that is $y^\intercal$ can be thought of as an element of the dual space $(\R^n)^*$. Secondly, the trace could be seen as an operator $\mathrm{tr}(y^\intercal, x)$ (an operator $\mathrm{tr}:(\R^n)^*\times\R^n \R\to\R$), which is linear in $x$ and in $y^\intercal$ (i.e., <em>bilinear</em>); from the <a href="../tensor-product-universality">universal property</a> of tensor products, we can look at the trace as a <em>linear</em> map $\mathrm{tr}:(\R^n)^*\otimes \R^n\to\R$.</p> 

<p>We may now define the trace in the context of tensor products.</p>

<h3>Definitions</h3>

<p>Given a vector space $V$ over a field $k$ we define the trace operator to be the linear map</p>
<p>$$\mathrm{tr}: V^* \otimes V\to k,$$</p>
<p>where for $a\in V^*$ and $u\in V$,</p> 
<p>$$\mathrm{tr}(a\otimes u) = a(u).$$</p>
<p>Having defined this for <em>pure</em> tensors, for general tensors we have</p> 
<p>$$\mathrm{tr}\left(\sum_i c_i(a_i \otimes u_i)\right) = \sum_i c_i a_i(u_i).$$</p>
<p>Alternatively, leveraging the <a href="../tensor-product-universality">universal property of tensor products</a>, we can define the bilinear map $\mathrm{tr}': V^*\times V \to k$ with</p> 
<p>$$\mathrm{tr}'(\phi, x) = \phi(x).$$</p>
<div id="fig1">
    <img src="/tensor-trace-1.png" alt="Tensor trace"  style="width: 55%; margin-left: auto;margin-right: auto;">
    <p><em><strong>Figure 1.</strong> Universal property for the trace map.</em></p>
</div>

<h3>Interpretation in the finite-dimensional case</h3>

<p>We know that in the finite-dimensional case, where $\dim V = n$, $V^*\otimes V \cong {\rm Hom}(V, V)$ with isomorphism a function which, for pure tensors, is defined as</p>
<p>$$\Phi(a\otimes v)(\xi) = a(\xi)v.$$</p>
<p>Since this $\Phi: V^*\otimes V \to {\rm Hom}(V, V)$ is linear, when applied to general tensors it gives</p>
<p>$$\Phi\left(\sum_i \lambda_i (a_i\otimes v_i)\right)(\xi) = \sum_i \lambda_i a_i(\xi)v_i.$$</p>
<p>To define the inverse, suppose that a basis for $V$ is $B_V = \{e_1, \ldots, e_n\}$. The corresponding dual basis is $B_{V^*} = \{e_1^*, \ldots, e_n^*\}$. The inverse is</p>
<p>$$\Phi^{-1}(\phi) = \sum_{i}e_i^* \otimes \phi(e_i).$$</p>
<div id="fig2">
    <img src="/tensor-trace-2.png" alt="Tensor trace as trace of endomorphism"  style="width: 55%; margin-left: auto;margin-right: auto;">
    <p><em><strong>Figure 2.</strong> The trace of a tensor can be seen as the trace of an endomorphism.</em></p>
</div>
<p>The linear map $\widetilde{\mathrm{tr}}:{\rm Hom}(V, V) \to k$ is defined as </p>
<p>$$\widetilde{\mathrm{tr}} = \mathrm{tr} \circ \Phi^{-1}.$$</p>

<h3>Finite-dimensional vector spaces</h3>
<p>If $V$ is a finite vector space with basis $B_V = \{e_1, \ldots, e_n\}$, then the dual basis, of $V^*$, is $B_{V^*} = \{e_1^*, \ldots, e_n^*\}$. The tensor space has basis vectors $B_{V^*\otimes V} = \{e_i^* \otimes e_j\}_{i,j=1}^{n}$ and $\dim (V^*\otimes V) = n^2$. The trace has been defined as a linear map $\mathrm{tr}: V^* \otimes V\to k$, with $\mathrm{tr}(a\otimes u) = a(u)$, so it suffices to see how it works on the basis of the tensor space. It is </p>
<p>$$\mathrm{tr}(e_i^*\otimes e_j) = e_i^*(e_j) = \delta_{i,j},$$</p>
<p>where $\delta_{i,j}$ is the Kronecker delta.</p>

<h3>The matrix case</h3>

<div style="border-style:solid;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="thm1">
    <p><strong>Theorem 1.</strong> 
    For $A\in\R^{n\times n}$ (that is, $A:\R^n\to\R^n$),</p>
    <p>$$\widetilde{\mathrm{tr}} A = \sum_{i=1}^{n} A_{ii}.$$</p>     
</div>

<p><em>Proof.</em> Here $A\in\R^{n\times n}$ is treated as a linear map $A:\R^n\to \R^n$. We have </p>
<p>$$\Phi^{-1}(A) = \sum_{i=1}^{n} e_i^* \otimes Ae_i,$$</p>
<p>and</p>
<p>$$\mathrm{tr}(\Phi^{-1}(A)) = \mathrm{tr}\left(\sum_{i=1}^{n} e_i^* \otimes Ae_i\right) = \sum_{i=1}^{n} e_i^*(Ae_i) = \sum_{i=1}^{n} A_{ii}.$$</p>
<p>This completes the proof. $\Box$</p> 

<h3>Trace of dual map</h3>

<div style="border-style:solid;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="thm2">
    <p><strong>Theorem 2.</strong> 
    Assume again that $V$ is finite dimensional. Let $T\in{\rm Hom}(V, V)$ and let $T'$ be the dual map. Then,</p>
    <p>$$\mathrm{tr}(T) = \mathrm{tr}(T').$$</p>     
</div>

<p>Before we proceed with the proof, note that</p> 
<p>$${\rm Hom}(V, V) \cong V^* \otimes V \cong V^* \otimes (V^*)^* \cong (V^*)^* \otimes V^* \cong {\rm Hom}(V^*, V^*).$$</p>
<p><em>Proof.</em> Suppose that a basis of $V$ is $\{e_1,\ldots, e_n\}$ and let the dual basis be $\{e_1^*, \ldots, e_n^*\}$. The canonical embedding of the basis of $V$ onto $V^{**}$ is $e_i^{**}$. We have</p>
<p>$$\begin{aligned}\mathrm{tr} T {}={}& \mathrm{tr} \left( \sum_i e_i^* \otimes Te_i\right) \\
{}={}&  \sum_i \mathrm{tr}(e_i^* \otimes Te_i) \\
{}={}&  \sum_i e_i^* (Te_i) \\
{}={}&  \sum_i T'(e_i^*)(e_i) \\
{}={}&  \sum_i e_i^{**}(T'(e_i^*)) \\
{}={}&  \sum_i \mathrm{tr} (e_i^{**} \otimes T'(e_i^*)) \\
{}={}& \mathrm{tr} T',
\end{aligned}$$</p>
<p>which completes the proof. $\Box$</p>

<h3>Trace on a tensor product space</h3>
<p>Here we will look at the trace on $V\otimes V^*$.<p>
<p>If $V^{**} \cong V$ (e.g., finite-dimensional case), then $V\otimes V^{*} \cong (V^*)^* \otimes V^*$ so, for $x\in V^{**}$ and $a\in V^*$ we can define</p> 
<p>$$\mathrm{tr}(x\otimes a) = x(a) = a(x) = \mathrm{tr}(a\otimes x),$$</p>
<p>where here we have identified $x\in V^{**}$ with $x\in V$ via the isomorphism $V^{**} \cong V$. </p>


<h3>Trace of tensor product and composition</h3>

<p>From <a href="../tensors-kim-exercise"><em>Kim Exercise 3.1.R on tensors</em></a> and assuming $f, g\in {\rm End}(V)$ can be written as $f = a\otimes x$ and $g = b\otimes y$[^1] we have the diagram</p>

<div id="fig1">
    <img src="/tensor-trace-3.png" alt="Trace and tensor products"  style="width: 75%; margin-left: auto;margin-right: auto;">
    <p><em><strong>Figure 3.</strong> Trace and tensor products.</em></p>
</div>

<p>This means </p>
<p>$$\mathrm{tr}((\mathrm{id} \otimes \mathrm{tr} \otimes \mathrm{id})(f\otimes g)) {}={} \mathrm{tr}((\mathrm{id} \otimes \mathrm{tr} \otimes \mathrm{id})(g\otimes f))$$</p>
<p>Equivalently</p>
<p>$$\mathrm{tr}(g\circ f) {}={} \mathrm{tr}(f\circ g).$$</p>
<p>More generally</p>
<p>$$\mathrm{tr}(g\circ f \circ h) {}={} \mathrm{tr}(h\circ f \circ g) {}={} \mathrm{tr}(g \circ h\circ f).$$</p>
<p>At the same time, if we treat $f\in {\rm End}(V)$ as an element of $V^* \otimes V$ we can talk about its trace. If $f = a\otimes x$ and $g=b\otimes y$, then </p>
<p>$$\mathrm{tr}(f\otimes g) = b(x)  a(y) = \mathrm{tr}(g\otimes f).$$</p>

