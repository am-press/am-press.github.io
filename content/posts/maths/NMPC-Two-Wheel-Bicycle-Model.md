---
author: "NVS Chandra Mouli G"
title:  "Nonlinear Model Predictive Control (NMPC) for autonomous navigation of small scale cars"
date: 2024-11-24
description: "NMPC for autonomous navigation"
summary: "Discussion of design and simulation results of NMPC controlled small scale car."
math: true
series: ["Mathematix"]
tags: ["Nonlinear Control", "NMPC"]
collapsible: true
---

## Introduction

<p>

I recently read a paper named "A Nonlinear Model Predictive Control Strategy for Autonomous Racing of Scale Vehicles" by Vittorio Cataffo et al., [1]. After reading the paper, I have decided to implement the proposed system in [Python3](https://www.python.org/) using [OpEn](https://alphaville.github.io/optimization-engine/). To study it further, I will be modifying the method to prioritize *road safe* features over minimum lap time. The purpose of this article is to showcase the formulation and simulation results of an NMPC controlled vehicle system that is *road safe* (at least in simulations). As a part of experimentation, I will be modifying the approach as the need arises and I will be updating this article by adding new sections accordingly. Going forward, the symbol **ఠ** will be used at the beginning of a section to indicate that a *road safety* feature is being introduced. Throughout this article the right-hand coordinate space is used with the z-axis pointing vertically up, the angles and rotations in counter-clockwise (CCW) direction are taken as positive. 
</p>

### Software Used
1. **[Python3](https://www.python.org/):** An open-source high-level interpreted programming language.
2. **[NumPy](https://numpy.org/):** Numerical Python in short NumPy is an open-source scientific computational tool for multidimensional array operations. 
3. **[Matplotlib](https://matplotlib.org/):** Is an open-source visualisation library for Python.
4. **[CasADi](https://web.casadi.org/):** Is an open-source tool for nonlinear optimisation and algorithmic (automatic) differentiation. 
5. **[Python-control](https://python-control.readthedocs.io/en/0.10.1/):** An open-source control systems library for Python.
6. **[OpEn](https://alphaville.github.io/optimization-engine/):** Optimization Engine in full, is a framework for designing and solving non-linear and non-convex optimisation problems. 

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


## Section 1: Initial Formulation

<p>

This section showcases the simulation results of an NMPC controlled vehicle based on bicycle model dynamics used in [1]. The results in this section are limited to the implementation of vehicle dynamics and the fromulation of an NMPC to drive it to a reference position on the global xy-coordinate space. This section does not include autonomous navigation on a track (lane-keeping) and obstacle avoidance, as these will be discussed in a separate post. 
</p>

<p>
The free-body diagram of the bicycle model (inspired by Figure 1 in [1]) is shown in the figure below.
</p>

<div>
<img src="/FBD_bicycle_model_NMPC.svg" alt="FBD bicycle model -- NMPC" style="width: 500px; height: 500px; object-fit: cover;"/>
</div>

<p>
The two-wheel bicycle model in [1] is as follows:
</p>

<p>
$$
\begin{aligned}
&\dot{p}_x = v_x \cos{\psi} - v_y \cos{\psi}, \\
&\dot{p}_y = v_x \sin{\psi} + v_y \cos{\psi}, \\
&\dot{\psi} = \omega, \\
&m \dot{v}_x = F_{r, x} - F_{f, y} \sin{\delta} - F_{f, x} \cos{\delta} + m v_y \omega, \\
&m \dot{v}_y = F_{r, y} + F_{f, y} \cos{\delta} + F_{f, x} \sin{\delta} - m v_x \omega, \\
&J_z \dot{\omega} = l_f F_{f, y} \cos{\delta} + l_f F_{f, x} \sin{\delta} - l_r F_{r, y}.
\end{aligned}
$$
</p>

<p>
with
</p>

<p>
$$
\begin{aligned}
&\alpha_f = - \arctan\left(\frac{\omega l_f + v_y}{v_x}\right) + \delta, \\
&\alpha_r = \arctan\left(\frac{\omega l_r - v_y}{v_x}\right), \\
&F_{f, y} = D_f \sin(C_f \arctan(B_f \alpha_f)), \\
&F_{r, y} = D_r \sin(C_r \arctan(B_r \alpha_r)), \\
&F_{f, x} = F_{r, x} = F_x, \\
&F_x = (C_{m1} - C_{m2} v_x)\tilde{d} - C_{m3} - C_{m4} v^2_x.
\end{aligned}
$$
</p>

<p>

Here, $m$ is the mass of the vehicle, $F_{r, x},\ F_{r, y},\ F_{f, x},\text{ and } F_{f, y} \in \R$ are the forces acting on the tires. In the subscript, the letters $r$ and $f$ indicate the rear and front tires, and the letters $x$ and $y$ indicate the longitudinal and lateral component of forces in the local vehicle body xy-frame respectively. The constants $l_f,\text{ and }  l_r \in \R_+$ are the distances from the Centre of Gravity $(C_g)$ to front and rear wheels respectively. 
</p>

<p>

The control inputs $\tilde{d} \in [0, 1],\text{ and }  \delta \in \R$ are the normalised forward acceleration PWM duty cycle of the electric drive train motor (refer [2]) and front wheel steering angle input in radians respectively. The states $p_x,\ p_y,\text{ and }  \psi \in \R$ are the vehicle's x position in meters, the y position in meters, and the heading in radians in the global frame of reference respectively. The states $v_x,\ v_y,\text{ and }  \omega \in \R$ are the vehicle's x velocity in meters-per-second, the y velocity in meters-per-second, and the angular rotation rate around the z-axis in radians-per-second in the local body-fixed frame of reference respectively. 
</p>

<p>

The constants $B_f$, and $B_r \in \R$ are the *stiffness factors*, $C_f$, and $C_r \in \R$ are the *shape factors* $D_f$, and $D_r \in \R$ are the *peak factors*, and $C_{m1}$, $C_{m2}$, $C_{m3}$, and $C_{m4} \in \R_+$ are empirical parameters as described in [1]. The parameters $\alpha_f$ and $\alpha_r$ are the slip angles. These equations and their parameters are described in equations (1) through (4) in [1]. Now the dynamics can be written as,
</p>

<p>
$$
\dot{\mathbf{z}}(t) = \mathbf{g}(\mathbf{z}(t),\ \mathbf{u}(t)).
$$
</p>

<p>
with
</p>

<p>
$$
\begin{aligned}
&\mathbf{z}(t) = [p_x(t),\ p_y(t),\ \psi(t),\ v_x(t),\ v_y(t),\ \omega(t)]^\mathsf{T}, \\
&\mathbf{u}(t) = [\tilde{d}(t),\ \delta(t)]^\mathsf{T}, \\
&\mathbf{g} : \R^{n_x} \times \R^{n_u} \rightarrow \R^{n_x}, \\
&n_x = 6 \text{ and } n_u = 2.
\end{aligned}
$$
</p>

<p>

Here, $\mathbf{z}(t)$ is the state vector and $\mathbf{u}(t)$ is the input vector. Using forward Euler method, the discretised system for some small sampling time $T >0$ can be written as,
</p>

<p>
$$
\mathbf{z}(k + 1) = \mathbf{z}(k) + T \cdot \mathbf{g}(\mathbf{z}(k), \mathbf{u}(k)), \ k \in \Z_+.
$$
</p>

<p>
Throughout this article, the vehicle parameters are taken as described in Table 1 in [1]. They are given (estimated) as,
</p>

| Param    | Value            | Param    | Value              | Param    | value                             |
| -------- | ---------------- | -------- | ------------------ | -------- | --------------------------------- |
| $l_f$    | $0.178$ m        | $l_r$    | $0.147$ m          | $m$      | $5.6292$ kg                       |
| $J_z$    | $0.204$ kg m$^2$ | $B_f$    | $9.242$            | $B_r$    | $17.716$                          |
| $C_f$    | $0.085$          | $C_r$    | $0.133$            | $D_f$    | $134.585$ N                       |
| $D_r$    | $159.919$ N      | $C_{m1}$ | $20$ N             | $C_{m2}$ | $6.92 \times 10^{-7}$ kg s$^{-1}$ |
| $C_{m3}$ | $3.99$ N         | $C_{m4}$ | $0.67$ kg m$^{-1}$ | $M$      | $1674$                            |

<p>

Notice that the value of $C_{m2}$ is almost negligible, this will be bought up in a later post. The goal is to formulate an NMPC that minimises the distance between the desired final position $\mathbf{p}_d = [p_x^d,\ p_y^d]^\mathsf{T}$ and the position of the vehicle $\mathbf{p}_N = [p_x^N,\ p_y^N]^\mathsf{T}$ after $N$ (prediction horizon) time steps. This can also be interpreted as asking the MPC controller to take the system as close as possible to the desired final position with in a given time of $N \cdot T$ seconds. Whilst doing so, the controller shall ensure that the system obeys the state and input (safety) constraints, given as
</p>

<p>
$$
\begin{aligned}
&v_x \in \bm{\Gamma} = [0,\ 5], \\
&\begin{bmatrix} \ \tilde{d} \ \\ \ \delta \ \end{bmatrix}  \in \mathbf{U} = [0,\ 1] \times \left[-\frac{\pi}{3},\ \frac{\pi}{3}\right].
\end{aligned}
$$
</p>

<p>

It should also be of priority that the system is not subjected to violently arbitrary accelerations and steering angles. This can be formulated as aiming for a minimum *jerk* trajectory and can be modeled as minimising the *distance* between successive input vectors. With this, the NMPC formulation takes the following form,
</p>

<p>
$$
\begin{aligned}
\mathbb{P}_N(\mathbf{z}(0), {}^\star\mathbf{u}_{\text{prev}}) :\ &\underset{\mathbf{u}}{\text{minimise}}\ \|\mathbf{p}_N - \mathbf{p}_d\|_{\mathbf{Q}_{1}}^2 + \sum_{k=0}^{N-1} \|\mathbf{u}(k) - \mathbf{u}(k-1)\|_{\mathbf{Q}_2}^2 \\
\text{s.t.}\ & \mathbf{z}(0) = \mathbf{z}(N), \\
\ &\mathbf{u}(-1) = {}^\star\mathbf{u}_{\text{prev}}, \\
\ &\mathbf{z}(k+1) = \mathbf{g}(\mathbf{z}(k), \mathbf{u}(k)),\ k \in \Z_{[0, N-1]}, \\
\ &\mathbf{u}(k) \in \mathbf{U},\ k \in \Z_{[0, N-1]}, \\
\ &v_x(k) \in \bm{\Gamma},\ k \in \Z_{[0, N-1]}, \\
\ &\mathbf{Q}_1 \in \mathbb{S}^{2}_{++}, \text{ and} \\
\ &\mathbf{Q}_2 \in \mathbb{S}^{n_u}_{++}.
\end{aligned}
$$
</p>

<p>

**Note:** The control input ${}^\star\mathbf{u}_{\text{prev}}$ is the control input used to drive the vehicle during the previous iteration.
</p>

<p>

The minimisation problem $\mathbb{P}_N$ is solved at every time step in a loop. For the first iteration the control input $\mathbf{u}(-1)$ is the input that the system began with i.e., the input at $t=-T$. The idea of NMPC is, at $t=0$, solve $\mathbb{P}_N$ for ${}^\star \mathbf{U}$, where ${}^\star \mathbf{U} = \underset{\mathbf{u}}{\text{argmin}}\ \mathbb{P}_N$. 
</p>

<p>

Then, take the first computed input ${}^\star\mathbf{u}(0)$, apply it to the system and take a measurement of the resulting state ${}^\star\mathbf{z}(1)$. With $\mathbf{u}(-1) = {}^\star\mathbf{u}(0)$ and $\mathbf{z}(0) = {}^\star\mathbf{z}(1)$, proceed to iteration two at $t=T$ to solve $\mathbb{P}_N$ again, obtain the new ${}^\star\mathbf{u}(0)$, apply it to the system to get a new ${}^\star\mathbf{z}(1)$ and move on to iteration three at $t = 2T$. This procedure is repeated indefinitely or till a set number of iterations is reached. Throughout this article the total number of simulation steps (iterations) is denoted by $\bm{S}$.
</p>

<p>

**Note:** The reader might notice that this iterative method only works assuming that the computation of ${}^\star \mathbf{U}$ takes less than $T$ seconds during each iteration. It is indeed the case and it is not clear what happens if each computation takes longer than $T$ seconds. This hurts the confidence on the designed system, as it might produce unexpected results. Thus, the selection of $T$ shall be of careful consideration owing to the trade off between the accuracy of the discrete approximation and computational limitations. 
</p>

<p>

This setup is implemented and simulated in Python3 with $N=50,\ \bm{S} = 300,\ T=0.01 \text{ s},\ \mathbf{z}(0) = 0_{n_x \times 1} \text{ and }\mathbf{p}_ d = [5, 5]^\mathsf{T}$. The following GIF shows the simulation results.
</p>

<div>
<img src="/mpc_car_st_slope_no_brake.gif" alt="Simulation results" width="640" height="400">
</div>

<p>

As it can be seen in the GIF, the NMPC controlled vehicle indeed drove towards (close to) the destination $[5, 5]^\mathsf{T}$, but it didn't converge. It could have been a matter of tuning $\mathbf{Q}_1$ and $\mathbf{Q}_2$, but before tuning them, the vehicle dynamics need to be modified to add a braking system. Remember, the end goal is to have an NMPC controlled autonomous vehicle that is *road safe*. These preliminary results are good enough for now as the weight matrices $\mathbf{Q}_1$ and $\mathbf{Q}_2$ might need retuning when the dynamics have changed.
</p>

## ఠ Section 2: Let's Brake _(or rather not)_ the system

<p>

This section discusses the addition of a braking system to the vehicle dynamics. The dynamics shall be modified with caution, as it might end up as a broken (*unsolvable*) system. In the end, a simulation result is provided for the modified system under the same scenario as in Section 1. Going further, the GIF playback will be slowed down when necessary to help with tracking changes in the states and control inputs. But, the time axis in the graph can always be used as a real-time time reference.
</p>

<p>

In the equation of $F_x$ in the dynamics, the term $C_{m3}$ might be acting as the initial resistance of static friction between the tires and the road, while the term $C_{m4}v^2_x$ acts as the force due to air drag as the vehicle starts to move. Though, this captures the initial and forward moving dynamics, this model only accurate when $\tilde{d} > 0$. Leaving $\tilde{d} = 0$, then at $t=0$, $F_x = -C_{m3}$, which makes $v_x < 0$. Since, $v_x^2 > 0$, the negative x-force on the vehicle increases quadratically as $F_x = -C_{m3} - C_{m3}v_x^2$. Also, when the vehicle starts moving i.e., $|v_x| > 0$, the term $C_{m3}$ should vanish, or should switch to rolling fictional force between the tires and the road. It would also make sense to make $\text{sign}(C_{m4}) = \text{sign}(v_x)$ to properly model air drag. These will require additional modifications in the dynamics that might require conditional expressions, which are currently not supported by OpEN. Nonetheless, this inaccuracy should be kept in mind when interpreting the simulation results.
</p>

<p>

Due to the above reasons, moving forward it is assumed that the simulation results are valid as long as $\tilde{d} > 0$ and $v_x > 0$ (this excludes the lower bound of $v_x$ in the above formulation) and any part of the simulation that violates these conditions (even when $v_x = 0$) shall be considered inaccurate. This also means that, going forward $C_{m3}$ is assumed to be the rolling frictional force between the tires and the road.
</p>

<p>
With these conditions under consideration, the addition of the braking system can be done by the following modifications to the system dynamics,
</p>

<p>
$$
\begin{aligned}
&F_\text{brake} = \mu_{KF}\tilde{b}, \\
&\tilde{b} \in [0, 1], \\
&\mu_{KF} \in \R_+, \\
&F_x = (C_{m1} - C_{m2} v_x)\tilde{d} - C_{m3} - C_{m4} v^2_x - F_\text{brake}.
\end{aligned}
$$
</p>

<p>

Here, $\mu_{KF}$ is a constant that relates the normalised brake input $\tilde{b}$ and the actual braking force $F_\text{brake}$, not exactly equal to the coefficient of kinetic friction. From here on the value of $\mu_{KF}$ is assumed to be $0.1$. It should be noted that the $\text{sign}(F_\text{brake})$ is only valid if $v_x > 0$. With these modifications to the dynamics, starting from $\mathbf{z}(0) = 0_{n_x \times 1}$, the modified system is simulated in Python3 with $N=50,\ \bm{S} = 300,\ T=0.01, \text{ and }\mathbf{p}_d = [5, 5]^\mathsf{T}$ and the results are shown in the following GIF.
</p>

<div>
<img src="/mpc_car_st_slope_with_brake.gif" alt="Simulation results" width="640" height="400">
</div>

<p>

As it can be seen from the results, indeed the vehicle reaches close to $z_d = [5, 5, 0, 0, 0, 0]^\mathsf{T}$ i.e., $\mathbf{p}_d = [5, 5]^\mathsf{T}$. While this is a good result, it should also be noted that between $t=2.25$ and $t=2.5$, the controller is applying acceleration PWM $(\tilde{d})$ and brake input $(\tilde{b})$ simultaneously. The fix for this undesirable usage of $\tilde{d}$ and $\tilde{b}$, and the addition of lane-keeping and obstacle avoidance will be discussed in a future article.
</p>

## References

<p>

[1] Cataffo, Vittorio & Silano, Giuseppe & Iannelli, Luigi & Puig, Vicenç & Glielmo, Luigi. (2022). A Nonlinear Model Predictive Control Strategy for Autonomous Racing of Scale Vehicles. 10.1109/SMC53654.2022.9945279. 
</p>

<p>

[2] Liniger, A., Domahidi, A., & Morari, M. (2017). Optimization-Based Autonomous Racing of 1:43 Scale RC Cars. ArXiv. https://doi.org/10.1002/oca.2123
</p>