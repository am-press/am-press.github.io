---
author: "Pantelis Sopasakis"
title:  "Monomial orderings"
date: 2025-08-01
description: "Monomial orderings"
summary: "Monomial orderings"
math: true
series: ["Mathematix"]
tags: ["Algebra"]
collapsible: true
---


Monomial orderings are used to define a division algorithm for multivariate polynomials. There can be many orderings. 

A monomial ordering in $k[x_1, \ldots, x_n]$, where $k$ is a field, is essentially a multiindex<sup>(<a href="#endnotes">1</a>)</sup> because every monomial $x_1^{i_1}x_2^{i_2}\cdots x_n^{i_n}$ is uniquely identified by a vector $\iota = (i_1, \ldots, i_n)\in \mathbb{N}^n$, which is a multiindex. An ordering of monomials is an ordering in $\N^n$. 

## Desiderata

> **Desiderata for monomial orderings**
> A monomial ordering on $k[x_1,\ldots, x_n]$ is a relation ${}>{}$ on $\N^n$ such that 
> 1. ${}>{}$ is a total ordering
> 2. If $\alpha > \beta$ and $\gamma\in\N^n$, then $\alpha+\gamma > \beta+\gamma$
> 3. ${}>{}$ is a well-ordering on $\N^n$

A **total order** implies that
1. For $a, b\in \N^n$ either $a>b$, or $b>a$, or $a=b$
2. If $a<b$ and $b<c$ then $a<c$

A **well-order** means that every subset $A\subset\N^n$ has a least element. This means that every sequence $a_1 > a_2 > a_3 > \ldots$ eventually terminates.

## Monomial orders

### Lexicographic order

<div style="border-style:solid;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="lex">
    <p><strong>Definition 1: lexicographic order.</strong> 
    Let $\alpha,\beta\in\N^n$. We say $\alpha >_{\rm lex} \beta$ if in the difference $\alpha - \beta$, the <em>leftmost</em> nonzero entry is positive.</p>
</div>

<p>We use the notation $x^\alpha >_{\rm lex} x^\beta$ whenever $\alpha >_{\rm lex} \beta$.</p>

<div style="border-style:solid;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="lex_is_monord">
    <p><strong>Proposition 1</strong> 
    The lexicographic order is a monomial order.</p>
</div>

<p>The lexicographic order depends on the order of the variables. Let's see how the lex order works in $k[x_1, x_2, x_3]$:</p>

<ol>
<li>$x_1$ has multiindex $(1, 0, 0)$ and $x_2$ has mutiindex $(0, 1, 0)$, so $(1, 0, 0) - (0, 1, 0) = (\textcolor{red}{1}, -1, 0)$, so $x_1 >_{\rm lex} x_2$</li>
<li>The multiindex of $x_2^2x_3$ is $(0, 2, 1)$, so $x_1 >_{\rm lex} x_2^2x_3$ because $(1, 0, 0) - (0, 2, 1) = (\textcolor{red}{+1}, -2, -1)$</li>
<li>Likewise $x_1x_2x_3^2 >_{\rm lex} x_1x_2x_3$ because $(1, 1, 2) >_{\rm lex} (1, 1, 1)$ because $(1,1,2)-(1,1,1)=(0, 0, \textcolor{red}{+1})$. </li>
</ol>

<p>The second example is a little weird. The term $x_1$ has degree $1$, but $x_2^2x_3$ has degree $3$, yet $x_1 >_{\rm lex} x_2^2x_3$.</p>


### Graded lexicographic order

<p>To remedy this we introduce an ordering called the <b>graded lexicographic order</b> which firstly orders the monomials by their total degree and then lexicographically.</p>

<div style="border-style:solid;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="grlex">
    <p><strong>Definition 2: graded lexicographic order</strong> 
    Let $\alpha, \beta\in \N^n$. Then $\alpha >_{\rm grlex} \beta$ if $|\alpha| > |\beta|$, or ($|\alpha|=|\beta|$ and $\alpha >_{\rm lex} \beta$).</p>
</div>

<p>Back to our previous examples, although $x_1 >_{\rm lex} x_2^2x_3$, we now have $x_1 <_{\rm grlex} x_2^2x_3$ because $\deg(x^2x_3)=3$, while $\deg(x_1)=1$.</p>

### Multidegree and leading monomial

<p>We can now define</p>

<div style="border-style:solid;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="multideg_lc_lm">
    <p><strong>Definition 3: Multidegree</strong> 
    Let $f=\sum_{\alpha\in \mathcal{I}}a_{\alpha} x^\alpha$ be a nonzero polynomial in $k[x_1, \ldots, x_n]$ and let ${}>{}$ be a monomial ordering. The multidegree of $f$ is</p>
    <p>$$\operatorname{mdeg} f = \max\{\alpha\in\mathcal{I}: a_\alpha \neq 0\},$$</p>
    <p>where the maximum is taken with respect to ${}>{}$.</p>   
</div>

<div style="border-style:solid;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="multideg_lc_lm">
    <p><strong>Definition 4: Leading coeff, monomial, term</strong> 
    Let again $f=\sum_{\alpha\in \mathcal{I}}a_{\alpha} x^\alpha$ be a nonzero polynomial in $k[x_1, \ldots, x_n]$ and let ${}>{}$ be a monomial ordering. 
    The leading coefficient is $\operatorname{lc}f = a_{\operatorname{mdeg} f},$
    the leading monomial is $\operatorname{lm}f = x^{\operatorname{mdeg} f},$
    and the leading term is
    </p>    
    <p>$$\operatorname{lt} f = \operatorname{lc}f  \cdot \operatorname{lm}f.$$</p>
</div>


### Exercises

This is from <a href="#references">Ref 1.</a>, p60:

> **Exercise 1, p60.**
> Rewrite each of the following polynomials, ordering the terms using the lex order and the grlex order, giving $\operatorname{mdeg} f$, $\operatorname{lc}f$, $\operatorname{lm}f$, and $\operatorname{lt} f$ in each case.
> 1. $f_1(x, y, z) = 2x+3y+z+x^2 −z^2 +x^3$
> 2. $f_2(x, y, z) = 2x^2 y^8 −3x^5yz^4 +xyz^3 −xy^4$

<p><em>Solution.</em> (1) For $f_1$ we have</p>

| Monomial | Multiindex  | Total degree |
| -------- | ----------- | ------------ |
| $x^3$    | $(3, 0, 0)$ | 3            |
| $x^2$    | $(2, 0, 0)$ | 2            |
| $z^2$    | $(0, 0, 2)$ | 2            |
| $x$      | $(1, 0, 0)$ | 1            |
| $y$      | $(0, 1, 0)$ | 1            |
| $z$      | $(0, 0, 1)$ | 1            |

<p>The lexicographic ordering is </p>
<p>$$(3, 0, 0) >_{\rm lex} (2, 0, 0) >_{\rm lex} (1, 0, 0) >_{\rm lex} (0, 1, 0) >_{\rm lex} (0, 0, 2) >_{\rm lex} (0, 0, 1),$$</p>
<p>i.e.,</p>
<p>$$x^3 >_{\rm lex} x^2 >_{\rm lex} x >_{\rm lex} y >_{\rm lex} z^2 >_{\rm lex} z.$$</p>
<p>Under this ordering:</p>
<p>$$\begin{aligned}
    \operatorname{mdeg} f {}={}& (3, 0, 0)\\
    \operatorname{lc}f {}={}& 1,\\
    \operatorname{lm}f {}={}& x^3,\\
    \operatorname{lt}f {}={}& x^3.
\end{aligned}$$</p>
<p>The graded lexicographic ordering is</p>
<p>$$(3, 0, 0) >_{\rm grlex} (2, 0, 0) >_{\rm grlex} (0, 0, 2) >_{\rm grlex} (1, 0, 0) >_{\rm grlex} (0, 1, 0) >_{\rm grlex} (0, 0, 1),$$</p>
<p>or, in terms of monomials</p>
<p>$$x^3 >_{\rm grlex} x^2 >_{\rm grlex} z^2 >_{\rm grlex} x >_{\rm grlex} y >_{\rm grlex} z.$$</p>
<p>and under the graded lex ordering we have the same multideg as before.</p>

<p>(2) For $f_2$ we have the following table</p>

| Monomial  | Multiindex  | Total degree | Coeff |
| --------- | ----------- | ------------ | ----- |
| $x^5yz^4$ | $(5, 1, 4)$ | 10           | $-3$  |
| $x^2y^8$  | $(2, 8, 0)$ | 10           | $2$   |
| $xy^4$    | $(1, 4, 0)$ | 5            | $-1$  |
| $xyz^3$   | $(1, 1, 3)$ | 5            | $1$   |

<p>Here the lexicographic and graded lexicographic orderings coincide:</p>
<p>$$x^5yz^4 > x^2y^8 > xy^4 > xyz^3,$$</p>
<p>and</p>
<p>$$\begin{aligned}
    \operatorname{mdeg} f_2 {}={}& (5, 1, 4)\\
    \operatorname{lc}f_2 {}={}& -3,\\
    \operatorname{lm}f_2 {}={}& x^5yz^4,\\
    \operatorname{lt} f_2 {}={}& -3x^5yz^4.
\end{aligned}$$</p>
<p>This completes the solution. $\bullet$</p>

<p>We can show that</p>

<div style="border-style:solid;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="prop2">
    <p><strong>Proposition 2: Multidegree properties</strong> 
    For $f,g\in k[x_1, \ldots, x_n]$,</p>
    <ol>
        <li>$\operatorname{mdeg}(fg) = \operatorname{mdeg} f + \operatorname{mdeg} g$</li>
        <li>If $f+g\neq 0$, then $$\operatorname{mdeg}(f+g)\leq \max \{\operatorname{mdeg} f, \operatorname{mdeg} g\},$$ and if $\operatorname{mdeg} f \neq \operatorname{mdeg} g$, then $$\operatorname{mdeg}(f+g)=\max \{\operatorname{mdeg} f, \operatorname{mdeg} g\}.$$</li>
    </ol>
</div>

<p>We can also show the following regarding $\operatorname{lt}$:</p>


<div style="border-style:solid;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="prop3">
    <p><strong>Proposition 3: Properties of lt and lm</strong> 
    For two polynomials $f,g\in k[x_1, \ldots, x_n]$ we have </p>
    <ol>
        <li>$\operatorname{lt}(fg) = \operatorname{lt}(f)\operatorname{lt}(g)$</li>
        <li>$\operatorname{lm}(f + g) \leq \max\{\operatorname{lm}(f), \operatorname{lm}(g)\}$</li>
    </ol>
</div>

<p>The property $\operatorname{lt}(fg)=\operatorname{lt}(f)\operatorname{lt}(g)$ is because $k$ is a field, thus an integral domain<sup>(<a href="#endnotes">2</a>)</sup>, so the product of two nonzero elements is nonzero. No cancellation can occur when multiplying.</p>

## MATLAB implementations

Implementation of lex order:
```matlab
function y = lex(a, b)
% Lexicographic ordering of multiindices
%
% INPUTS:
% a, b: multiindices (arrays)
% OUTPUT:
% 1 if a > b, -1 if b > a, 0 if a = b

d = a - b;
idx_d = find(d~=0, 1, 'first');
if isempty(idx_d)
    y = 0;
else
    y = sign(d(idx_d));
end
```
The graded lex ordering is as follows:
```matlab
function y = grlex(a, b)
% Graded lexicographic ordering of multiindices
%
% INPUTS:
% a, b: multiindices (arrays)
% OUTPUT:
% 1 if a > b, -1 if b > a, 0 if a = b

d_deg = sum(a) - sum(b);
if d_deg ~= 0
    y = sign(d_deg);
else
    y = lex(a, b);
end
```
The following function orders a sequence of multiindices according to a monomial ordering. The sequence of monomials is provided as a matrix where each row is a multiindex. This implementation is adapted from code found on Wikipedia's article on the [cocktail shaker sort](https://en.wikipedia.org/wiki/Cocktail_shaker_sort). 

```matlab
% INPUTS:
% x - matrix of multiindices (monomials)
% ord - monomial ordering as function handle
%       defaults to lex
% OUTPUT:
% x - sorted x

if nargin == 1
    ord = @(s1, s2) lex(s1, s2);
end

beginIdx = 1;
endIdx = size(x, 1) - 1;

while beginIdx <= endIdx
    newBeginIdx = endIdx;
    newEndIdx = beginIdx;
    for ii = beginIdx:endIdx
        if ord(x(ii,:), x(ii + 1, :)) == -1
            [x(ii+1, :), x(ii, :)] = deal(x(ii, :), x(ii+1, :));
            newEndIdx = ii;
        end
    end
    
    endIdx = newEndIdx - 1;
    
    for ii = endIdx:-1:beginIdx
        if ord(x(ii, :), x(ii + 1,:)) == -1
            [x(ii+1, :), x(ii, :)] = deal(x(ii, :), x(ii+1,:));
            newBeginIdx = ii;
        end
    end
    beginIdx = newBeginIdx + 1;
end
end
```
Example of use:

```matlab
sort1 = monomial_sort([1 2 0;0 2 1;2 0 0; 1 0 0; 1 1 1]);
grl = @(s1, s2) grlex(s1, s2);
sort2 = monomial_sort([1 2 0;0 2 1;2 0 0; 1 0 0; 1 1 1], grl);
```


## References

1. David A. Cox, John Little, Donal O'Shea, Ideals, Varieties and Algorithms: An Introduction to Computational Algebraic Geometry and Commutative Algebra, Springer, 2015


## Endnotes

1. A multiindex is an $n$-tuple of nonnegative integers. The *length* of a multiindex $\alpha$ is the sum of its components and it is denoted as $|\alpha|$.

2. Recall that an integral domain is a nonzero commutative ring where the product of nonzero elements is nonzero ($ab=0$ implies $a=0$ or $b=0$).