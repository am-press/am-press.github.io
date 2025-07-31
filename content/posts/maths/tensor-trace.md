---
author: "Pantelis Sopasakis"
title:  "Tensor trace"
date: 2025-07-31
description: "Tensor trace"
summary: "Tensor trace"
math: true
series: ["Mathematix"]
tags: ["Algebra"]
collapsible: true
---

<p>Given a vector space $V$ over a field $k$ we define the trace operator to be the linear map</p>
<p>$$\mathrm{tr}: V^* \otimes V\to k,$$</p>
<p>where for $a\in V^*$ and $u\in V$,</p> 
<p>$$\mathrm{tr}(a\otimes u) = a(u).$$</p>
<p>Having defined this for <em>pure</em> tensors, for general tensors we have</p> 
<p>$$\mathrm{tr}\left(\sum_i c_i(a_i \otimes u_i)\right) = \sum_i c_i a_i(u_i).$$</p>
<p>Alternatively, leveraging the <b>universal property of tensors</b>, we can define the bilinear map $\mathrm{tr}': V^*\times V \to k$ with</p> 
<p>$$\mathrm{tr}'(\phi, x) = \phi(x).$$</p>
<div id="fig1">
    <img src="/tensor-trace-1.png" alt="Tensor trace"  style="width: 55%; margin-left: auto;margin-right: auto;">
    <p><em><strong>Figure 1.</strong> Universal property for the trace map.</em></p>
</div>

### Interpretation in the finite-dimensional case
<p>We know that in the finite-dimensional case, where $\dim V = n$, $V^*\otimes V \cong {\rm Hom}(V, V)$ with [[Isomorphism|isomorphism]] an function which, for pure tensors, is defined as</p>
<p>$$\Phi(a\otimes v)(\xi) = a(\xi)v.$$</p>
<p>Since this $\Phi: V^*\otimes V \to {\rm Hom}(V, V)$ is linear, when applied to general tensors it gives</p>
<p>$$\Phi\left(\sum_i \lambda_i (a_i\otimes v_i)\right)(\xi) = \sum_i \lambda_i a_i(\xi)v_i.$$</p>
<p>To define the inverse, suppose that a [[Basis of vector space|basis]] for $V$ is $B_V = \{e_1, \ldots, e_n\}$. The corresponding [[Basis of vector space#Dual basis|dual basis]] is $B_{V^*} = \{e_1^*, \ldots, e_n^*\}$. The inverse is</p>
<p>$$\Phi^{-1}(\phi) = \sum_{i}e_i^* \otimes \phi(e_i).$$</p>
<div id="fig2">
    <img src="/tensor-trace-2.png" alt="Tensor trace as trace of endomorphism"  style="width: 55%; margin-left: auto;margin-right: auto;">
    <p><em><strong>Figure 2.</strong> The trace of a tensor can be seen as the trace of an endomorphism.</em></p>
</div>
<p>The linear map $\widetilde{\mathrm{tr}}:{\rm Hom}(V, V) \to k$ is defined as </p>
<p>$$\widetilde{\mathrm{tr}} = \mathrm{tr} \circ \Phi^{-1}.$$</p>

### Finite-dimensional vector spaces
<p>If $V$ is a finite vector space with basis $B_V = \{e_1, \ldots, e_n\}$, then the dual basis, of $V^*$, is $B_{V^*} = \{e_1^*, \ldots, e_n^*\}$. The tensor space has basis vectors $B_{V^*\otimes V} = \{e_i^* \otimes e_j\}_{i,j=1}^{n}$ and $\dim (V^*\otimes V) = n^2$. The trace has been defined as a linear map $\mathrm{tr}: V^* \otimes V\to k$, with $\mathrm{tr}(a\otimes u) = a(u)$, so it suffices to see how it works on the basis of the tensor space. It is </p>
<p>$$\mathrm{tr}(e_i^*\otimes e_j) = e_i^*(e_j) = \delta_{i,j},$$</p>
<p>where $\delta_{i,j}$ is the Kronecker delta.</p>


