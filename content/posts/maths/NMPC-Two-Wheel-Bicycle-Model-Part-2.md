---
author: "NVS Chandra Mouli G"
title:  "Nonlinear Model Predictive Control (NMPC) for autonomous navigation of small scale cars part 2"
date: 2024-11-30
description: "NMPC for autonomous navigation part 2"
summary: "Continuation of the discussion of design and simulation results of NMPC controlled small scale car."
math: true
series: ["Mathematix"]
tags: ["Nonlinear Control", "NMPC", "Obstacle Avoidance", "Lane-Keeping"]
collapsible: true
---

> **Previous post:** <a href="../NMPC-Two-Wheel-Bicycle-Model" target="_blank">Nonlinear Model Predictive Control (NMPC) for autonomous navigation of small scale cars</a>.<br/>

<p>This post is the continuation of a previous post on <a href="../NMPC-Two-Wheel-Bicycle-Model" target="_blank">Nonlinear Model Predictive Control (NMPC) for autonomous navigation of small scale cars</a>, which served as an introduction to NMPC based autonomous navigation. The previous post also featured the addition of the vehicle's braking system along with its simulation results. The discussion ended with a note on the undesirable simultaneous use of the acceleration PWM input $(\tilde{d})$ and the brake input $(\tilde{b})$ by the controller.</p>

<p>In this post, a simple fix for this undesirable simultaneous control activation will be discussed under Section 1. Under that same section, the addition of the obstacle avoidance feature will be discussed along with a showcase of the corresponding simulation results.</p>

<p>In Section 2, the discussion will be focused on the introduction of the lane-keeping feature by building upon a high-level reference generator algorithm. This reference generator algorithm is inspired by the work in [1]. After showcasing the corresponding simulation results the section ends on an interesting note about the safety constraints.</p>

<p>The post ends with Section 3, where the obstacle avoidance feature will be combined with lane-keeping. In this section a special function will be introduced to the reference generator algorithm that helps with real-time (dynamic) obstacle avoidance. The discussion then moves on to how this special function can help with avoiding obstacles in motion and ends with the showcase of corresponding simulation results.</p>

<p>The software used for the simulations in this post are same as in the previous post and are listed below.</p>

### Software Used
1. **[Python3](https://www.python.org/):** An open-source high-level interpreted programming language.
2. **[NumPy](https://numpy.org/):** Numerical Python in short NumPy is an open-source scientific computational tool for multidimensional array operations. 
3. **[Matplotlib](https://matplotlib.org/):** Is an open-source visualisation library for Python.
4. **[CasADi](https://web.casadi.org/):** Is an open-source tool for nonlinear optimisation and algorithmic (automatic) differentiation. 
5. **[Python-control](https://python-control.readthedocs.io/en/0.10.1/):** An open-source control systems library for Python.
6. **[OpEn](https://alphaville.github.io/optimization-engine/):** Optimization Engine in full, is a framework for designing and solving non-linear and non-convex optimisation problems. 

<p>Once again the notations from the previous post are reintroduced below.</p>

## Notation
| Symbol                | Expansion              | Comment                     |
|--------               |-----------             |---------                    |
|$0_{n \times m}$       | $[0]_{n \times m}$     | An $n \times m$ null matrix.|
|$1_{n \times m}$       | $[1]_{n \times m}$     | An $n \times m$ matrix with every element equal to 1.|
|$I_{n}$                | $I_{n \times n}$       | An $n \times n$ identity matrix.|
|$\R$                   |$(-\infty,\ \infty)$    | Set of real numbers.        |
|$\R^n$                 |$\\{\mathbf{x}_ {n \times 1}: x_i \in \R \ \forall \ i \in \Z_{[1,\ n]}, n \in \R\\}$| Set of all $n \times 1$ real vectors.|
|$\R^{n \times m}$      |$\\{\mathbf{A}_ {n \times m}: a_ {i,\ j} \in \R \ \forall \ i \in \Z_{[1,\ n]},\text{ and } j \in \Z_{[1,\ m]}\\}$| Set of all $n \times 1$ real vectors.|
|$\R_{[i,\ j]}$         |$\\{k \in \R: i \le k \le j\ \forall\ i,\ j \in \R\\}$| Set of real numbers from $i$ to $j$ inclusive.        |
|$\R_-$ or $\R_{\lt 0}$ |$(-\infty,\ 0)$         | Set of all negative real numbers.|
|$\R_+$ or $\R_{\ge 0}$ |$[0,\ \infty)$          | Set of all positive real numbers.|
|$\Z$                   |$(\ldots, -2,\ -1,\ 0,\ 1,\ 2, \ldots)$| Set of all integers.|
|$\Z_{[i,\ j]}$         |$\\{k \in \Z: i \le k \le j\ \forall\ i,\ j \in \Z \\}$| Set of all integers between $i$ and $j$ inclusive.|
|$\Z_-$ or $\Z_{\lt 0}$ |$(\ldots, -2,\ -1,\ 0)$ | Set of all negative integers.|
|$\Z_+$ or $\Z_{\ge 0}$ |$[0,\ 1,\ 2, \ldots)$   | Set of all positive integers.|
|$\mathbb{S}_+^n$       |$\\{\mathbf{A}\in \R^{n \times n}: \mathbf{x}^\mathsf{T}\mathbf{A}\mathbf{x} \ge 0 \ \forall \ \mathbf{x} \in \R^n \\}$| Set of all symmetric positive semi-definite matrices.|
|$\mathbb{S}_{++}^n$    |$\\{\mathbf{A} \in \R^{n \times n}: \mathbf{x}^\mathsf{T}\mathbf{A}\mathbf{x} \gt 0 \text{ and } \mathbf{x}^\mathsf{T}\mathbf{A}\mathbf{x} = 0 \implies \mathbf{x} = 0_n \ \forall \ \mathbf{x} \in \R^n \\}$| Set of all symmetric positive definite matrices.|

<p>

Similar to the previous post, the symbol **ఠ** will be used at the beginning of each section to indicate the introduction of a *road safety* feature.</p>

## ఠ Section 1: Obstacle avoidance

<p>

In this section, obstacle avoidance feature (*constraint*) will be added to the NMPC formulation. This feature is of importance for two reasons, first being that it extends the concept of *safety* for both the vehicle and the environment that it is navigating in. The obstacle could be a tree, another vehicle, or a human. Enabling such a feature prohibits the vehicle from injuring living beings and prevents self-damage when navigating around objects. The second reason being that this concept can be extended and reused when constraining the vehicle to stay on the track (road).
</p>

<p>

Before introducing this feature, the fix for simultaneous activation of $\tilde{d}$ and $\tilde{b}$ should be addressed. The solution for this problem is being introduced here, instead of in Section 2 of the previous post is because just like for obstacle avoidance the fix has to do with modifying the formulation (not the dynamics as in Section 2 of the previous post). The simplest fix is to modify the cost function in the minimisation problem as follows,
</p>

<p>
$$
\|\mathbf{p}^i_N - \mathbf{p}^i_d\|_{\mathbf{Q}_{1}}^2 + \sum_{k=0}^{N-1} \left(\|\mathbf{u}^i(k) - \mathbf{u}^i(k-1)\|_{\mathbf{Q}_2}^2 + \mathbf{R} \cdot \tilde{d}(k) \cdot \tilde{b}(k) \right)
$$
</p>

<p>

Where $\mathbf{R} \gt 0$ and must be a very large number. This imposes that the best (*optimal*) way to avoid a large cost is to make the selected control actions $\tilde{d}$ and $\tilde{b}$ not be greater than zero at the same time. Since $\tilde{d}$ and $\tilde{b} \in [0, 1]$, their product need not be squared in the cost function.
</p>

<p>

The obstacle avoidance feature shall be introduced in the form of additional constraints to the formulation. In general, it is easy to both design and compute ellipsoidal constraints and for this reason, moving forward all obstacles will be approximated as either ellipses or circles with radii selected in such a way that guarantees *safety* (collision avoidance). For that, the radii shall address both the size of the obstacle puls some safe distance and a buffer zone that takes the size of the vehicle into account. It is also to be ensured that the geometric centre of the object coincides with the centre of the ellipse. From here on, such ellipses are called as safety circles.
</p>

<p>

For demonstration, an obstacle is placed with its centre at the origin with the radius of its safety circle equal to $2$ mts. The corresponding obstacle avoidance constraint is as follows,
</p>

<p>
$$
\begin{aligned}
&p_x^2(k) + p_y^2(k) > 2^2 \\
\implies &4 - p_x^2(k) - p_y^2(k) < 0,\ \forall k \in \Z_{[1, N]}
\end{aligned}
$$
</p>

<p>

Needless to mention that it should be ensured that $(p_x(0), p_y(0)) \notin \\{(x, y): x^2 + y^2 \le 2^2\\}$ i.e., the system does not start from a position inside the safety circle. With these modification the new formulation can we written as follows,
</p>

<p>
$$
\begin{aligned}
\mathbb{P}^i_N(\mathbf{z}^i(0)) :\ &\underset{\mathbf{u}^i}{\text{minimise}}\ \|\mathbf{p}^i_N - \mathbf{p}^i_d\|_{\mathbf{Q}_{1}}^2 + \sum_{k=0}^{N-1} \left(\|\mathbf{u}^i(k) - \mathbf{u}^i(k-1)\|_{\mathbf{Q}_2}^2 + \mathbf{R} \cdot \tilde{d}(k) \cdot \tilde{b}(k) \right) \\
\text{s.t.}\ & \mathbf{z}^i(0) = \mathbf{z}^{i-1}(N), \\
\ &\mathbf{u}^i(-1) = \mathbf{u}^{i-1}(N-1), \\
\ &\mathbf{z}^i(k+1) = \mathbf{g}(\mathbf{z}^i(k), \mathbf{u}^i(k)),\ k \in \Z_{[0, N-1]}, \\
\ &\mathbf{u}^i(k) \in \bm{\mu},\ k \in \Z_{[0, N-1]}, \\
\ &v_x^i(k) \in \bm{\gamma},\ k \in \Z_{[0, N-1]}, \\
\ &2^2 - (p_x^i(k))^2 - (p_y^i(k))^2 < 0,\ k \in \Z_{[1, N]}, \\
\ &\mathbf{p}^0(0) \notin \{(x, y): x^2 + y^2 \le 2^2\}, \\
\ &\mathbf{Q}_1 \in \mathbb{S}^{2}_{++}, \\
\ &\mathbf{Q}_2 \in \mathbb{S}^{n_u}_{++}, \text{ and}\\
\ &i \in \Z_{[0, \bm{S}]}.
\end{aligned}
$$
</p>

<p>

The simulation results of this formulation with $N=50,\ \bm{S} = 300,\ T=0.01$, $\mathbf{R} = 1000000$, $\mathbf{p}^0(0) = [-2, 2]^\mathsf{T}$, and $\mathbf{p}^i_d = [1, 4]^\mathsf{T}\ \forall\ i \in \Z_{[0, \bm{s}]}$ are shown in the following GIF,
</p>

<div>
<img src="/mpc_car_obst_2m_6.gif" alt="Simulation results" width="640" height="400">
</div>

<p>

From the above GIF, it can be seen that the NMPC controlled vehicle is indeed avoiding the obstacle and reaching close to the destination. It must be observed that before $t=1.5$, the vehicle drove close (tangential) to the safety circle because that is an optimal path that minimises the above formulated cost function. This is the reason why the radii (radius in this case) must include a buffer zone around the actual object. This ensures that the vehicle never comes *dangerously* close to the actual object. 
</p>

<p>

It should also be noted that before $t<1$, there was a noticeable magnitude of $v_y$. This could probably be the case that the vehicle is drifting, which is undesirable when it comes to *road safety*. However, this can simply be fixed by tuning the wright matrices $\mathbf{Q}_1$ and $\mathbf{Q}_2$, which will be done at a later point. But, for Section 4 (the next section), the goal is to have the NMPC drive on a predetermined track without exiting.
</p>

## ఠ Section 2: Lane-keeping

track sampling = 40 samples/m, 120 samples look ahead

<div>
<img src="/track_drive_higer_resolution_track.gif" alt="Simulation results" width="640" height="400">
</div>

<div>
<img src="/track_drive_higer_resolution_track_with_ref.gif" alt="Simulation results" width="640" height="400">
</div>

<div>
<img src="/track_drive_higer_resolution_track_with_ref_trail.gif" alt="Simulation results" width="640" height="400">
</div>

track sampling = 40 samples/m, 200 samples look ahead

<div>
<img src="/track_drive_higer_resolution_track_distant_destination.gif" alt="Simulation results" width="640" height="400">
</div>

<div>
<img src="/track_drive_higer_resolution_track_distant_destination_with_ref.gif" alt="Simulation results" width="640" height="400">
</div>

<div>
<img src="/track_drive_higer_resolution_track_distant_destination_with_ref_trail.gif" alt="Simulation results" width="640" height="400">
</div>

## ఠ Section 2: Lane-keeping + Obstacle Avoidance




## References
<p>

[1] Cataffo, Vittorio & Silano, Giuseppe & Iannelli, Luigi & Puig, Vicenç & Glielmo, Luigi. (2022). A Nonlinear Model Predictive Control Strategy for Autonomous Racing of Scale Vehicles. 10.1109/SMC53654.2022.9945279. 
</p>
