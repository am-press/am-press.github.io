---
author: "Pantelis Sopasakis"
title:  "Resultants"
date: 2025-08-01
description: "Resultants"
summary: "Resultants"
math: true
series: ["Mathematix"]
tags: ["Algebra"]
collapsible: true
---

## Summary 

1. Given two (univariate) polynomials, $f, g$, the resultant is a polynomial of their coefficients
2. The resultant is zero iff $f$ and $g$ share a common root
3. Practical criterion: the polynomials $f$ and $g$ share a root iff we can find polynomials $a, b$ such that $af+bg=0$ (i.e., if $0\in \langle f, g\rangle$ in terms of <a href="../ideal-generated-by-polynomials">polynomial ideals</a>)
4. We can determine such $a$ and $b$ using the Sylvester matrix of $f$ and $g$ 
5. We can show that $\det \operatorname{Syl}(f, g) = \operatorname{res}(f, g)$, so $f$ and $g$ have a common root iff $\det \operatorname{Syl}(f, g) = 0$.
6. This theory can be used to tell whether $f$ has a multiple root
7. We can extend this to multivariate polynomials and we can solve systems of polynomial equations. The procedure is reminiscent of what we do using Gröbner bases<sup>[<a href="#references">4</a>]</sup>


## 1. Definition

<p>Here we work with polynomials<sup>[<a href="#references">1</a>]</sup> over a field $F$, $f\in F[x]$, which are of the general form</p> 
<p>$$f(x) = \prod_{i=0}^{n}(x-\alpha_i) = a_0 + a_1x + \ldots + a_n x^n,$$</p>
<p>with $a_n\neq 0$ and $a_i\in F$.</p>

<div style="border-style:dashed;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="problem_statement">
    <p><strong>Problem statement.</strong> 
    Given two univariate polynomials $p$ and $q$ we want to know whether they share a root.  
    </p>    
</div>


<p>Of course we can factor both polynomials and check if they share a factor, but this is not efficient. If the polynomials are in a <a href="https://en.wikipedia.org/wiki/Euclidean_domain" target="_blank">Euclidean domain</a> (e.g., a field), then we can find their <abbr title="greatest commmon divider">gcd</abbr>. The method of resultants works for more general polynomials and is an efficient method. It also leads to Hilbert's nullstellensatz, which generalises this to multivariate polynomials.</p>

<p>Let us state the definition of the resultant of two polynomials<sup>[<a href="#references">2</a>]</sup></p>

<div style="border-style:solid;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="def1">
    <p><strong>Definition 1</strong> 
    Let $F$ be a field and $f, g\in F[x]$ are two polynomials with $\deg f=n$, $\deg g = m$, with </p>
    <p>$$\begin{aligned}f(x)={}& a_0+a_1x+\ldots+a_nx^n = a_n\prod_{i=1}^{n}x - \alpha_i \\ g(x)={}& b_0+b_1x + \ldots+b_mx^m = b_m\prod_{j=1}^{m}x - \beta_j.\end{aligned}$$</p>
    <p>Their <em>resultant</em> is a polynomial <em>of their coefficients</em> defined as </p>
    <p>$$\operatorname{res}(f, g) = a_n^m b_m^n \prod_{i,j}(\alpha_i- \beta_j),$$</p>
    <p>where $(\alpha_i)_i$ and $(\beta_j)_j$ are the roots of $f$ and $g$ respectively.</p>
</div>

<p>There are other equivalent ways to write the resultant:</p>
<p>$$\operatorname{res}(f, g) = a_n^m b_m^n \prod_{i=1}^n\prod_{j=1}^m\alpha_i- \beta_j,$$</p>
<p>and note that</p>
<p>$$g(\alpha_i) = b_m\prod_{j=1}^{m}\alpha_i - \beta_j,$$</p>
<p>therefore,</p>
<p>$$\operatorname{res}(f, g) = a_n^m \prod_{i=1}^{n}g(\alpha_i).$$</p>
<p>Likewise, we can see that</p>
<p>$$\operatorname{res}(f, g) = (-1)^{nm}b_m^n \prod_{j=1}^{m}f(\beta_j).$$</p>
<p>The resultant is, essentially, a product of the values of one polynomial evaluated at the roots of the other.</p>

<p>We can't use any of the above formulas unless we have factorised one of the two polynomials. Later, we'll give a formula that allows us to write the resultant as the determinant of a matrix.</p>

<div style="border-style:solid;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="prop1">
    <p><strong>Proposition 1: Common roots.</strong> 
    The polynomials $f$ and $g$ share a common root ($\exists \bar{x}: f(\bar{x}) = g(\bar{x}) = 0$) if and only if $\operatorname{res}(f, g)=0$.</p>
</div>


<p><em>Note.</em> This is true because the polynomials are defined over a field, which is an integral domain.</p>

<p><em>Proof.</em> (i) If $\bar{x}\in F$ is a common root of $f$ and $g$, then from the definition $\operatorname{res}(f, g) = 0$. Conversely, if $\operatorname{res}(f, g)=0$, and $\bar{x}$ is a root of $f$, then,</p>
<p>$$a_n^m \prod_{i=1}^{n}g(\alpha_i) = 0,$$</p>
<p>and $a_n \neq 0$. Since $F$ is a field, there is $i_0$ such that $g(\alpha_{i_0}) = 0$, where $\alpha_{i_0}$ is a root of $f$. $\Box$ </p>

## 2. Conditions so that $f$ and $g$ have a common root

<p>Here we will state a key result, which is a criterion for $f$ and $g$ to have a common root. We will then see the connection between this and the resultant.</p>

### 2.1. Key result

<p>The following result is a little reminiscent of the weak nullstellensatz, but it is stated for two univariate polynomials over a field, which <em>is not required to be algebraically closed</em>.</p>

<div style="border-style:solid;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="thm1">
    <p><strong>Theorem 1.</strong> 
    Let $F$ be a field, $f,g\in F[x]$, $\deg f = n > 0$, $\deg g = m > 0$. Then, $f$ and $g$ have a non-constant common factor iff there exist nonzero polynomials $a, b\in F[x]$ with $\deg a \leq m - 1$ and $\deg b \leq n-1$ such that </p>
    <p>$$af+bg=0$$</p>
</div>

<p><em>Proof.</em> ($\Rightarrow$) Suppose $f$ and $g$ have a common non-constant factor. Then, $f(x)=h(x)f_1(x)$ and $g(x)=h(x)g_1(x)$. We have that</p>
<p>$$0 = h(x)f_1(x)g_1(x) - h(x)f_1(x)g_1(x) = g_1(x)f(x) + (-f_1(x))g(x),$$</p>
<p>which means $a(x) = g_1(x)$ and $b(x) = -f_1(x)$, and $\deg a = \deg g_1 \leq m-1$ and $\deg b = \deg f_1 \leq n-1$.</p>

<p>($\Leftarrow$) Now suppose there exist $a$ and $b$ with $\deg a \leq m - 1$ and $\deg b \leq n-1$ such that $af+bg=0$. We will prove this by contradiction. Suppose $f$ and $g$ have no common factors. Then </p>
<p>$$\gcd(f, g)=1.$$</p>
<p>Equivalently, there are polynomials $p$ and $q$ such that</p>
<p>$$\begin{aligned}
&pf+qg=1
\\
\Rightarrow
\;&pfa+qga=a,
\end{aligned}$$</p>
<p>and using the fact that $fa=-bg$,</p>
<p>$$-pbg+qga=a \Rightarrow (-pb+qa)g=a.$$</p>
<p>From this we conclude that $\deg a \geq \deg g$; this is because $-pb+qa\neq 0$, and $\deg g = m$, so $\deg a \geq 0$. This is a contradiction because $\deg a$ was assumed to have a degree of no more than $m-1$. $\Box$</p>

### 2.2. Determination of $a$ and $b$

<p>Suppose that $f$ and $g$ have a non-constant common factor. We will determine polynomials $a$ and $b$ so that $af+bg=0$. We know $\deg a \leq m-1$ and $\deg b \leq n-1$, while $\deg f = n$ and $\deg g = m$. Therefore,</p>
<p>$$\begin{aligned}
f(x) {}={}& a_0 + a_1 x + \ldots + a_n x^n, \\
g(x) {}={}& b_0 + b_1 x + \ldots + b_m x^m, \\
a(x) {}={}& c_0 + c_1 x + \ldots + c_{m-1} x^{m-1}, \\
b(x) {}={}& d_0 + d_1 x + \ldots + d_{n-1} x^{n-1}.
\end{aligned}$$</p>
<p>From $af+bg = 0$ we have</p>
<p>$$\begin{aligned}
(c_0 + c_1 x + \ldots + c_{m-1} x^{m-1})(a_0 + a_1 x + \ldots + a_n x^n)\qquad\qquad
\\
+ (d_0 + d_1 x + \ldots + d_{n-1} x^{n-1})(b_0 + b_1 x + \ldots + b_m x^m) = 0.
\end{aligned}$$</p>
<p>By comparing the terms of equal order we have </p>
<p>$$\begin{aligned}
\text{degree }0:{}& c_0a_0 + d_0 b_0= 0,
\\
\text{degree }1:{}& c_1a_0 + a_1c_0 + d_0 b_1 + d_1b_0= 0,
\\
\text{degree }2:{}& c_1a_1 + c_2a_0 + c_0a_2 + d_1b_1 + d_2b_0 + d_0b_2= 0,
\end{aligned}$$</p>
<p>etc. By collecting the coefficients in a matrix we define $\operatorname{Syl}(f, g)\in\R^{n+m\times n+m}$, which is known as the Sylvester matrix</p>
<p id="eq_star">$$
\operatorname{Syl}(f, g) {}={}
\begin{bmatrix}
\textcolor{red}{a_n} & & & & b_m\\
\textcolor{red}{a_{n-1}} & a_{n} & & & b_{m-1} & b_m \\
\textcolor{red}{a_{n-2}} & a_{n-1} &  \ddots & & b_{m-2} & b_{m-1} & \ddots \\
\textcolor{red}{\vdots} & \vdots & \ddots & a_n & \vdots & \vdots & \ddots & \textcolor{blue}{b_m} \\
\textcolor{red}{a_0}  & a_1 & & &b_0 & b_1  \\
&a_0 & \ddots & \vdots &&b_0 & \ddots & \textcolor{blue}{\vdots} \\
&&\ddots& a_1 &&&\ddots& \textcolor{blue}{b_1} \\
&&& a_0 &&&& \textcolor{blue}{b_0}
\end{bmatrix}.\tag{*}$$</p>

<p>Van Der Waerden<sup>[<a href="#references">3</a>]</sup> <em>defines</em> the resultant to be...</p>

<div style="border-style:dashed;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="quote">
    <p><em>The resultant of two polynomials $f$, $g$ is a rational integral form in the coefficients of the form (<a href="#eq_star">*</a>). If the resultant vanishes, the polynomials f and g have either a common nonconstant factor, or the leading coefficient vanishes in both of them, and conversely.</em></p>    
</div>

<p>In fact, the matrix defined by Van Der Waerden<sup>[<a href="#references">3</a>]</sup> is the <em>transpose</em> of the above matrix. </p>

<p>A remarkable result is that the determinant of $\operatorname{Syl}(f, g)$ is $\operatorname{res}(f, g)$:</p>

<div style="border-style:solid;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="thm2">
    <p><strong>Theorem 2.</strong>
    $\det \operatorname{Syl}(f, g) = \operatorname{res}(f, g)$.</p>
</div>

This implies that $f$ and $g$ have a common nonconstant factor iff $\det \operatorname{Syl}(f, g) = 0$.

## 3. Further results

### 3.1. A corollary of Bézout's identity

<p>If $f$ and $g$ are relatively prime, we have that $\gcd(f, g)=1$. From Bézout's identity (which is valid for univariate polynomials over a field) we have that there exist polynomials $a$ and $b$ such that $af+bg=\gcd(f, g)$, that is,</p>
<p>$$af+bg=1.$$</p>
<p>If we combine this with the above result we conclude that </p>

<div style="border-style:solid;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="prop3">
    <p><strong>Proposition 3.</strong>
    For univariate polynomials $f$ and $g$ over a field, there always exist polynomials $a$ and $b$ such that </p>
    <p>$$af+bg=\operatorname{res}(f, g).$$</p>
</div>


<p>To put it simply, either there exist $a, b$ such that $af+bg=0$ (if $f$ and $g$ have a root in common), or there exist $a, b$ such that $af+bg=1$ (otherwise).</p>

### 3.2. Detection of multiple roots

<p>The resultant can be used to tell whether a given univariate polynomial has a multiple root.</p>

<p>A polynomial has a multiple root $\bar{x}$ if $\bar{x}$ is a root of both $f$ and $f'$, that is, if $\operatorname{res}(f, f') = 0$, or equivalently, if $\det \operatorname{Syl}(f, f') = 0$.</p>

<p><strong>Example 1 (quadratic).</strong> Let $f(x) = ax^2 + bx + c$; then $f'(x) = 2ax + b$. The Sylvester matrix is<p> 
<p>$$\operatorname{Syl}(f, f') = \left(\begin{array}{ccccc} a & 0 & 0 & 0 & 0\\ b & a & 0 & 0 & 0\\ c & b & a & 2\,a & 0\\ 0 & c & b & b & 2\,a\\ 0 & 0 & c & 0 & b \end{array}\right),$$</p>
<p>and its determinant is </p>
<p>$$\operatorname{res}(f, f') = \det \operatorname{Syl}(f, f') = -a^3(b^2-4ac) = -a^3\Delta,$$</p>
<p>where $\Delta = b^2-4ac$. Cool!</p>

<p><strong>Example 2 (cubic).</strong> Let $f(x) = ax^3+bx^2+cx+d$; then $f'(x) = 3ax^2 + 2bx + c$. The Sylvester matrix is </p>
<p>$$\operatorname{Syl}(f, f') 
{}={} 
\left(\begin{array}{ccccccc} 
a &  &  &  &  &  & \\ 
b & a &  &  &  &  & \\ 
c & b & a &  & 3a^2 &  & \\ 
d & c & b & a & 2b & 3a^2 & \\ 
  & d & c & b & c & 2b & 3a^2\\ 
  &  & d & c &  & c & 2b\\ 
  &  &  & d &  &  & c 
  \end{array}\right)$$</p>
<p>and, by taking the determinant (done in MATLAB)</p>
<p>$$\begin{aligned}\operatorname{res}(f,f')=a^3(27a^5d^2 - 36a^3bcd + 9a^3c^3 + 18a^2bcd \\ 
- 6a^2c^3 + 12ab^3d - 3ab^2c^2 + ac^3 - 8b^3d + 2b^2c^2).\end{aligned}$$</p>
<p>The result is simplified if we focus on the monic case ($a=1$) where</p>
<p>$$\operatorname{res}(f, f') = 4b^3d - b^2c^2 - 18bcd + 4c^3 + 27d^2.$$</p>


### 3.3. Multivariate polynomials 

<p>The key idea is that a ring of multivariate polynomials can be written as a standard polynomial ring because $R[x_1, x_2] = R[x_1][x_2]$, i.e., $R[x_1, x_2]$ is a the ring of polynomials in $x_2$ on the ring $R[x_1]$.</p>

<p><strong>Remark: Resultant of multivariate polynomial wrt a variable:</strong>
> Suppose $p,q\in R[x_1, \ldots, x_n] = R[x_1,\ldots, x_{n-1}][x_n]$, i.e., we can see $p$ and $q$ as polynomials in $x_n$ with coefficients from $R[x_1,\ldots, x_{n-1}]$. We denote the resultant of $p$ and $q$ with respect to $x_n$ as $\operatorname{res}_{x_n}(p, q)$. This is an element of $R[x_1,\ldots, x_{n-1}]$. Likewise, we define the Sylvester matrix of $(p, q)$ with respect to $x_n$ by $\operatorname{Syl}_{x_n}(p, q)$.</p>

<p><strong>Example.</strong> Suppose $p(x, y) = 2x^2y + xy + y^2x - 1$ and $q(x, y) = y^2-3xy+1$. Then, by treating $p$ and $q$ as polynomials of $y$, we can write</p>
<p>$$\begin{aligned}
p(y) = xy^2 + x(2x+1)y + 1,
\\
q(y) = y^2 -3xy + 1.
\end{aligned}$$</p>
<p>Therefore, the Sylvester matrix wrt $y$ is a $4\times 4$ matrix</p>
<p>$$\operatorname{Syl}_y(p, q) = 
\left(\begin{array}{cccc} x &  & 1 & \\ 
x\,\left(2\,x+1\right) & x & -3\,x & 1\\ 
-1 & x\,\left(2\,x+1\right) & 1 & -3\,x\\  
& -1 &  & 1 \end{array}\right).$$
and 
$$\operatorname{res}_y(f, g) 
{}={} 
\det \operatorname{Syl}_y(p, q) 
{}={} 
10x^4 - 8x^3 - x^2 + 2x + 1.$$</p>
<p>Likewise, we can determine the resultant with respect to $x$; it is $p(x)=2yx^2 + y(y+1)x-1$, $q(x)=-3yx + 1+y^2$, so</p> 
<p>$$\operatorname{Syl}_x(p, q) = \left(\begin{array}{ccc} 2\,y & -3\,y & 0\\ y\,\left(y+1\right) & y^2+1 & -3\,y\\ -1 & 0 & y^2+1 \end{array}\right),$$</p>
<p>and</p>
<p>$$\operatorname{res}_x(p, q) = 5y^5 + 3y^4 + 7y^3 - 6y^2 + 2y.$$</p>

<p><strong>Example (Polynomial system).</strong> 
Suppose we have to solve the polynomial system</p>
<p>$$\begin{aligned}
2x^2y + xy + y^2x - 1 {}={}& 0,\\
y^2-3xy+1 {}={}& 0.
\end{aligned}$$</p>
<p>By going through all roots of $\operatorname{res}_y(f, g)$ and $\operatorname{res}_x(f, g)$ and taking all combinations of roots, we fine that a solution of the above system is </p>
<p>$$\begin{aligned}
(x^\star_{1,2}, y^\star_{1,2}) 
{}={}& 
(0.7173 \pm 0.3795i, 0.3446 \mp 0.2682i),
\\
(x_{3,4}^\star, y_{3,4}^\star) 
{}={}& 
(-0.3173 \pm 0.2263i,  -0.6446 \pm 1.2970i).
\end{aligned}$$</p>

<p><strong>Note:</strong>
In $R[x, y]$, the resultant with respect to $y$ is </p>
<p>$$\operatorname{res}_y: R[x, y] \times R[x, y] \to R[x]$$</p>
<p>More generally,</p>
<p>$$\operatorname{res}_{x_i}: R[x_1, \ldots, x_n] \times R[x_1, \ldots, x_n] \to R[x_1,\ldots, x_{i-1}, x_{i+1}, \ldots, x_n]$$</p>


## Endnotes

<p>There must be a relationship between Gröbner bases and resultants; for example Wiesinger-Widi<sup>[<a href="#references">5</a>]</sup> says</p>

> There are basically two approaches for computing a Gröbner basis. 
> The first is the one pursued by the Buchberger algorithm: We start from the initial set $F$, execute certain reduction steps (consisting of multiplication of polynomials by terms — called shifts — and subtraction of polynomials) and due to Buchberger's theorem, which says that the computation is finished if all the s-polynomials reduce to zero, we know that after finitely many iterations of this procedure we obtain a Gröbner basis of the <a href="../ideal-generated-by-polynomials">ideal</a> generated by $F$. 
> 
> **The second approach** is to start from $F$, execute certain shifts of the initial polynomials in $F$, arrange them as rows in a matrix, triangularize this matrix and from the resulting matrix extract a Gröbner basis.

<p>Another discussion of the relationship between Gröbner bases and resultants is given by Marko Roczen<sup>[<a href="#references">6</a>]</sup> where a connection between Hilbert's nullstellensatz and resultants is established (Section 2 therein). </p>

<p>The book of Gelfand et al.<sup>[<a href="#references">7</a>]</sup> is  is dedicated to resultants and discriminants of multivariate polynomials. </p>


## References

1. A good abstract algebra book that covers polynomials is: Thomas W. Judson, [Abstract algebra: theory and applications](http://abstract.ups.edu/download/aata-20220728-print.pdf). [[Ring#Integral domain|Integral domains]] are also of interest because $ab=0$ implies $a=0$ or $b=0$ and this allows to establish $\deg(pq)=\deg p + \deg q$ for $p, q\in F[x]$ (which is not generally true).
2. H Woody, [Polynomial resultants](http://buzzard.ups.edu/courses/2016spring/projects/woody-resultants-ups-434-2016.pdf), notes, accessed on 28 June 2024
3. Van Der Waerden, Algebra, vol I, Springer, 1991.
4. D.A. Cox, J. Little, D. O' Shea, Ideals, Varieties, and Algorithms: An Introduction to Computational Algebraic Geometry and Commutative Algebra, 4th Edition, Springer, 2015
5. Manuela Wiesinger-Widi, Gröbner Bases and Generalized Sylvester Matrices, ACM Communications in Computer Algebra, Vol. 45, No. 2, Issue 176, June 2011, [link](https://dl.acm.org/doi/pdf/10.1145/2016567.2016594) 
6. Marko Roczen, Gröbner Bases and Resultants, Autumn School on Commutative Algebra and Combinatorics, Universitiy of Constanta, Eforie 1999, [link](https://www2.mathematik.hu-berlin.de/~roczen/papers/eforie2.pdf), accessed on 31 July 2024
7. I.M. Gelfand, M.M. Kapranov, and A.V. Zeleninsky, Discriminants, resultants, and multidimensional determinants, Modern Birkhäuser Classics, 2008, [link](https://www.maths.ed.ac.uk/~v1ranick/papers/gelkapzel.pdf) 

