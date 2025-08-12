---
author: "Pantelis Sopasakis"
title:  "What is a Gröbner basis and how to make one"
date: 2025-08-11
description: "Gröbner bases, S-polynomials, Buchberger's criterion and algorithm"
summary: "Gröbner bases, S-polynomials, Buchberger's criterion and algorithm"
math: true
series: ["Mathematix"]
tags: ["Algebra"]
collapsible: true
---


**Read first:** <a href="../hilbert-basis-theorem">Hilbert's basis theorem</a>

In this post we define the notion of a Gröbner basis of a polynomial ideal, which is an ideal basis possessing favourable properties related to multivariate polynomial division. It is a notion of central importance in algebraic geometry. Good reading material is this book[^1] and this thesis[^2].

## Gröbner bases

### Definitions

A Gröbner basis is a "good" basis of a <a href="../ideal-generated-by-polynomials">polynomial ideal</a> and it helps give a solution to the <span id="imp">ideal membership problem</span>, which is stated as follows:

> **Ideal membership problem:**
> Given an ideal $I$ and a polynomial $f$, we need to tell whether $f\in I$ (in a computationally tractable manner).
<!-- ^8b2268 -->

Let us give the definition of a Gröbner basis:

<div style="border-style:solid;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="def1">
    <p><strong>Definition 1: Gröbner Basis.</strong> 
    Let $I$ be a polynomial ideal and fix a <a href="../monomial-orderings">monomial ordering</a>. A set of polynomials $\{g_1, \ldots, g_t\}$ is called a Gröbner basis of $I$ if </p>
    <p>$$\langle \mathrm{lt}(I)\rangle = \langle \mathrm{lt}(g_1), \ldots, \mathrm{lt}(g_t)\rangle.$$</p>   
</div>

**A Gröbner basis exists** as we saw in the <a href="../hilbert-basis-theorem/#proof-hilberts-basis-theorem">proof of Hilbert's basis theorem</a> (and it is a basis of $I$; see <a href="#lem1">Lemma 1</a> below). 

<p>Recall that in general, for $g_1, \ldots, g_t\in I$,</p>
<p>$$\langle \mathrm{lt}(g_1), \ldots, \mathrm{lt}(g_s)\rangle \subseteq \langle \mathrm{lt}(I)\rangle.$$</p>

<p>Let's show that a Gröbner basis is indeed a basis.</p>

<div style="border-style:solid;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="lem1">
    <p><strong>Lemma 1: Gröbner Basis is a Basis.</strong> 
    A Gröbner basis $G$ of an ideal $I$ is a basis of $I$.</p>   
</div>
<!-- ^078410 -->

<p><em>Proof.</em>  Let $G=\{g_1, \ldots, g_t\}$ be a Gröbner basis. Evidently, since $G\subseteq I$, $\langle G\rangle \subseteq I$. Pick an $f\in I$. By the <a href="../division-multivariate-polynomials/">multivariate division</a> algorithm,</p>
<p>$$f = a_1g_1 + \ldots + a_tg_t + r,$$</p>
<p>for some $a_i\in k[x_1, \ldots, x_n]$ and no monomials of $r$ are divisible by the <a href="../monomial-orderings/#multidegree-and-leading-monomial">leading terms</a> of any $g_i$. Suppose $r\neq 0$. Then, $r = f - a_1g_1 - \ldots - a_tg_t,$ so $r\in I$. This means that</p>
<p>$$\mathrm{lt}(r) \in \mathrm{lt}(I)\subseteq \langle \mathrm{lt}(I)\rangle = \langle G\rangle,$$</p>
<p>where the last equality is because $G$ is a Gröbner basis. The key here is that $\langle G\rangle$ is a <a href="../monomial-ideals">monomial ideal</a> and the monomial ideal membership problem is easy to decide according to <a href="../monomial-ideals/#prop1">this proposition</a>!  </p>

<p>Let's see what we have: we know that the <b>monomial</b> $\mathrm{lt}(r)$ belongs to the monomial ideal $\langle G\rangle$, which means that a monomial of $\langle G\rangle$ divides it. This is impossible! $\Box$</p>

### Exercises

These are exercises from[^1].

<div style="border-style:dashed;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="ex1">
    <p><strong>Exercise 1, Section 5, p81.</strong> 
    Let $I=\langle g_1, g_2, g_3\rangle\subseteq\R[x, y, z]$ where $g_1 = xy^2 - xz + y$, $g_2=xy - z^2$, and $g_3=x - yz^4$. Using the <a href="../monomial-orderings/#lex">lex</a> ordering, give an example of a $g\in I$ such that $\mathrm{lt}(g) \notin \langle \mathrm{lt}(g_1), \mathrm{lt}(g_2), \mathrm{lt}(g_3)\rangle$.</p>
</div>


<p><em>Solution.</em> Using the <a href="../monomial-orderings/#lex">lex</a> ordering, the leading terms of $g_1, g_2, g_3$ are $\mathrm{lt}(g_1)=xy^2$, $\mathrm{lt}(g_2)=xy$, and $\mathrm{lt}(g_3)=x$. We need to come up with a $g\in I$ such that the monomial $\mathrm{lt}(g)$ is not divisible by any of $\mathrm{lt}(g_1), \mathrm{lt}(g_2), \mathrm{lt}(g_3)$. Actually, it suffices that $\mathrm{lt}(g)$ be not a polynomial in $x$. To pick a $g\in I$ we do </p>
<p>$$g = a_1g_1 + a_2 g_2 + a_3g_3.$$</p>
<p>If we choose $a_1=1$, $a_2 = -y$ and $a_3=z$ we have $g = - yz^5 + yz^2 + y$ and $\mathrm{lt}(g) = - yz^5$, which is not divisibly by $xy^2$, $xy$, or $x$. </p>

<p>This can be confirmed by the following MATLAB code using our <a href="https://github.com/alphaville/algebraic-geometry">MATLAB algebraic geometry toolbox</a></p>

```matlab
ord = @(s1, s2) lex(s1, s2);

[x, y, z] = polysymbols({'x', 'y', 'z'}, ord);

g1 = x*y^2 - x*z + y;
g2 = x*y - z^2;
g3 = x - y*z^4;

g = g1 - y*g2 + z*g3;
lt_g = g.leadTermAsPolynomial
lt_gi  = {...
    g1.leadTermAsPolynomial, ...
    g2.leadTermAsPolynomial, ...
    g3.leadTermAsPolynomial};

[q, r] = lt_g.euclideanDivision(lt_gi);
```

<p>The nonzero remainder of $r=-yz^5$ proves that $g$ is such a polynomial. $\bullet$ </p>

<div style="border-style:dashed;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="ex3a">
    <p><strong>Exercise 3a, Section 5, p81.</strong> 
    To generalise the situation of the previous exercise, suppose $I=\langle f_1, \ldots, f_s\rangle$ is an ideal such that</p> 
    <p>$$\langle \mathrm{lt}(f_1), \ldots, \mathrm{lt}(f_s)\rangle \subsetneq \langle \mathrm{lt}(I)\rangle.$$</p>
    <p>Prove that there is some $f \in I$ whose remainder on division by $f_1,\ldots, f_s$ is nonzero.</p>
</div>

<p><em>Solution.</em> There is an $f\in I$ such that 
$$\mathrm{lt}(f) \notin \langle \mathrm{lt}(f_1), \ldots, \mathrm{lt}(f_s)\rangle,$$
and note that $\mathrm{lt}(f)$ is a monomial and $\langle \mathrm{lt}(f_1), \ldots, \mathrm{lt}(f_s)\rangle$ is a monomial ideal. From <a href="../monomial-ideals/#prop1">this proposition</a>, we conclude that $\mathrm{lt}(f)$ is not divisible by any of $\mathrm{lt}(f_1), \ldots, \mathrm{lt}(f_s)$, so from the <a href="../division-multivariate-polynomials/#a-multivariate-division-algorithm">multivariate division algorithm</a> the remainder of the division of $f$ by $f_1, \ldots, f_s$ is nonzero. $\bullet$</p>

<div style="border-style:dashed;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="ex3b">
    <p><strong>Exercise 3b, Section 5, p81.</strong> 
    What does part (a) say about the ideal membership problem?</p>
</div>

<p><em>Solution.</em> If $I=\langle g_1, \ldots, g_t\rangle$ and $\{g_1, \ldots, g_t\}$ is a Gröbner basis, then we can tell whether $f\in I$ by performing a <a href="../division-multivariate-polynomials/">multivariate division</a> by the generating polynomials. If the remainder is zero, then $f\in I$. $\bullet$ </p>

<div style="border-style:dashed;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="ex4">
    <p><strong>Exercise 4, Section 5, p82.</strong> 
    Let $I$ be an ideal in $k[x_1, \ldots, x_n]$. Show that</p>
    <p>$$\langle \mathrm{lt}(g); g\in I\setminus \{0\}\rangle {}={} \langle \operatorname{lm}(g); g\in I\setminus \{0\}\rangle.$$</p>
</div>

<em>Solution.</em> This is because 
<p>$$\begin{aligned}
& f \in \langle \mathrm{lt}(g); g\in I\setminus \{0\}\rangle
\\
\Rightarrow{} &
f = \sum_{i=1}^{m}a_i \mathrm{lt}(g_i), a_i\in k[x_1,\ldots, x_n], g_i\in I
\\
\Rightarrow{} &
f = \sum_{i=1}^{m}a_i \operatorname{lc}(g_i)\operatorname{lm}(g_i) 
= \sum_{i=1}^{m}\tilde{a}_i \operatorname{lm}(g_i),
\end{aligned}$$</p>
<p>where $\tilde{a}_i = a_i \operatorname{lc}(g_i)$, so </p>
<p>$$f\in \langle \operatorname{lm}(g); g\in I\setminus \{0\}\rangle.$$</p>
<p>The converse is also simple. $\bullet$ </p>


<div style="border-style:dashed;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="ex5">
    <p><strong>Exercise 5, Section 5, p82.</strong> 
     Let $I$ be an ideal in $k[x_1, \ldots, x_n]$. Show that $G=\{g_1, \ldots, g_t\}\subseteq I$ is a Gröbner basis of $I$ if and only if the leading term of every element of $I$ is divisible by one of $\mathrm{lt}(g_i)$.</p>
</div>


<p><em>Solution.</em> (1) Suppose that $G=\{g_1, \ldots, g_t\}$ is a Gröbner basis of $I$ and $f\in I$. I want to show that $\mathrm{lt}(f)$ is divisible by one of $\mathrm{lt}(g_i)$. The fact that $\{g_1, \ldots, g_t\}$ is a Gröbner basis implies that</p>
<p>$$\mathrm{lt}(f) = a_1 \mathrm{lt}(g_1) + \ldots + a_t \mathrm{lt}(g_t),$$</p>
<p>Let us divide by each of $\mathrm{lt}(g_i)$ separately. This means that $\mathrm{lt}(f) \in \langle \mathrm{lt}(g_1), \ldots, \mathrm{lt}(g_t)\rangle$ and the latter is a <a href="../monomial-ideals">monomial ideal</a>, so from <a href="../monomial-ideals/#prop1">this proposition</a> it is divisible by one of $\mathrm{lt}(g_i)$.</p>

<p>(2) For the converse, suppose that for every $f\in I$, $\mathrm{lt}(f)$ is divisible by an $\mathrm{lt}(g_i)$.</p>

<p>To show that $G$ is a Gröbner basis, we need to show that $\langle \mathrm{lt}(I)\rangle = \langle \mathrm{lt}(g_1), \ldots, \mathrm{lt}(g_t)\rangle$. It suffices to show that $\langle \mathrm{lt}(I)\rangle \subseteq \langle \mathrm{lt}(g_1), \ldots, \mathrm{lt}(g_t)\rangle$.</p>

<p>Let $f$ be such that $\mathrm{lt}(f)\in \langle \mathrm{lt}(I)\rangle$. This means</p> 
<p>$$\begin{aligned}
\mathrm{lt}(f) ={}& a_1 \mathrm{lt}(f_1) + \ldots + a_m \mathrm{lt}(f_m) 
\\
={}& a_1h_1\mathrm{lt}(g_1) + \ldots + a_m h_m \mathrm{lt}(g_m) 
\\
{}\in{}& \langle \mathrm{lt}(g_1), \ldots, \mathrm{lt}(g_m)\rangle
\end{aligned}$$</p>

<p>This completes the proof. $\Box$</p>


### Properties

<p>First and foremost, a Gröbner basis for an ideal, $G=\{g_1, \ldots, g_t\}$, behaves nicely as a divisor: when we <a href="../division-multivariate-polynomials">divide</a> a polynomial $f$ by $(g_1, \ldots, g_t)$ the remainder is the same regardless of the order of the $g_i$. </p>

<div style="border-style:solid;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="thm2">
    <p><strong>Theorem 2: Gröbner bases and division.</strong> 
     Let $I$ be a <a href="../ideal-generated-by-polynomials">polynomial ideal</a> and $G=\{g_1, \ldots ,g_t\}$ be a Gröbner basis for $I$. Then, given $f\in k[x_1, \ldots, x_n]$ there is a <em>unique</em> $r\in k[x_1, \ldots, x_n]$ with the following properties:</a>
        <p>1. No term of $r$ is divisible by any of $\mathrm{lt}(g_1), \ldots, \mathrm{lt}(g_t)$</p>
        <p>2. There is $g \in I$ with $f= g + r$</p>
     <p>Additionally, the remainder, $r$, is independent of the order of the $g_i$. This remainder is denoted by $r = \bar{f}^{G}$ (see also <a href="#def2">Definition 2</a>).</p>
</div>
<!-- ^11c761 -->


<p><em>Proof.</em>  If we <a href="../division-multivariate-polynomials">divide</a> $f$ by $(g_1, \ldots, g_t)$ we get </p>
<p>$$f = a_1g_1 + \ldots + a_tg_t + r,$$</p>
<p>where none of the monomials of $r$ is divisible by any of  $\mathrm{lt}(g_1), \ldots, \mathrm{lt}(g_t)$. Let $g = a_1g_1 + \ldots + a_tg_t\in I$. Then, $f = g + r$. </p>

<p>It remains to prove that $r$ is unique: Suppose that $f = g + r = g' + r'$ such that condition (1) is satisfied. Then, $r-r' = g' - g \in I$. Suppose $r\neq r'$. Then,</p>
<p>$$\mathrm{lt}(r-r') \in \mathrm{lt}(I) \in \langle  \mathrm{lt}(I) \rangle = \langle \mathrm{lt}(g_1), \ldots, \mathrm{lt}(g_t)\rangle.$$</p>
<p>By <a href="../monomial-ideals/#prop1">this proposition</a>, $\mathrm{lt}(r-r')$ is divisible by at least one of $\mathrm{lt}(g_i)$. This is a contradiction, as neither none of the terms of $r$ nor $r'$ are divisible by any of $\mathrm{lt}(g_i)$ — see also <a href="#e3bed7">this clarification</a>. $\Box$</p>

<p><b>Note: Non-uniqueness of quotients.</b>
 Although the remainder, $r$, is unique as we showed in <a href="#thm2">Theorem 2</a>, the quotients, $a_1, \ldots, a_t$, depend on the order of division.</p>

<p><b>Example:</b> Consider the polynomial ideal $I=\langle f_1, f_2\rangle$ of ${\rm I\!R}[x, y]$ where $f_1 = x - y^4$ and $f_2 = xy - yx^2$ and the lex monomial ordering. A Gröbner basis for this ideal is $G = \{g_1, g_2, g_3\}$ with $g_1 = x - y^4$, $g_2 = x^2y - xy$, and $g_3 = y^9 - y^5$.
Take the polymomial $f = x^2 - xy^4 + xy^2 + x - x^2y^2$ (chosen arbitrarily). The remainder of the division of $f$ by $G$ (in any order) is $r=y^4$. However, if we divide by $(g_1, g_2, g_3)$ we get</p>
<p>$$f = (- xy^2 + x - y^6 + y^2 + 1)g_1 + 0g_2 + (-y)g_3 + r,$$</p>
<p>whereas if we divide by $(g_2, g_3, g_1)$ we get</p>
<p>$$f = (-y)g_2 + 0g_3 + (x + 1)g_1 + r.$$</p>
<p>Here is a code snippet in MATLAB using <a href="https://github.com/alphaville/algebraic-geometry">our algebraic geometry toolbox</a>:</p>

```matlab
ord = @(s1, s2) lex(s1, s2);
vars = {'x', 'y'};
[x, y] = polysymbols(vars, ord);
id = MultivariatePolynomial.id(vars, ord);

f1 = x - y^4; f2 = x*y - y*x^2;

I = PolynomialIdeal({f1, f2});
I.grobnerBasis(true);
G = I.grobner;

f = x^2 - x*y^4 + x*y^2 + x - x^2*y^2;
[q, r] = f.euclideanDivision({G{[1 2 3]}}) ;
```


<p>Gröbner bases solve the <a href="#imp">ideal membership problem</a>. Let us state this as a theorem.</p>

<div style="border-style:solid;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="thm3">
    <p><strong>Theorem 3: Decision of ideal membership.</strong> 
     Let $G= \{g_1, \ldots, g_t\}$ be a Gröbner basis for an ideal $I$ and let $f\in k[x_1, \ldots, x_n]$ be a polynomial. Then, $f\in I$ iff the remainder of the division of $f$ with the polynomials of $G$ is zero.</p>
</div>
<!-- ^027a41 -->

<p><em>Proof.</em>  If the remainder is zero, then $f=a_1g_1 + \ldots + a_tg_t$, so, evidently, $f\in I$. Conversely, if $f\in I$ then $f=f+0$ satisfies the conditions of <a href="#thm2">Theorem 2</a>, and due to the uniqueness of the remainder, zero is <em>the</em> remainder. $\Box$</p>

<p>Here is an example using our <a href="https://github.com/alphaville/algebraic-geometry">MATLAB algebraic geometry toolbox</a>:</p>

```matlab
ord = @(s1, s2) lex(s1, s2);
vars = {'x', 'y'};
[x, y] = polysymbols(vars, ord);
id = MultivariatePolynomial.id(vars, ord);

f1 = x - y^4; f2 = x*y - y*x^2;

I = PolynomialIdeal({f1, f2});

f = (x^2 + y^2 + 3*id) * f1 + x*y*f2;
is_member = I.ismember(f) % is true
```

<p>The ideal membership problem is decided by dividing $f$ by a Gröbner basis of $I$ (computed internally).</p>

### More Exercises

<p>Exercise 1 is a generalisation of <a href="#thm2">Theorem 2</a>:</p>

<div style="border-style:dashed;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="ex1a">
    <p><strong>Exercise 1a, Section 2.6, p. 88-89.</strong> 
     Let $I\subseteq k[x_1, \ldots, x_n]$ be an ideal and fix a <a href="../monomial-orderings">monomial ordering</a>. Let $f$ be a polynomial. Then $f$ can be written in the form $f=g+r$, where $g\in I$ and none of the terms of $r$ is divisible by any elements of $\mathrm{lt}(I)$. </p>
</div>
<!-- ^b9c39e -->

<p><em>Solution.</em> Let $g$ and $r$ be as in <a href="#thm2">Theorem 2</a> where we showed that the terms of $r$ are not divisible by any of $\mathrm{lt}(g_1), \ldots, \mathrm{lt}(g_t)$. We claim that the same $g$ and $r$ satisfy the conditions stated here.</p>

<p>Suppose $r_0$ is a term of $r$ (a monomial term). Aiming at a contradiction, suppose $r_0$ is divisible by an element of $\mathrm{lt}(I)$, i.e., $r_0 = b \mathrm{lt}(h)$ for some $h\in I$ and some polynomial $b\in k[x_1, \ldots, x_n]$. Note that</p>
<p>$$\begin{aligned}
\mathrm{lt}(f) \in \langle \mathrm{lt}(I)\rangle = \langle \mathrm{lt}(g_1), \ldots, \mathrm{lt}(g_t)\rangle,
\end{aligned}$$</p>
<p>therefore,</p>
<p>$$r_0 \in \langle \mathrm{lt}(I)\rangle = \langle \mathrm{lt}(g_1), \ldots, \mathrm{lt}(g_t)\rangle.$$</p>
<p>From <a href="../monomial-ideals/#prop1">this proposition</a>, $r_0$ is divisible by one of $\mathrm{lt}(g_i)$, which is a contradiction.</p>

<p>Note that we limited our search to decompositions of the form $f = g+r$ where $r$ satisfies the conditions of <a href="#thm2">Theorem 2</a>; this is necessary because we're trying to prove something stronger. $\bullet$ </p>

<div style="border-style:dashed;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="ex1b">
    <p><strong>Exercise 1b, Section 2.6, p. 88-89.</strong> 
     Show that the remainder in <a href="#ex1a">Exercise 1a</a> is unique.</p>
</div>
<!-- ^8c9217 -->

<p><em>Solution.</em> We'll show this building up on <a href="#ex1a">Exercise 1a</a>, although uniqueness is already implied by <a href="#thm2">Theorem 2</a>. Suppose $f=g+r=g'+r'$ where the pairs $(g, r)$ and $(g', r')$ satisfy the conditions of Exercise 1a. Then $r-r' = g'-g\in I$, so $\mathrm{lt}(r-r')\in \mathrm{lt}(I)\subseteq \langle \mathrm{lt}(I)\rangle$, which is a monomial ideal; see again the definition of $\mathrm{lt}(I)$ <a href="../hilbert-basis-theorem/#leading-terms">here</a>. From <a href="../monomial-ideals/#prop1">this proposition</a>, this means that $\mathrm{lt}(q)$ divides $\mathrm{lt}(r-r')$ for some $q\in I$, which is impossible because $\mathrm{lt}(q)$ cannot divide any of the terms of $r$ or $r'$. $\bullet$</p>

<p>We will see now a proof that the definition is independent of the choice of Gröbner basis.</p>

<div style="border-style:dashed;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="ex4-2">
    <p><strong>Exercise 4, Section 2.6, p. 89.</strong> 
     Let $G$ and $G'$ be Gröbner bases of an ideal $I$ with respect to some monomial order in $k[x_1, \ldots, x_n]$. Show that $\overline{f}^{G}=\overline{f}^{G'}$ for every $f\in k[x_1, \ldots, x_n]$.</p>
</div>
<!-- ^d11a7b -->

<p><em>Solution.</em> From <a href="#ex1a">Exercise 1a</a> and <a href="#ex1b">Exercise 1b</a> we can write $f=g+r$ where $g\in I$ and $r$ is the unique polynomial whose terms are not divisible by any of the monomials of $\mathrm{lt}(I)$. Note here that if $m$ is a <em>monomial</em> in $\langle \mathrm{lt}(I)\rangle$ then $m$ is divisible by an $\mathrm{lt}(\phi)$, $\phi\in I$. We conclude that the terms of $r$ are not divisible by any element of $\langle \mathrm{lt}(I)\rangle$. Note that</p> 
<p>$$\langle \mathrm{lt}(I)\rangle = \langle \mathrm{lt}(g_1), \ldots, \mathrm{lt}(g_t)\rangle = \langle \mathrm{lt}(g_1'), \ldots, \mathrm{lt}(g_s')\rangle,$$</p>
<p>so $r$ is not divisible by any of $\mathrm{lt}(g_1), \ldots, \mathrm{lt}(g_t)$ or $\mathrm{lt}(g_1'), \ldots, \mathrm{lt}(g_s')$ and since it is unique, it is the same remainder regardless of the choice of Gröbner basis. $\Box$</p>

> **Conclusion:**
> We can talk about the remainder of the division of $f\in k[x_1, \ldots, x_n]$ by an **ideal** $I$; what we'll mean by this is that if $G$ is a Gröbner basis of $I$,
> $$\overline{f}^I = \overline{f}^G.$$
> As we saw in <a href="#ex4-2">Exercise 4</a>, the remainder is independent of the choice of Gröbner basis.


<div style="border-style:dashed;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="ex13a">
    <p><strong>Exercise 13a, Section 2.6, p. 89.</strong> 
     Let $I\subseteq k[x_1, \ldots, x_n]$ be an ideal and let $G$ be a Gröbner basis for $I$. Show that $\overline{f}^G=\overline{g}^G$ iff $f-g\in I$.</p>
</div>
<!-- ^f9bf0b -->

<p><em>Solution.</em> From <a href="#ex1a">Exercise 1a</a>, we can write $f = q_f + \overline{f}^G$ and  $g = q_g + \overline{g}^G$, where $q_f, q_g\in I$. If we subtract by parts we get 
$$f - g = (q_f - q_g) + (\overline{f}^G - \overline{g}^G).\tag{1}$$
Firstly, suppose that $\overline{f}^G = \overline{g}^G$. Then, from Equation (1) $f-g=q_f-q_g\in I$.</p>

<p>For the converse, suppose that $f-g\in I$. Then, from Equation (1) we see that $\overline{f}^G - \overline{g}^G = (f-g) - (q_f - q_g)$ and since $f-g\in I$ and $q_f, q_g\in I$ we conclude $\overline{f}^G - \overline{g}^G\in I$. Now $\overline{f}^G - \overline{g}^G$ must be equal to zero and here is why: firstly,</p> 
<p>$$\overline{f}^G - \overline{g}^G\in I \Rightarrow \mathrm{lt}(\overline{f}^G - \overline{g}^G) \in \mathrm{lt}(I).\tag{2}$$</p>
<p>From <a href="#ex1a">Exercise 1a</a>, none of the terms of $\overline{f}^G$ and $\overline{g}^G$ are divisible by any of the elements of $\mathrm{lt}(I)$, therefore, similar to our argument in <a href="#thm2">Theorem 2</a>, it must be $\overline{f}^G - \overline{g}^G = 0$. </p>

<p id="e3bed7">Let us clarify this argument:</p>

> **Clarification:**
> Suppose $p, q\in k[x_1, \ldots, x_n]$ are polynomials and they have the form 
> $$p = \sum_{i\in \mathcal{I}}a_ix^i, q = \sum_{j\in \mathcal{J}}b_j x^j.$$
> Then, the terms of $p\pm q$ are some of the terms $\{x^\iota\}_{\iota \in I\cup J}$ and $\mathrm{lt}(p\pm q)$ is one of these. As a result, if $s\in k[x_1, \ldots, x_n]$ is such that $\mathrm{lt}(s)$ does not divide *any* of these terms, it does not divide $\mathrm{lt}(p \pm q)$. This is the argument we made above.
<!-- ^e3bed7 -->

<p>This completes the proof. $\bullet$</p>

<div style="border-style:dashed;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="ex13b">
    <p><strong>Exercise 13b, Section 2.6, p. 89.</strong> 
     Let $I\subseteq k[x_1, \ldots, x_n]$ be an ideal and let $G$ be a Gröbner basis for $I$. Show that </p>
     <p>$$\overline{f+g}^G = \overline{f}^G + \overline{g}^G.$$</p>
</div>

<p><em>Solution.</em> Suppose $f+g = q + r$ with $q\in I$ and no terms of $r$ are divisible by any element of $\mathrm{lt}(I)$. Likewise, $f = q_f + r_f$, $g = q_g + r_g$ and no terms of $r_f$ or $r_g$ are divisible by an element of $\mathrm{lt}(I)$. We have</p>
<p>$$q_f +q_g + r_f + r_g = q + r \Rightarrow r - (r_f + r_g) \in I,$$</p>
<p>and this implies that</p>
<p>$$\mathrm{lt}(r - r_f - r_g) \in \mathrm{lt}(I),$$</p>
<p>so as we did before, we conclude that $r = r_f + r_g$. $\bullet$</p>

<div style="border-style:dashed;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="ex13c">
    <p><strong>Exercise 13c, Section 2.6, p. 89-90.</strong> 
     Let $I\subseteq k[x_1, \ldots, x_n]$ be an ideal and let $G$ be a Gröbner basis for $I$. Show that</p>
     <p>$$\overline{fg}^G = \overline{\overline{f}^G {}\cdot{} \overline{g}^G}^G.$$</p>
</div>

<p><em>Solution.</em> Here is a neat proof: Let $f = q_f + r_f$ and $g = q_g + r_g$. Then,
$$fg = (q_f + r_f)(q_g + r_g) \in r_f r_g + I,$$
so $fg - r_f r_g \in I$, so from <a href="ex13a">Exercise 13a</a> $\overline{fg}^G = \overline{r_fr_g}^G$, which completes the proof. $\Box$</p>


## Buchberger's criterion

Buchberger's criterion allows us to tell whether a given basis is a Gröbner basis. Before we can state the criterion, we need to introduce a few definitions.

Good references for this topic are[^1] and[^3].

<div style="border-style:solid;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="def2">
    <p><strong>Definition 2: Remainder.</strong> 
    Let $f\in k[x_1, \ldots, x_n]$ and $F=(f_1, \ldots, f_s)$ is a tuple of polynomials. We denote the remainder of <a href="../division-multivariate-polynomials/">multivariate division</a> of $f$ by $F$ as $\overline{f}^F$.</p>   
</div>

<p>Note that if $F$ is a <a href="#def1">Gröbner basis</a> then the order of the elements of $F$ <a href="#thm2">is not important</a>, so we can treat $F$ as a set. </p>

<p>The remainder, $\overline{f}^F$ is also called the <b>normal form</b> of $f$.</p>

<p>We <a href="#thm3">have shown</a> that if $G$ is a <a href="#def1">Gröbner basis</a> for $I$ then $f\in I$ iff $\overline{f}^G = 0$. </p>


<div style="border-style:solid;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="def3">
    <p><strong>Definition 3: LCM.</strong> 
    Let $f,g\in k[x_1, \ldots, x_n]$ be two polynomials with <a href="../monomial-orderings#multidegree-and-leading-monomial">multidegrees</a> $\alpha$ and $\beta$ respectively. Let $\gamma = \max(\alpha, \beta)$, where the maximum is taken element-wise. We call $x^\gamma$ the least common multiple (lcm) of $\mathrm{lt}(f)$ and $\mathrm{lt}(g)$</p>
    <p>$$x^\gamma = \operatorname{lcm}(\mathrm{lt}(f), \mathrm{lt}(g)).$$</p>   
</div>
<!-- ^81a5e9 -->

<p>Note the the definition of lcm is with respect to an (implied) monomial ordering.</p>


### S-polynomials 

<p>Next, we define the $S$-polynomial of $f$ and $g$.</p>

<div style="border-style:solid;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="def4">
    <p><strong>Definition 4: S-polynomial.</strong> 
    Let $f,g\in k[x_1, \ldots, x_n]$ be two polynomials and let $\gamma$ be as in <a href="#def3">Definition 2</a>. We define the $S$-polynomial of $f$ and $g$ as </p>  
    <p>$$S(f, g) = \frac{x^\gamma}{\mathrm{lt}(f)}f - \frac{x^\gamma}{\mathrm{lt}(g)}g$$</p> 
</div>

<p>Again, the $S$-polynomial implies a monomial ordering.</p>

<p>Also note that $\mathrm{lt}(f) = \operatorname{lc}(f) x^\alpha$, so</p>
<p>$$\frac{x^\gamma}{\mathrm{lt}(f)} = \frac{x^\gamma}{\operatorname{lc}(f) x^\alpha} = \operatorname{lc}(f)^{-1}x^{\gamma - \alpha},$$</p>
<p>and given that $\gamma \geq \alpha$ (element-wise), $\gamma - \alpha$ is a multiindex.<p>

<p>Let's see what the $S$-polynomial looks like.</p>

**Example 1.** In $\mathbb{Q}[x, y, z]$ take $f=x^2y+2z$ and $g=5xy + 7y^3z$. Using <a href="../monomial-orderings/#lex">lex</a>, $\mathrm{lt}(f)=x^2y$, $\mathrm{lt}(g)=5xy$, so $\alpha=(2, 1, 0)$ and $\beta=(1, 1, 0)$, so $\gamma=(2,1,0)$ and 
<p>$$\begin{aligned}
S(f, g) {}={}& \frac{x^2y}{x^2y}f - \frac{x^2y}{5xy}g 
\\
{}={}& f - \frac{x}{5}g 
\\
{}={}&  \cancel{x^2y}+2z -\cancel{x^2y} - \tfrac{7}{5}xy^3z = 2z - \tfrac{7}{5}xy^3z,
\end{aligned}$$</p>
so we see that 
- $S(f, g) \in \langle f, g\rangle$
- and the leading terms of $f$ and $g$ vanish in $S(f, g)$
- $\operatorname{multideg}S(f, g) = (1, 3, 1) < \gamma$ $\bullet$ 

This can be computed using our <a href="https://github.com/alphaville/algebraic-geometry">MATLAB algebraic geometry toolbox</a>:

```matlab
[x, y, z] = polysymbols({'x', 'y', 'z'}); % lex order is default

f = x^2 * y + 2*z; g = 5*x*y + 7*y^3*z;
sfg = f.spoly(g)
```

Let us make an observation about the terms involved in $S$: it is 
$$\frac{x^\gamma}{\mathrm{lt}(f)}f = \operatorname{lc}(f)^{-1}x^{\gamma - \alpha}f,$$
so $\operatorname{multideg}\left[\frac{x^\gamma}{\mathrm{lt}(f)}f\right] = \gamma$ and $\operatorname{multideg}\left[-\frac{x^\gamma}{\mathrm{lt}(g)}g\right] = \gamma$. This can be confirmed in Example 1. 

### The criterion

<p>When we add these two polynomials, the leading terms cancel out. We now ask the "converse" question: if we have two polynomials $p_1$ and $p_2$ with the same multidegree (same as the two terms in $S$) and the leading terms cancel out, is it possible to describe $p_1 + p_2$ in terms of $S$-polynomials? The following lemma gives an affirmative answer.</p>

<div style="border-style:solid;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="lem4">
    <p><strong>Lemma 4.</strong> 
    Let $p_i$ be polynomials of <a href="../monomial-orderings#multidegree-and-leading-monomial">multidegree</a> $\delta$. If $\operatorname{multidegree}\sum_i p_i < \delta$, then $\sum_i p_i$ is a linear combination, with coefficients in $k$, of $S(p_j, p_l)$ and $\operatorname{multidegree} S(p_j, p_l) < \delta$.</p>
</div>

The proof can be found in[^1].

<p>The lemma implies that if $\operatorname{multidegree}p_i=\delta$, then</p>
<p>$$\sum_i p_i = \sum_{j,l} c_{j,l}S(p_j, p_l).$$</p>

As Cox et al.[^1] put it, in the RHS "all cancellation can be accounted for by $S$-polynomials." 

<p>This brings us to Buchberger's criterion:</p>

<div style="border-style:solid;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="thm5">
    <p><strong>Theorem 5: Buchberger's criterion.</strong> 
    Let $I$ be a <a href="../ideal-generated-by-polynomials">polynomial ideal</a>. A basis $G=\{g_1, \ldots, g_t\}$ is a <a href="#def1">Gröbner basis</a> of $I$ iff for all $i\neq j$, the remainder of the <a href="../division-multivariate-polynomials">division</a> of $S(g_i, g_j)$ by $G$ is zero.</p>
</div>


**Example 2.** In $\mathbb{Q}[x, y, z]$ with <a href="../monomial-orderings/#lex">lex</a> we will show that $G=\{x^5 + 2y, xy^2, y^3\}$ is a <a href="#def1">Gröbner basis</a>. We have 
$$S(x^5 + 2y, xy^2) = 2y^3,$$
which is clearly in $I = \langle G\rangle$. Likewise,
$$S(x^5 + 2y, y^3) = 2y^4 = 2y (y^3)\in I.$$
Lastly, 
$$S(xy^2, y^3) = 0 \in I.$$
We conclude that $G$ is a Gröbner basis. $\bullet$


## Buchberger's algorithm

<p>Buchberger's algorithm allows us to construct a Gröbner basis of an ideal $I$.</p>

<p>In Buchberger's criterion we saw that if $G=\{g_1, \ldots, g_t\}$ is a <a href="#def1">Gröbner basis</a> of an ideal $I$ then</p>
<p>$$\overline{S(g_i, g_j)}^G = 0,$$</p>
<p>for all $i\neq j$.</p>

<p>Suppose $I = \langle f_1, \ldots, f_s\rangle$ and $F=(f_1, \ldots, f_s)$ is not a <a href="#def1">Gröbner basis</a>. Buchberger's algorithm extends $F$ — it adds more polynomials (from $I$) until we obtain a Gröbner basis. In particular, every time we have $\overline{S(g_i, g_j)}^G \neq 0$, the remainder $\overline{S(g_i, g_j)}^G$ is included into the basis. </p>

To see how this works, let us compute some Gröbner bases[^1]:

<div style="border-style:dashed;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="ex2a">
    <p><strong>Exercise 2a, Section 2.7, p. 95</strong> 
    Find a Gröbner basis for $I=\langle x^2y-1, xy^2 - x\rangle$ using the lex and grlex monomial orderings.</p>
</div>


<p><b>Solution.</b> We can do this with our <a href="https://github.com/alphaville/algebraic-geometry">MATLAB algebraic geometry toolbox</a>:</p>

```matlab
ord = @(s1, s2) lex(s1, s2);

[x, y] = polysymbols({'x', 'y'}, ord);
id = MultivariatePolynomial([0 0 1], ord);
f1 = x^2*y - id;
f2 = x*y^2 - x;

F = {f1, f2};
I = PolynomialIdeal(F);
I.grobnerBasis();
G = I.grobner;
```

<p>When using lex we end up with a Gröbner basis with 4 polynomials. This is</p>
<p>$$G_{\rm lex} = \{x^2y - 1, xy^2 - x, x^2 - y, y^2 - 1\}.$$</p>
<p>When using grlex we obtain the same Gröbner basis. $\bullet$</p>

<div style="border-style:dashed;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="ex2b">
    <p><strong>Exercise 2b, Section 2.7, p. 95</strong> 
    Find a Gröbner basis for $I=\langle x^2+y, x^4+2x^2y+y^2+3\rangle$ using the lex and grlex monomial orderings. What does your result indicate about the variety $\mathbf{V}(I)$?</p>
</div>

<p><b>Solution.</b> Again, using <a href="https://github.com/alphaville/algebraic-geometry">our MATLAB algebraic geometry toolbox</a>, we have</p>

```matlab
ord = @(s1, s2) lex(s1, s2);

vars = {'x', 'y', 'z'};
[x, y] = polysymbols(vars, ord);
id = MultivariatePolynomial.id(vars, ord);

f1 = x^2 + y;
f2 = x^4 + 2*x^2*y+y^2+3*id;

F = {f1, f2};
I = PolynomialIdeal(F);
I.grobnerBasis();
G = I.grobner;
```

<p>We find that regardless of the monomial ordering, it is</p>
<p>$$G = \{x^2+y, x^4+2x^2y+y^2+3, -3\},$$</p>
<p>and we see that a constant term has sneaked into $G$ which means that $I=\mathbb{Q}[x, y]$, so $\mathbf{V}(I) = \emptyset$. $\bullet$</p>

<div style="border-style:dashed;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="ex2c">
    <p><strong>Exercise 2c, Section 2.7, p. 95</strong> 
    Find a Gröbner basis for $I=\langle x - z^4, y-z^5\rangle$ using the lex and grlex monomial orderings.
</div>

<p><b>Solution.</b>  Interestingly, using lex this <em>is</em> a <a href="#def1">Gröbner basis</a>, but using grlex the generated Gröbner basis is </p>
<p>$$G_{\rm grlex}=\{- z^4 + x, - z^5 + y, - xz + y, yz^3 - x^2, - y^2z^2 + x^3, x^4 - y^3z\},$$</p>
<p>which involves six polynomials. $\bullet$</p>

### The algorithm

Buchberger's algorithms is as follows:

<p>
<b>Input:</b> $F=\{f_1, \ldots, f_s\}$<br>
<b>Output:</b> Gröbner basis $G=\{g_1, \ldots, g_t\} \supseteq F$ for $I$<br>
<span>1. $G\gets F$</span><br>
<span>2. <b>Do</b></span><br>
	<span style='margin-left:2em'>2.1. $G' \gets G$</span><br>
	<span style='margin-left:2em'>2.2. For each $f,f'\in G$, compute $r = \overline{S(f_i, f_j)}^{G'}$; if $r\neq 0$ insert $r$ into $G'$</span><br>
<span>3. <b>until</b> $G'=G$.</span><br>
<span>4. <b>return</b> G</span>
</p>

<p>The algorithm creates an <b>ascending chain</b> of ideals, $\langle \mathrm{lt}(G')\rangle$, which is known to saturate.</p>


[^1]: David A. Cox, John Little, Donal O'Shea, Ideals, Varieties and Algorithms: An Introduction to Computational Algebraic Geometry and Commutative Algebra, Springer, 2015
[^2]: Ahlgren, Joyce Christine, "Ideals, varieties, and Gröbner bases" (2003). Theses Digitization Project. 2282. https://scholarworks.lib.csusb.edu/etd-project/2282, California State University, San Bernardino, accessed on 20 July 2024; offers a good presentation of Gröbner bases and the basics of algebraic geometry. 
[^3]: Brendan Hasset, Introduction to Algebraic Geometry, Cambridge University Press, 2007