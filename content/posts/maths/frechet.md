---
author: "Pantelis Sopasakis"
title:  "Fréchet derivative: a tutorial"
date: 2026-05-10
description: Fréchet derivative
summary: Fréchet derivative
math: true
series: ["Mathematix"]
tags: ["Functional Analysis", "Directional derivative"]
---

This is a short tutorial on the Fréchet derivative and some key properties. 
The Fréchet derivative is a notion of directional derivative in 
Banach spaces and is used a lot in optimal control (e.g., in 
Pontryagin's maximum principle).

## Definition

Let $V$ and $W$ be normed vector spaces, and $U\subseteq V$ be an open subset of $V$. A function $f : U \to W$ is called *Fréchet differentiable* at $x \in U$ if there exists a bounded linear operator $A:V\to W$ such that
<p>$$\lim_{\Vert h\Vert \to 0} \frac{\Vert f(x + h) - f(x) - Ah\Vert_W}{\Vert h\Vert_V} = 0.$$</p>

Equivalently, we can say that as $\Vert h\Vert \to 0$ we have[^8]

<p>$$f(x + h) = f(x) + Ah + o(h).$$</p>
If such an operator exists we write $\mathrm{D}f(x) = A$; this is a function $\mathrm{D}f: U \to B(V, W)$, where $B(V, W)$ is the set of bounded linear operators from $V$ to $W$. We can think of $\mathrm{D}f(x)[h]$ as the derivative of $f$ at $x$ along the direction $h$; this is linear in $h$. 

Spivak[^2] proves that for functions $F:\R^n\to\R^m$, if such a linear map exists, it is *unique.* 

We also have the following formula that $\mathrm{D}f(x)$ satisfies

<div style="border-style:solid;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="prop1">
    <p><strong>Calculation from Gâteaux derivative.</strong>
    The Fréchet derivative satisfies</p>
    <p>$$\mathrm{D}f(x)[h] = \lim_{t\to 0}\frac{f(x+th) - f(x)}{t} = \frac{\mathrm{d}}{\mathrm{d} t}f(x+th)\bigg|_{t=0},$$</p>
    <p>that is, if it exists, it equals the Gâteaux derivative of $f$.</p>
</div>

In other words, If a function is Fréchet-differentiable, then it is 
also Gâteaux-differentiable, and the two derivatives coincide.

The Fréchet derivative is a generalisation of the total derivative. The total derivative is precisely the Fréchet derivative when $V$ and $W$ are Euclidean spaces ($\R^n$ and $\R^m$). 

### Linearity

Firstly, the Fréchet derivative is linear in the following sense

1. $\mathrm{D}(cf)(x) = c\mathrm{D}f(x)$
2. $\mathrm{D}(f+g)(x) = \mathrm{D}f(x) + \mathrm{D}g(x)$

<p>A simple observation is that if $A: V \to W$ is a bounded linear operator, then $\mathrm{D}A(x)[h] = Ah$. Now consider the space $V = H^1([0, 1], {\rm I\!R})$ (a <a href="https://en.wikipedia.org/wiki/Sobolev_space#The_case_p_=_2" target="_blank">Sobolev space</a>) and $W = L^2([0, 1], {\rm I\!R})$ and let $A: x \mapsto \dot{x}$, which is a bounded linear operator. Then,</p>

$$\mathrm{D} \dot{x}[h] = \dot{h}.$$


### Several variables

Let $U, V, W$ be vector spaces and we have a Fréchet-differentiable function $F:U\times V \to W$. In that case, the Gâteaux derivative, $\mathrm{d}F(x, y)[\dot{x}, \dot{y}]$, is linear in $\dot{x}, \dot{y}$, that is 
$$\mathrm{d}F(x, y)[\dot{x}, \dot{y}] = A(x, y)\dot{x} + B(x, y)\dot{y}.$$
For $\dot{y}=0$, 
$$\mathrm{d}F(x, y)[\dot{x}, 0] = A(x, y)\dot{x}.$$
Using the definition of the Gâteaux derivative, we see that $A$ is the Gâteaux derivative of the mapping $x\mapsto F(x, y)$. We conclude that 

<div style="border-style:solid;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="prop2">
    <p><strong>Gâteaux derivative in several variables.</strong>
    Let $F:U\times V\to W$ be Fréchet differentiable. Then</p>
    <p>$$\mathrm{d}F(x, y)[\dot{x}, \dot{y}] = \mathrm{d}_x F(x, y)[\dot{x}] + \mathrm{d}_y F(x, y)[\dot{y}].$$</p>
</div>


## Relation to Gateaux derivative

If $f$ is Fréchet differentiable at $x$ it is also Gâteaux differentiable at $x$. Note, however, that although the Fréchet derivative is linear, the Gâteaux derivative may not be. 

If $f$ is Fréchet differentiable, then its Fréchet derivative is a linear map 
$$\mathrm{D}f(x): v\mapsto \mathrm{d}f(x)[v],$$
that is, the Fréchet derivative can be used to determine the Gâteaux derivative of $f$.

A function can be Gâteaux differentiable, but not Fréchet differentiable. In particular, $\mathrm{d}f(x)[v]$ may not be linear in $v$. 

## Relation to $C^1$

If all partial derivatives of $f$ exist and are continuous, then $f$ is Fréchet differentiable. However, a function can be Fréchet differentiable without being $C^1$. Here is a counterexample[^1]
$$
f(x, y)
{}={}
\begin{cases}
(x^2 + y^2)\sin\frac{1}{\sqrt{x^2 + y^2}}, & \text{ if } (x,y)\neq (0, 0)
\\\\
0, & \text{ otherwise}
\end{cases}
$$
This function has a Fréchet derivative, but does not have continuous derivatives at the origin. The function looks like this.

<img src="/frechet_not_c1.png" alt="Plot of the function f(x, y)" style="width: 80%; margin-left: auto;margin-right: auto;">
<p><em><strong>Figure 1.</strong> Plot of function $f(x, y)$.</em></p>

<img src="/frechet_not_c1b.png" alt="Slices of the function f for constant y" style="width: 80%; margin-left: auto;margin-right: auto;">
<p><em><strong>Figure 2.</strong> Slices of $f$ for constant $y$.</em></p>

and the partial derivative wrt $x$ is
$$
\frac{\partial f(x, y)}{\partial x}
{}={}
2\,x\,\sin\left(\frac{1}{\sqrt{x^2+y^2}}\right)-\frac{x\,\cos\left(\frac{1}{\sqrt{x^2+y^2}}\right)}{\sqrt{x^2+y^2}},$$
for $(x, y)\leq 0$. We can see that the partial derivative at zero does not exist and the limit of this partial derivative at zero does not exist. 

## Chain rule
The chain rule for the Fréchet derivative is as follows
<div style="border-style:solid;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="chain_rule">
<strong>Theorem.</strong>We have 
$$\mathrm{D}(G\circ F)(x) = \mathrm{D}G(F(x)) \circ \mathrm{D}F(x),$$
</div>

This means that 
$$\mathrm{d}(G\circ F)(x)[v] = \mathrm{d}G(F(x))[\mathrm{D}F(x)[v]],$$
for all $v$.

Proof[^3]. By definition, $F(x+v) = F(x) + \mathrm{D}F(x)[v] + o(\|v\|)$. We apply this to $G\circ F$

<p>$$\begin{aligned}(G\circ F)(x+v)
{}={}&
G(F(x+v))
\\
{}={}&
G(F(x) + \underbrace{\mathrm{D}F(x)[v] + o(\|v\|)}_{\text{direction}})
\\
{}={}&
G(F(x)) + DG(F(x))[\mathrm{D}F(x)[v] + o(\|v\|)] 
\\&\quad+ \underbrace{o(\|\mathrm{D}F(x)[v] + o(\|v\|)\|)}_{o(\|v\|)}
\\
{}={}&
G(F(x)) + DG(F(x))[\mathrm{D}F(x)[v]] + o(\|v\|),\end{aligned}$$</p>

so, to summarise
$$
(G\circ F)(x+v)
{}={}
G(F(x)) + DG(F(x))[DF(x)[v]] + o(\|v\|).
$$
This completes the proof. $\Box$ 


## Higher order Fréchet derivative

The Fréchet derivative is very general. We defined the Fréchet derivative of a mapping $f:V\to W$, where $V$ and $W$ are just vector spaces. We saw that the Fréchet derivative is a map $\mathrm{D}f: V \to {\rm Hom}(V, W)$. The Fréchet derivative of the Fréchet derivative is the second Fréchet derivative, which is a map
<p>$$\mathrm{D}^2f : V \to {\rm Hom}(V, {\rm Hom}(V, W)) \cong {\rm Hom}(V\otimes V, W),$$</p>
where the last isomorphism with the <a href="../tensor-product/">tensor product</a> holds provided $V$ is finite dimensional. We can likewise define Fréchet derivatives of order $k$; these are mappings 
$$\mathrm{D}^k f:V \to {\rm Hom}(V^{\otimes k} , W),$$
with $V$ finite dimensional. 

<div style="border-style:dashed;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="note1">
    <p><strong>Note.</strong>
    It makes sense: $\mathrm{D}f(x)$ is a linear operator. Naturally, $\mathrm{D}^2f(x)$ is a bilinear operator, and $\mathrm{D}^kf(x)$ is $k$-linear. Hence the tensors.</p>
</div>

From the above, we also see that $\mathrm{D}f(x):V \to W$ is linear, $\mathrm{D}^2f(x): V\times V \to W$ is bilinear, $\mathrm{D}^3 f(x): X\times X \times X \to W$, etc. We can also say that 
$$\mathrm{D}^kf(x): \underbrace{V\otimes \cdots \otimes V}_{k \text{ times}} \to W,$$
is linear, i.e., we have a $\mathrm{D}^kf(x)[\mathrm{d} x_1 \otimes \ldots \otimes \mathrm{d} x_k]$.

## Fréchet derivative of bilinear operators

This is from[^5] (Example 5, p. 231). 

<div style="border-style:solid;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="prop3">
    <p><strong>Bilinear operators.</strong>
    Let $b:X\times X\to Y$ be a bilinear bounded operator, where $X_1$, $X_2$, and $Y$ are Banach spaces. Let $X \coloneqq X_1 \times X_2$. Then, $b$ is Fréchet differentiable infinitely many times and for $x, h\in X$,</p>
    <p>$$\mathrm{D}b(x)(h) = b(h_1, x_2) + b(x_1, h_2).$$</p>
</div>

*Proof.* For the proof we'll first determine the Gâteaux derivative of $b$. We have 

<p>$$\begin{aligned}b(x+th)={}&b(x_1+th_1, x_2 + th_2) \\ 
{}={}& b(x_1, x_2) + t(b(h_1, x_2)+b(x_1, h_2))+t^2b(h_1, h_2).\end{aligned}$$</p>

Therefore, the Gâteaux derivative of $b$ at $u$ along $h$ is 
<p>$$\begin{aligned}
\mathrm{d}f(x)(h) {}={}& \lim_{t\to 0}\frac{b(x+th) - b(x)}{t}
\\{}={}& \lim_{t\to 0}\frac{t(b(h_1, x_2)+b(x_1, h_2))+t^2b(h_1, h_2)}{t}
\\{}={}&b(h_1, x_2)+b(x_1, h_2).
\end{aligned}$$</p>
This proves that $\mathrm{d} b(x)(h) = b(h_1, x_2)+b(x_1, h_2)$. 

Actually, from the first equation we see that 
<p>$$\begin{aligned}
b(x+h){}={}& b(x) + \underbrace{b(h_1, x_2)+b(x_1, h_2)}_{Ah}+B(h).
\end{aligned}$$</p>

To show that $\mathrm{D}b(x) = A$ it suffices to show that $b(h) = o(h)$. It is[^5]. The Fréchet derivative of $b$ is  $\Box$

Some other examples are given in[^6]. 

## Taylor's formula in Banach spaces

Let $U$ be an open subset of a real Banach space $X$. If $f: U \to \R$ is differentiable $n+1$ times on  $U$, it may be expanded by Taylor's formula[^4]:

<p>$$\begin{aligned}f(x+h) ={}& f(x) + \mathrm{D} f(x)  (h)
+ \frac{1}{2!} \mathrm{D}^2 f(x)(h, h)
+ \ldots 
\\
&+ \frac{1}{n!} \mathrm{D}^n f(x) (h, h, \ldots, h) + R_n(h),\end{aligned}$$</p>

where $R_n$ is the remainder. 

In[^4] they use the notation

<p>$$\begin{aligned}f(x+h) ={}& f(x) + \mathrm{D} f(x)  \cdot h + \frac{1}{2!} \mathrm{D}^2 f(x) \cdot h^2
+ \ldots 
\\
&+ \frac{1}{n!} \mathrm{D}^n f(x) \cdot h^n + R_n(h),\end{aligned}$$</p>

but **with the note that** the notation $\cdot h^k$ means "evaluation at $(h, \ldots, h)$, $k$ times". Of course this is just for notational convenience because no vector-to-vector multiplication operation has not been defined. This is Theorem 4.C in the book of Zeidler (they also use Fréchet derivatives there). 

Again using tensors, since $\mathrm{D}^k f(x)$ is $k$-linear, it can be seen as a linear function over the space of tensor products. This is illustrated in Figure 3.

<img src="/frechet-universal.png" alt="Frechet derivative" style="width: 50%; margin-left: auto;margin-right: auto;">
<p><em><strong>Figure 3.</strong> <a href="../tensor-product-universality">Universal property</a> of tensor products.</em></p>

where we often, with some abuse of notation, drop the tilde.

Let's try to understand this better. Say we have a function $f:\R^n\to\R$. Then $\mathrm{D}^3f(x):\R^n\times \R^n \times \R^n\to\R$ is trilinear and can be seen as a linear function $\tilde{\mathrm{D}}^3f(x):\R^n \otimes \R^n \otimes \R^n \to \R$, 

<p>$$\begin{aligned}\tilde{\mathrm{D}}^3f(x):\R^n \otimes \R^n \otimes \R^n \ni &\mathrm{d} x_1 \otimes \mathrm{d} x_2 \otimes \mathrm{d} x_3 \\
&\to \tilde{\mathrm{D}}^3f(x)[\mathrm{d} x_1 \otimes \mathrm{d} x_2 \otimes \mathrm{d} x_3] \in \R.\end{aligned}$$</p>

We know that $\mathrm{d} x_1 \otimes \mathrm{d} x_2 \otimes \mathrm{d} x_3$ with $\mathrm{d} x_i\in\R^n$ can be seen as a vector 

<p>$$\mathrm{d} x_1 \otimes \mathrm{d} x_2 \otimes \mathrm{d} x_3 = (\mathrm{d} x_{1i} \otimes \mathrm{d} x_{2j} \otimes \mathrm{d} x_{3k})_{ijk},$$</p>

so the linear map $\tilde{D}^3f(x):\R^n \otimes \R^n \otimes \R^n \to \R$ is a map
$$\tilde{D}^3f(x)[\mathrm{d} x_1 \otimes \mathrm{d} x_2 \otimes \mathrm{d} x_3] = \sum_{i,j,k} a_{i,j,k}(x) \cdot (\mathrm{d} x_{1i} \otimes \mathrm{d} x_{2j} \otimes \mathrm{d} x_{3k}).$$
In fact,
<p>$$a_{i,j,k}(x) {}={} \frac{\partial^3 f(x)}{\partial x_i \partial x_j \partial x_k}.$$</p>

These can be thought of as the "coordinates" of the third-order total derivative, which is a tensor[^7].


Let us look at an application: Let $f:\R^n\to\R$ and let $X$ be a random variable. Let $\bar{X} = {\rm I\! E}[X]$. Then,
<p>$${\rm I\! E}[\mathrm{D}^kf(\bar{X})[h, \ldots, h]] = {\rm I\! E}[\mathrm{D}^k f(\bar{X})[h^{\otimes k}]] 
= 
{\rm I\! E} \left[\sum_{
  \substack{
    \iota = (i_1, \ldots, i_k)\\
    1 \leq i_1, \ldots, i_k \leq n
  }
}
f_{x_{\iota}}(\bar{X})h^{\iota}\right],$$</p>
where we used some convenient notation; firstly, $\iota$ is a multiindex, and we denote 
$$f_{x_{\iota}} = \frac{\partial^{|\iota|} f}{\partial x_{\iota}} = \frac{\partial^{i_1+\ldots+i_k} f}{\partial x_{i_1}\ldots\partial x_{i_k}},$$
and $h^{\otimes \iota} = h_{i_1}h_{i_2}\ldots h_{i_k}$. 

<p>For example, using $h = X - \bar{X}$,</p>
<p>$$\begin{aligned}
{\rm I\! E}[\mathrm{D}^2 f(\bar{X})[h,h]] 
{}={}& \sum_{i_1, i_2 = 1}^{n}\frac{\partial^2 f(\bar{X})}{\partial x_1 \partial x_2}{\rm I\! E}[h_{i_1}h_{i_2}] 
\\
{}={}& \sum_{i_1, i_2 = 1}^{n}\frac{\partial^2 f(\bar{X})}{\partial x_1 \partial x_2}{\rm Cov}(X_{i_1}, X_{i_2}).
\end{aligned}$$</p>
No wonder, the expectation of the second total derivative of $f$ is associated with a covariance (which provides second-order information). 

We can further define the skewness tensor 
<p>$$S_{i_1, i_2, i_3} = {\rm I\! E}[h_{i_1}h_{i_2}h_{i_3}],$$</p>
where $h = X - \bar{X}$, with $\bar{X} = {\rm I\! E}[X]$. Then,
$${\rm I\! E}[\mathrm{D}^3 f(\bar{X})[h, h, h]] = \sum_{i_1,i_2,i_3=1}^{n}\frac{\partial^3 f(\bar{X})}{\partial x_{i_1}\partial x_{i_2}\partial x_{i_3}}S_{i_1, i_2, i_3}.$$
This allows us to apply the above Taylor expansion. It is 
<p>$$\begin{aligned}
{\rm I\! E}[f(X)] 
{}={}& 
{\rm I\! E}\Big[f(\bar{X}) + \mathrm{D}f(\bar{X})[h] + \frac{1}{2!}\mathrm{D}^2f(\bar{X})[h^{\otimes 2}] \\
&+ \frac{1}{3!}\mathrm{D}^3f(\bar{X})[h^{\otimes 3}] + \frac{1}{4!}\mathrm{D}^4f(\bar{X})[h^{\otimes 4}] + R(h)\Big]
\\
{}\approx{}&
f(\bar{X}) +
\frac{1}{2!}\sum_{i_1, i_2 = 1}^{n}\frac{\partial^2 f(\bar{X})}{\partial x_1 \partial x_2}P_{i_1, i_2}
\\ &\quad {}+{}
\frac{1}{3!}\sum_{i_1,i_2,i_3=1}^{n}\frac{\partial^3 f(\bar{X})}{\partial x_{i_1}\partial x_{i_2}\partial x_{i_3}}S_{i_1, i_2, i_3}
\\ & \quad {}+{}
\frac{1}{4!}\sum_{i_1,i_2,i_3, i_4=1}^{n}\frac{\partial^4 f(\bar{X})}{\partial x_{i_1}\partial x_{i_2}\partial x_{i_3}\partial x_{i_4}}K_{i_1, i_2, i_3, i_4},
\end{aligned}$$<p>
where $P$, $S$, and $K$ are the variance-covariance, skewness, and kurtosis tensors. 

## Interchanging integrals and derivatives

Kammar[^9] has shown the following dominated convergence result that a
allows to interchange the Fréchet derivative and integrals.

<div style="border-style:solid;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="dominate_convergence">
Let $X$ be a Banach space, $U \subseteq X$ is open, 
$(\Omega, \mathcal{F}, \mu)$ is a measure space, $A \in \mathcal{F}$,
$f: \Omega \times X \to {\rm I\!R}$. Assume that 

<ol>
    <li>For almost every $\omega \in A$, $f(\omega, \cdot)$ is Fréchet-differentiable</li>
    <li>For all $x\in U$ and $h\in X$, $f(\cdot, x)$ and $\mathrm{D} f(\cdot, x)[h]$ are integrable on $A$</li>
    <li>There is an integrable function $\theta:\Omega \to {\rm I\!R}$ on $A$ such that for almost every $\omega \in A$ it is $\|\mathrm{D} f(\cdot, x)\| \leq \theta(\omega)$</li>
</ol>

Then, $F(x) \coloneqq \int_A f(\omega, x) \mathrm{d}\mu$ is Fréchet-differentiable
on $U$ and 

<p>$$\mathrm{D}\left(\int_A f(\omega, x) \mathrm{d}\mu\right)[h] = \int_A \mathrm{D}f(\omega, x)[h]\mathrm{d}\mu.$$</p>

</div>

Next, we'll see how the Fréchet derivative will be useful to solve optimization problems in 
Banach spaces using the Lagrange multiplier theorem.


[^1]: See [Wikipedia page](https://en.wikipedia.org/wiki/Fr%C3%A9chet_derivative) (Fréchet derivative), accessed on 16 November 2023
[^2]: M. Spivak, Calculus on manifolds: a modern approach to classical theorems of advanced calculus, 1968; see mainly Chapter 2
[^3]: Francis J. Narcowich, Fréchet & Gâteaux derivatives and the chain rule, 2021,  [link](https://people.tamu.edu/~f-narcowich//m642/m642_notes/F_G_derivatives.pdf), accessed on 17 November 2023
[^4]: Planetmath, Taylor's formula in Banach spaces, accessed on 2 July 2025
[^5]: Eberhard Zeidler. Applied functional analysis: main principles and their applications. Springer-Verlag, 1995, [link](http://dl.iranidata.com/book/daneshgahi/Applied%20Functional%20Analysis-www.iranidata.com.pdf), accessed on 3 June 2025
[^6]: Alan Edelman and Steven G. Johnson, Matrix Calculus for Machine Learning and Beyond, MITOpenCourseware, 18.S096 | January IAP 2023 | Undergraduate, accessed on 3 June 2025 [link](https://ocw.mit.edu/courses/18-s096-matrix-calculus-for-machine-learning-and-beyond-january-iap-2023/pages/lecture-notes/); see in particular Lectures 2, 3, and 12.
[^7]: $\mathrm{D}^3f(x)$ is indeed a tensor. It is a linear map $\mathrm{D}^3 f(x) \in {\rm Hom}(V^{\otimes 3}, W)$ and ${\rm Hom}(V^{\otimes 3}, W) \cong (V^{\otimes 3})^* \otimes W$ (in the finite-dimensional case).
[^8]: Recall that the Landau notation $f(h) = o(g(h))$ as $\|h\|\to 0$ means that $\lim_{\|h\|\to 0}f(x)/g(x) = 0$.
[^9]: O. Kummar, A note on Fréchet diffrentiation under Lebesgue integrals, available [here](https://www.cs.ox.ac.uk/people/ohad.kammar/notes/kammar-a-note-on-frechet-differentiation-under-lebesgue-integrals.pdf); accessed on 12 May 2026