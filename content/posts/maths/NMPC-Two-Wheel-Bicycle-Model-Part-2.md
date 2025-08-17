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

> <b>Previous post:</b> <a href="../NMPC-Two-Wheel-Bicycle-Model" target="_blank">Nonlinear Model Predictive Control (NMPC) for autonomous navigation of small scale cars</a>.<br/>

<p>This post is the continuation of a previous post on <a href="../NMPC-Two-Wheel-Bicycle-Model" target="_blank">Nonlinear Model Predictive Control (NMPC) for autonomous navigation of small scale cars</a>, which served as an introduction to NMPC based autonomous navigation. The previous post also featured the addition of the vehicle's braking system along with its simulation results. The discussion ended with a note on the undesirable simultaneous use of the acceleration PWM input $(\tilde{d})$ and the brake input $(\tilde{b})$ by the controller. 
</p>

<p>
<b>Note:</b> It is recommended that the reader should first go through the previous post to help understand what follows in this post. The simulation code can be found at the following GitHub repository <b>(Insert link here)</b>.</p>

<p>
<b>Aim of this article:</b>
The aim of this article is to demonstrate the abilities of an NMPC controlled vehicle to navigate around moving obstacles without exiting a predetermined track. For simplicity, throughout this article, the predetermined track is selected to be a simple ellipse and the obstacles will be approximated to their respective elliptical boundaries. 
</p>

<p>In this post, a simple fix for this undesirable simultaneous control activation will be discussed under Section 1. Under that same section, the addition of the obstacle avoidance feature will be discussed along with a showcase of the corresponding simulation results.</p>

<p>In Section 2, the discussion will be focused on the introduction of the lane-keeping feature by building upon a high-level reference generator algorithm. This reference generator algorithm is inspired by the work in [1]. After showcasing the corresponding simulation results the section ends on an interesting note about the safety constraints.</p>

<p>The post ends with Section 3, where the obstacle avoidance feature will be combined with lane-keeping. In this section a special function will be introduced to the reference generator algorithm that helps with real-time (dynamic) obstacle avoidance. The discussion then moves on to how this special function can help with avoiding obstacles in motion and ends with the showcase of corresponding simulation results.</p>

<p>The software used for the simulations in this post are same as in the previous post and are listed below.</p>

### Software Used
1. <b>[Python3](https://www.python.org/):</b> An open-source high-level interpreted programming language.
2. <b>[NumPy](https://numpy.org/):</b> Numerical Python in short NumPy is an open-source scientific computational tool for multidimensional array operations. 
3. <b>[Matplotlib](https://matplotlib.org/):</b> Is an open-source visualisation library for Python.
4. <b>[CasADi](https://web.casadi.org/):</b> Is an open-source tool for nonlinear optimisation and algorithmic (automatic) differentiation. 
5. <b>[Python-control](https://python-control.readthedocs.io/en/0.10.1/):</b> An open-source control systems library for Python.
6. <b>[OpEn](https://alphaville.github.io/optimization-engine/):</b> Optimization Engine in full, is a framework for designing and solving non-linear and non-convex optimisation problems. 

<p>Once again the notations from the previous post are reintroduced below.</p>

## Notation
| Symbol                | Expansion              | Comment                     |
|--------               |-----------             |---------                    |
|$0_{n \times m}$       | $[0]_{n \times m}$     | An $n \times m$ null matrix.|
|$1_{n \times m}$       | $[1]_{n \times m}$     | An $n \times m$ matrix with every element equal to 1.|
|$I_{n}$                | $I_{n \times n}$       | An $n \times n$ identity matrix.|
|$\N$                   |$\\{0,\ 1,\ 2, \ldots\\}$| Set of all natural numbers.|
|$\N_{[i,\ j]}$         |$\\{k \in \N: i \le k \le j\ \forall\ i,\ j \in \N \\}$| Set of all natural numbers between $i$ and $j$ inclusive.|
|$\R$                   |$(-\infty,\ \infty)$    | Set of real numbers.        |
|$\R^n$                 |$\\{x_ {n \times 1}: x_i \in \R \ \forall \ i \in \N_{[1,\ n]}\\}$| Set of all $n \times 1$ real vectors.|
|$\R^{n \times m}$      |$\\{A_ {n \times m}: a_ {i,\ j} \in \R \ \forall \ i \in \N_{[1,\ n]},\text{ and } j \in \N_{[1,\ m]}\\}$| Set of all $n \times 1$ real vectors.|
|$\R_{[i,\ j]}$ or $[i,\ j]$ |$\\{k \in \R: i \le k \le j\ \forall\ i,\ j \in \R\\}$| Set of real numbers from $i$ to $j$ inclusive.        |
|$\R_-$ or $\R_{\lt 0}$ |$(-\infty,\ 0)$         | Set of all negative real numbers.|
|$\R_+$ or $\R_{\ge 0}$ |$[0,\ \infty)$          | Set of all positive real numbers.|
|$\mathbb{S}_+^n$       |$\\{A\in \R^{n \times n}: x^\mathsf{T}Ax \ge 0 \ \forall \ x \in \R^n \\}$| Set of all symmetric positive semi-definite matrices.|
|$\mathbb{S}_{++}^n$    |$\\{A \in \R^{n \times n}: x^\mathsf{T}Ax \gt 0 \text{ and } x^\mathsf{T}Ax = 0 \implies x = 0_n \ \forall \ x \in \R^n \\}$| Set of all symmetric positive definite matrices.|

<p>

Similar to the previous post the Telugu letter ttha, <b>ఠ</b>, will be used at the beginning of each section to indicate the introduction of a *road safety* feature.</p>

## ఠ Section 1: Obstacle avoidance

<p>

In this section, obstacle avoidance feature (*constraint*) will be added to the NMPC formulation. This feature is of importance for two reasons, first being that it extends the concept of *safety* for both the vehicle and the environment that it is navigating in. The obstacle could be a tree, another vehicle, or a human. Enabling such a feature prohibits the vehicle from injuring living beings and prevents self-damage when navigating around objects. The second reason being that this concept can be extended and reused when constraining the vehicle to stay on the track (road).
</p>

<p>

Before introducing this feature, the fix for simultaneous activation of $\tilde{d}$ and $\tilde{b}$ should be addressed. The solution for this problem is being introduced here, instead of in Section 2 of the previous post is because just like for obstacle avoidance, the fix has to do with modifying the formulation (not the dynamics as in Section 2 of the previous post). The simplest fix is to modify the cost function in the minimisation problem as follows,
</p>

<p>
$$
\|p_N - p^d\|_{Q_{1}}^2 + \sum_{k=0}^{N-1} \left(\|u_k - u_{k-1}\|_{Q_2}^2 + R \cdot \tilde{d}_k \cdot \tilde{b}_k \right)
$$
</p>

<p>

Where $R \gt 0$ and must be a very large number. This imposes that the best (*optimal*) way to avoid a large cost is to make the selected control actions $\tilde{d}$ and $\tilde{b}$ not be greater than zero at the same time. Since $\tilde{d}$ and $\tilde{b} \in [0, 1]$, their product need not be squared in the cost function.
</p>

<p>

The obstacle avoidance feature shall be introduced in the form of additional constraints to the formulation. In general, it is easy to both design and implement ellipsoidal constraints and for this reason, moving forward all obstacles will be approximated as either ellipses or circles with radii selected in such a way that guarantees *safety* (collision avoidance). For that, the radii shall address both the size of the obstacle plus some safe distance and a buffer zone that takes the size of the vehicle into account. 
</p>

<p>

The buffer zone is needed because the position dynamics of the car are calculated for the center of gravity $(C_g)$ of the vehicle. This means, if the buffer zone in excluded, the calculated optimal path might end-up entering some part of the vehicle inside the safety circle (further clarification provided after presenting the simulation results). It is also to be ensured that the geometric centre of the object coincides with the centre of the ellipse. From here on, such ellipses are called as safety circles.
</p>

<p>
The below figure shows a colour coded representation of the composition of a safety circle for a rectangular object. The object (in white) is first approximated into an object ellipse in blue. Then a green safety ellipse is layered on top of the object ellipse to account for the safety distance. Finally, the dark teal obstacle ellipse forms the final layer taking into account the vehicle size (its width).
</p>

<div>
<img src="/safety_circle.svg" alt="FBD bicycle model -- NMPC" style="width: 550px; height: 450px; object-fit: cover;"/>
</div>

<p>

For demonstration, an obstacle is placed with its centre at the origin with the radius of its safety circle equal to $2 + \frac{w_c}{2}$ mts, where $w_c = 0.25$ mts is the width of the car. The corresponding obstacle avoidance constraint can be formulated as follows,
</p>

<p>
$$
\begin{aligned}
&p_{x, k}^2 + p_{y, k}^2 > \left(2 + \frac{w_c}{2}\right)^2 \\
\implies &\left(2 + \frac{w_c}{2}\right)^2 - p_{x, k}^2 - p_{y, k}^2 < 0,\ \forall k \in \Z_{[1, N]}
\end{aligned}
$$
</p>

<p>

Needless to mention that it should be ensured that $p^2_{x, 0} + p^2_{y, 0} \gt (2 + \frac{w_c}{2})^2$ i.e., the system does not start from a position inside the safety circle. With these changes, the modified NMPC formulation can be written as follows,
</p>

<p>
$$
\begin{aligned}
\mathbb{P}_ N(z, u^\star_ {\text{p}}) :\ &\underset{\bm{u}_ N}{\text{minimise}}\ \|p_ N - p^d\|_ {Q_ {1}}^2 + \sum_ {k=0}^{N-1} \left( \|u_ k - u_ {k-1}\|_ {Q_ 2}^2 + R \cdot \tilde{d}_k \cdot \tilde{b}_k \right) \\
\text{s.t.}\ & z = z^\star_ {\text{p}}, \\
\ &u_ {-1} = u^\star_ {\text{p}}, \\
\ &z_ {k+1} = \mathbf{g}(z_ k, u_ k),\ k \in \N_ {[0, N-1]}, \\
\ &u_ k \in \mathbf{U},\ k \in \N_ {[0, N-1]}, \\
\ &v_ {x, k} \in \bm{\Gamma},\ k \in \N_ {[0, N-1]}, \text{ and} \\
\ &\left(2 + \frac{w_c}{2}\right)^2 - p^2_{x, k} - p^2_{y, k} < 0,\ k \in \Z_{[1, N]}.
\end{aligned}
$$
</p>

<p>

Here, $Q_ 1 \in \mathbb{S}^{2}_ {++}$ and $Q_ 2 \in \mathbb{S}^{n_ u}_ {++}$. In Python3, the modified stage cost function can be written as,
</p>

```python
# Set a large value for R
R = 1000000
# Define the new stage cost to fix the undesired simultaneous actuation
# of the acceleration and brake inputs by incurring a huge penalty.
def stage_cost(u_current, u_prev):
    return (u_current.T - u_prev.T) @ Q2 @ (u_current - u_prev)\
            + R * u_current[0] * u_current[2]  # Fix
```

<p>The modified formulation of on the NMPC in Python3 can be done as follows,</p>

```python
penality_constraint_position = 0
# Formulate the NMPC total cost and constraints
for t in range(N):
    # Set current time step input
    u_current = u_seq[t * nu : (t + 1) * nu]
    # Add stage cost to total cost for the current time step
    total_cost += stage_cost(u_current, u_prev)
    # Set current state to resultant state of the current input
    z_t = dynamics(z_t, u_current)  
    # Change previous input to current input for next iteration
    u_prev = u_current  
    # Add set vx maximum constraint
    penality_constraint_vx += cs.fmax(0, cs.norm_1(z_t[3]) - vmax) 
    # Add set vx minimum constraint 
    penality_constraint_vx += cs.norm_1(cs.fmin(vmin, z_t[3])) 
    # incur penalty if inside the obstacle otherwise 0 
    penality_constraint_position += cs.fmax(0, (2 + car_width)**2 - z_t[0]**2 - z_t[1]**2)  

# Add terminal cost to total cost
total_cost += terminal_cost(z_t)

# Define rectangular constraints for inputs
U = og.constraints.Rectangle(umin_seq, umax_seq)

# Define and build the problem
problem = og.builder.Problem(u_seq, problem_params, total_cost)
problem = problem.with_constraints(U)
# Add the obstacle constraints to velocity constraints
problem = problem.with_penalty_constraints(penality_constraint_vx 
                                    + penality_constraint_position)
```

<p>

The simulation results of this formulation with $N=50,\ S = 300,\ T=0.01$ s, $R = 1000000$, $p_0 = [-2, 2]^\mathsf{T}$, and $p^d = [1, 4]^\mathsf{T}$ are shown in the following GIF,
</p>

<div>
<img src="/mpc_car_obst_2m_6.gif" alt="Simulation results" width="640" height="400">
</div>

<p><b>Noe:</b> The above representation of the safety circle, in blue, excludes the car width in its radius for visualisation purpose. Had it been included, it would appear as if the car is driving inside the obstacle. Going forward this would be the case, where the car width will be included in the constraint formulation but will be excluded from the visual representation.</p>

<p>

From the above GIF, it can be seen that the NMPC controlled vehicle is indeed avoiding the obstacle and reaching close to the destination. It must be observed that before $t=1.5$, the vehicle drove close (tangential) to the safety circle because that is an optimal path that minimises the above formulated cost function. This is the reason why the radii (radius in this case) must include a buffer zone around the actual object. This ensures that the vehicle never comes *dangerously* close to the actual object. 
</p>

<p>

Absence of the buffer zone would mean that the calculated optimal path only prevents the $(C_g)$ of the vehicle from entering the blue circle. This makes the rest of the vehicle free to enter (<em>collide</em>) with the object.
</p>

<p>

It should also be noted that before $t<1$, there was a noticeable magnitude of $v_y$. This could probably be the case that the vehicle is drifting, which is undesirable when it comes to *road safety*. However, this can simply be fixed by tuning the wright matrices $Q_1$ and $Q_2$, which will be done at a later point. But, for Section 2 (the next section), the goal is to have the NMPC drive on a predetermined track without exiting.
</p>

## ఠ Section 2: Lane-keeping (sort of)

<p>

In order for the vehicle to follow a particular (desired) path, the reference position not only need to keep changing but also should follow the desired path. The change of reference position should be systematic and deterministic, for such, a higher-level reference generator algorithm needs to be introduced. This reference generator sits one level above the NMPC controller, thus higher-level, and generates (changes the old reference) a new reference positions at every instant.
</p>

<p>

This makes the vehicle (enabled by the NMPC controller) continuously chase after the changing reference. If the reference generator enables the changing reference to trace a desired path, this would, to some extent, guarantee that the vehicle also follows that particular path. One particular limitation of this <em>guarantee</em> will be evident from the simulation results of this section, emphasising on the importance of formulating proper constraints.
</p>

<p>

Inspired from the reference generator in [1], the reference generator used for this article can be summarised in the following algorithm.
</p>

### Reference generator algorithm

<p>

Consider a path that is sampled into $s \in \N_{>0}$ equidistant (Euclidean distance between any two successive samples is constant) samples. Let the ordered collection of this sampled space be $\bm{p}^\text{traj}$. Let the $i^\text{th}$ point (element) in this collection be represented as $\bm{p}^\text{traj}[i] = p^\text{traj}_ i = [p^\text{traj}_ {x, i},\ p^\text{traj}_ {y, i}]^\mathsf{T}$.
</p>

<p>

Let $p^\text{traj}_ {i_ p}$ be the projection of the current position of the vehicle $p^c = [p_x^c,\ p_y^c]^\mathsf{T}$ on to the sampled space. The goal is to find a reference position in this sampled space, that is $l \in \N_ {>0}$ points ahead of $p^\text{traj}_ {i_ p}$. Then the reference generator algorithm can be written as,
</p>

<p>

<b>Algorithm steps:</b>

1. Sample the desired trajectory into an ordered collection of equidistant position points as: $\bm{p}^\text{traj}$.
2. Project the current position of the vehicle on to $\bm{p}^ \text{traj}$ and find the corresponding index as: $i_ p = \text{min}\left\\{\underset{i}{\text{argmin}} \\|p^c - p^\text{traj}_ i\\|^2 \right\\}$.
3. With $p^\text{traj}_ {i_ p}$ being the closest point to the vehicle on the trajectory, find a reference position that is $l$ points ahead as: $p^d = p^\text{traj}_ {i_ p + l} = \bm{p}^\text{traj}[i_ p + l]$.
4. Set $p^d$ as the new reference the NMPC controller and run the simulation for one iteration.
5. repeat steps $2$ to $4$ till the end of the simulation.
</p>

<p><b>Note:</b> This approach for finding $i_ p$ is naive, in that the reference generator can be easily fooled to generate a reference position that is either too far ahead or (too far) behind the current vehicle position. In simple terms, this reference generator approach cannot guarantee the robustness of the NMPC controlled system for any arbitrary path. It is a good exercise for the reader to experiment and see why this is true. Addressing this issue is beyond the scope of this article and the reader is encouraged to do their own research.</p>

<p>

This algorithm is implemented in Python3 with a elliptical track of width $w = 1$ m. The major axis radius of the centre of the track is equal to $a = 5$ m and the corresponding minor axis radius is equal to $b = 4.5$ m. The track centre lane is sampled at a constant rate of $s_ p = 40$ samples per metre and the number of points to look ahead is set to $l = 120$ samples. The system is simulated with $N=50$, $S=1500$, $T=0.01$ s, and $z=[5, 0]^\mathsf{T}$ and the realtime results are shown in the GIF below.
</p>

<div>
<img src="/track_drive_higer_resolution_track.gif" alt="Simulation results: with track sampling = 40 samples/m and 120 samples look ahead." width="640" height="400">
</div>

<p>

In the GIF above, the black path represents the elliptical track and yellow curve represents the centre division lane of the elliptical path. It seems like the vehicle is successfully navigating around the track. A slowed down version of the simulation results are shown in the GIF below. 
</p>

<p>

For further clarity, a white dot representing the projection of the current position of the vehicle on to the centre lane ($p^\text{traj}_ {i_ p}$) and a red dot representing the generated reference position $p^d$ are added to the plot.</p>

<div>
<img src="/track_drive_higer_resolution_track_with_ref.gif" alt="Simulation results: A slowed version of the simulation results with track sampling = 40 samples/m and 120 samples look ahead." width="640" height="400">
</div>

<p>

As it can be seen from the simulation results above, the vehicle successfully follows (chases after) the reference without exiting the track (is it though?). To verify this, the actual trajectory of the vehicle is plotted with a blue trail in the GIF below.
</p>

<div>
<img src="/track_drive_higer_resolution_track_with_ref_trail.gif" alt="Simulation results: with vehicle trail in blue." width="640" height="400">
</div>

<p>

Though, it seems like the trail of the vehicle never exits the track, this is only because the choice of the track dimensions and the look ahead $l = 120$ samples coincidentally leave enough time and space for the vehicle to trace an elliptical path of comparable dimensions to the actual track. Had the dimensions of the track been a bit different or the look ahead been any larger, the vehicle could have exited the track. To prove this, the same simulation is repeated with $l = 200$ samples and the results are shown in the GIF below.
</p>

<div>
<img src="/track_drive_higer_resolution_track_distant_destination.gif" alt="Simulation results: track sampling = 40 samples/m and 200 samples look ahead" width="640" height="400">
</div>

<p>

A slower version with reference and vehicle projections is shown in the GIF below.
</p>

<div>
<img src="/track_drive_higer_resolution_track_distant_destination_with_ref.gif" alt="Simulation results: track sampling = 40 samples/m and 200 samples look ahead" width="640" height="400">
</div>

<p>

Now, it can be clearly seen that the NMPC controlled vehicle is only aiming to catch up to the reference and it staying on the track was a coincidence. This is because there is no constraint (<em>safety</em>) in the NMPC formulation that explicitly states that the vehicle must stay on the track. The same simulation with the vehicle trail is presented below.
</p>

<div>
<img src="/track_drive_higer_resolution_track_distant_destination_with_ref_trail.gif" alt="Simulation results: track sampling = 40 samples/m and 200 samples look ahead" width="640" height="400">
</div>

<p>

This is not true lane-keeping but rather the first step towards it. True lane-keeping is achieved when the system actively puts effort to not exit the track. This will be discussed in the next section, but for now this finding is important for two reasons:
1. Safety constraints must be explicit, meaning if it is not in the formulation, there is not guarantee that the NMPC controlled system will obey it.
2. Safety must take priority over reference tracking.
</p>

## ఠ Section 3: Lane-keeping + Obstacle Avoidance

<p>

In this section, the obstacle avoidance feature (constraint) will be combined with the lane-keeping feature to enable our NMPC controlled vehicle to navigate around obstacles on the track.
</p>

<p>

In the previous section, it was observed that the NMPC controlled vehicle is tracking the dynamic reference, but is prone to exiting the road. The fix for this is addressed in this section because it is closely related to the formulation of the obstacle avoidance constraints. As seen in Section (1), given the right obstacle avoidance constraint formulation, the NMPC controlled vehicle avoids (entering) collision with the obstacle.
</p>

<p>

What if the blue obstacle in the simulation results of Section (1) is not an obstacle, but a region in which the vehicle is prohibited to entre. Turns out, the formulation remains the same and the simulation results will also remain unaltered. This is because in the NMPC controller formulation, there is no mathematical difference between an obstacle avoidance constraint and a state constraint where the vehicle is prohibited to entre a circular (elliptical in this case) region. 
</p>

<p>

Now, that translates to prohibiting the vehicle from entering the inner elliptical region of the track as follows:
</p>

<p>
$$
\begin{aligned}
&\left(\frac{p_{x, k}}{a_ i + \frac{w_c}{2}}\right)^2 + \left(\frac{p_{y, k}}{b_ i + \frac{w_c}{2}}\right)^2 \gt 1 \\
\implies &1 - \left(\frac{p_{x, k}}{a_ i + \frac{w_c}{2}}\right)^2 - \left(\frac{p_{y, k}}{b_ i + \frac{w_c}{2}}\right)^2 \lt 0,\ \forall k \in \Z_{[1, N]}
\end{aligned}
$$
</p>

<p>

Here, $a_ i$ and $b_ i$ are the major and minor axis' radii of the inside edge of the track respectively. Similarly, for the exclusion of the outer region, the obstacle avoidance constraint can be reformulated as:
</p>

<p>
$$
\begin{aligned}
&\left(\frac{p_{x, k}}{a_ o + \frac{w_c}{2}}\right)^2 + \left(\frac{p_{y, k}}{b_ o + \frac{w_c}{2}}\right)^2 \lt 1 \\
\implies &\left(\frac{p_{x, k}}{a_ o + \frac{w_c}{2}}\right)^2 + \left(\frac{p_{y, k}}{b_ o + \frac{w_c}{2}}\right)^2 - 1 \lt 0,\ \forall k \in \Z_{[1, N]}
\end{aligned}
$$
</p>

<p>

Here, $a_ o$ and $b_ o$ are the major and minor axis' radii of the outside edge of the track respectively.
</p>

<p><b>Note:</b> For simplicity, the road tracks demonstrated in this article are assumed to have their centres coincide with the origin, their major axis parallel to the global x-axis, and their minor axis parallel to the global y-axis. Nonetheless, the reintroduction of obstacle constraints below, features translated and/ or rotated elliptical constraints for the interested. While it is possible to extend this and make arbitrary paths by <em>stitching</em> parts of ellipses together, it is beyond the scope of this article.</p>

<p>

Before re-introducing the 
</p>

<p>
$$
\begin{aligned}
&p'_{x, k} = p_{x, k} - c_ x, \\
&p'_{y, k} = p_{y, k} - c_ y, \\
&\tilde{p}_{x, k} = p'_{x, k}\cos(\theta) - p'_{y, k}\sin(\theta), \\
&\tilde{p}_{y, k} = p'_{x, k}\sin(\theta) + p'_{y, k}\cos(\theta), \\
&\left(\frac{p_{x, k}}{a_ o + \frac{w_c}{2}}\right)^2 + \left(\frac{p_{y, k}}{b_ o + \frac{w_c}{2}}\right)^2 \lt 1 \\
\implies &\left(\frac{p_{x, k}}{a_ o + \frac{w_c}{2}}\right)^2 + \left(\frac{p_{y, k}}{b_ o + \frac{w_c}{2}}\right)^2 - 1 \lt 0,\ \forall k \in \Z_{[1, N]}
\end{aligned}
$$
</p>

## References
<p>

[1] Cataffo, Vittorio & Silano, Giuseppe & Iannelli, Luigi & Puig, Vicenç & Glielmo, Luigi. (2022). A Nonlinear Model Predictive Control Strategy for Autonomous Racing of Scale Vehicles. 10.1109/SMC53654.2022.9945279. 
</p>
