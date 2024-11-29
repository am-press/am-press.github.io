---
author: "Pantelis Sopasakis"
title:  "PDF of (X, X)"
date: 2024-09-29
description: "PDF of (X, X)"
summary: "The PDF of (X, X) does not exist... in the ordinary sense"
math: true
series: ["Mathematix"]
tags: ["Probability"]
collapsible: true
showtoc: false
---

<p></p>

## Problem statement
<p>Let $X$ be a continuous real-valued random variable. <b>What is the pdf of $Z = (X, X)$?</b> This is of course a <em>problème mal posé</em> because $Z$ is not a continuous random variable in $\R^2$. However, the pdf of $Z$ makes sense in a different sense. Let's have a look...</p>

<p>Let us start by definining the set</p>
<p>$$E = \{(x, y)\in\R^2: x = y\}.$$</p>
<p>Note that $Z:(\Omega, \mathcal{F}, \mathsf{P}) \to E$ takes values in $E$.</p>
<p>We also define the "lifting" map $\pi:\R\ni x \mapsto (x, x)\in E$, which is continuous and invertible.</p>

## There is no such PDF

<p>We have a continuous real-valued random variable $X$. The random variable $Z=(X, X)$, seen as a random variable in $\R^2$ doesn't have a pdf; let's see why: take a subset of $E$, say $N=\{(x, x); x\in [a, b]\}$, with $a < b$, which is Lebesgue-measurable and a null set (measure zero). However, $\mathsf{P}[Z\in N] = \mathsf{P}[X\in [a, b]] \neq 0$. Therefore, there is no function $p_Z:\R^2\to\R_+$ such that 
</p>
<p>$$\int_{N}p_Z \mathrm{d}(x, y) \neq 0,$$</p>
<p>so $Z$ does not have a pdf.</p>

## The Radon-Nikodym derivative

<p>The Radon-Nikodym derivative states that if $\mu, \nu$ are measures and $\mu \ll \nu$, then there is a nonnegative-valued function $f\in\mathcal{L}^1(\mu)$ such that $\nu(A) = \int_A f\mathrm{d}\mu$. The function $f$ is unique $\mu$-a.e. We call $f$ the Radon-Nikodym derivative of $\nu$ with respect to $\mu$ and we denote $f = \frac{\mathrm{d}\nu}{\mathrm{d}\mu}$. This result can be extended to cases where $\nu$ is not absolutely continuous with respect to $\mu$.</p>

## PDFs as Radon-Nikodym derivatives

<p>We can generalise the notion of a pdf: a pdf can be seen as Radon-Nikodym derivative with respect to a measure (the default being the Lebesgue measure). Here we need to use a different measure.</p>
<p>Firstly, recall the definition of the pushforward measure, $Z_*\mathsf{P}$, of the probability measure $\mathsf{P}$ via $Z$, which is defined as the measure</p>
<p>$$(Z_*\mathsf{P})(A) = \mathsf{P}[Z\in A],$$</p>
<p>If $Z_*\mathsf{P}$ is absolutely continuous with respect to a measure $\nu$, i.e., $Z_*\mathsf{P} \ll \nu$, then there is a function $p_Z$, called the pdf of $Z$ with respect to $\nu$, such that</p>
<p>$$(Z_*\mathsf{P})(A) = \int_A p_Z \mathrm{d}\nu.$$</p>
<p>Next, we will construct such a measure, $\nu$.</p>


## Constructing new measures

<p>We start with the Lebesgue measure, $\mu$, on $\R$. We will define a measure on $E$. Firstly, we make $E$ into a measurable space by endowing it with the σ-algebra $\mathcal{E} = \{\pi(B), B\in \mathcal{B}_\R\}$, where $\mathcal{B}_\R$ is the Borel σ-algebra on $\R$. Now we define a measure $\nu:\mathcal{E}\to\R_+$ as </p>
<p>$$\nu(\pi(B)) =\mu(B)$$</p>
<p>It can be seen that $Z_*\mathsf{P} \ll \nu$, so there is a pdf of $Z$ wrt $\nu$, i.e., $\mathsf{P}[Z\in \pi(B)] = \int_{\pi(B)}p_Z \mathrm{d}\nu$, where $\pi_Z:E\to\R_+$. Note that $\nu$ is the pushforward measure of $\mu$ via $\pi$, i.e., $\nu = \pi_*\mu$. Let us recall that for a measurable function $g:E\to\R$,</p>
<p>$$\int_E g\mathrm{d}\nu = \int_E g\mathrm{d}(\pi_*\mu) = \int_\R (g\circ \pi)\mathrm{d}\mu.$$</p>
<p>This is a <em>change of measure</em> formula in integration.</p>
<p>We can now extend the measure $\nu$ to the entire $\mathcal{B}_{\R^2}$. The σ-algebra $\mathcal{E}$ is a sub-σ-algebra of $\mathcal{B}_{\R^2}$; in fact, $\mathcal{E}$ is the restriction of $\mathcal{B}_{\R^2}$ on $E$. We can construct a measure $\tilde{\nu}$ on $\mathcal{B}_{\R^2}$ such that</p>
<p>$$\tilde{\nu}(B) = \nu(B\cap E),$$</p>
<p>for $B\in\mathcal{B}_{\R^2}$.</p>
<p>If we treat $Z$ as a random variable that takes values in $\R^2$, we see that $Z_*\mathsf{P}$ is absolutely continuous wrt $\tilde{\nu}$. </p>

## The pdf of $Z$ wrt $\nu$

<p>Here we treat $Z$ as a random variable that takes values in $E$. Let us denote the pdf of $Z$ wrt the measure $\nu$ by $p_Z^\nu$. We claim that $p^{\nu}_Z(x, x) = p_X(x)$ for $\mu$-almost all $x$.</p>
<p>By definition, for the pdf of $Z$ wrt $\nu$, which is a function $p_Z^\nu:E\to\R_+$, we have that for $S\subseteq E$, measurable, </p>
<p>$$\mathsf{P}[Z\in S] = \int_S p_Z^\nu\mathrm{d}\nu = \int_S p_Z^\nu \mathrm{d}(\pi_*\mu) = \int_{\pi^{-1}(S)}p_Z^\nu \circ \pi \mathrm{d} \mu.$$</p>
<p>At the same time, </p>
<p>$$\mathsf{P}[Z\in S] = \mathsf{P}[X\in \pi^{-1}(S)] = \int_{\pi^{-1}(S)}p_X\mathrm{d}\mu,$$</p>
<p>where $p_X$ is the pdf of $X$ wrt $\mu$. We conclude that...
<div style="border-style:solid;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="thm:mse_var_bias">
<p><b>Proposition 1.</b>The pdf of $Z=(X, X)$ with respect to $\nu$ is $p_Z^\nu \circ \pi = p_X$, $\mu$-a.s., i.e., </p>
<p>$$p_Z^{\nu}(x, x) = p_X(x),$$</p>
<p>$\mu$-a.s.</p>
</div>




## The pdf of $Z$ wrt $\tilde{\nu}$
<p>Here we are looking for a function $p_Z^{\tilde{\nu}}:\R^2\to\R_+$ such that for all measurable sets $A\subseteq \R^2$, </p>
<p>$$\mathsf{P}[Z\in A] = \int_A p_Z^{\tilde{\nu}} \mathrm{d}\tilde{\nu}.$$</p>
<p>By following the same procedure as above we'll find that $(p_Z^{\tilde{\nu}}1_E)\circ \pi = p_X$, $\mu$-a.s., but the Radon-Nikodym derivative is $\tilde{\nu}$-a.e. unique, so $p_Z^{\tilde{\nu}} | E^c$ is free. Note that $E^c$ is a $\tilde{\nu}$-null set. This leads to the following result...</p>
<div style="border-style:solid;border-width:1.5px;padding: 10px 15px 0px 10px; margin-bottom: 10px" id="thm:mse_var_bias">
<p><b>Proposition 2.</b>The pdf of $Z=(X, X)$ with respect to $\tilde{\nu}$ is </p>
<p>$$p_Z^{\tilde{\nu}}(x, y) = p_X(x)1_{E}(x, y), \tilde{\nu}-\text{a.e.}$$</p>
</div>


