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
<p>$$\tr: V^* \otimes V\to k,$$</p>
<p>where for $a\in V^*$ and $u\in V$,</p> 
<p>$$\tr(a\otimes u) = a(u).$$</p>
<p>Having defined this for *pure* tensors, for general tensors we have</p> 
<p>$$\tr\left(\sum_i c_i(a_i \otimes u_i)\right) = \sum_i c_i a_i(u_i).$$</p>
<p>Alternatively, leveraging the <b>universal property of tensors</b>, we can define the bilinear map $\tr': V^*\times V \to k$ with</p> 
<p>$$\tr'(\phi, x) = \phi(x).$$</p>
