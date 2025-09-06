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

<p><b>Background:</b><p>
<p>This post is the continuation of a previous post on <a href="../NMPC-Two-Wheel-Bicycle-Model" target="_blank">Nonlinear Model Predictive Control (NMPC) for autonomous navigation of small scale cars</a>, which served as an introduction to NMPC based autonomous navigation. The previous post also featured the addition of the vehicle's braking system along with its simulation results. The discussion ended with a note on the undesirable simultaneous use of the acceleration PWM input $(\tilde{d})$ and the brake input $(\tilde{b})$ by the controller.</p>

<p>
<b>Note:</b> It is recommended that the reader should first go through the previous post to help understand what follows here. The simulation code can be found at the following GitHub repository ***Insert link here***.</p>

### Aim of this article:
<p>The aim of this article is to demonstrate the abilities of an NMPC controlled vehicle to navigate around moving obstacles without exiting a predetermined track. To that end, to keep things simple, the predetermined track is selected to be a simple ellipse and the obstacles will be approximated to their respective elliptical boundaries.</p>

### Article structure:
<p>Similar to the previous post, I intend to divided this article in to several sections each featuring simpler feature additions to the previous, and slowly build up towards the end goal. Again, here, the Telugu letter ttha, <b>ఠ</b>, will be used at the beginning of a section to indicate the introduction of a <em>road safety</em> feature.</p>

<p>Section $1$ of this post discusses a simple fix for the undesirable simultaneous activation of the control actions, the acceleration PWM input $(\tilde{d})$ and the brake input $(\tilde{b})$. The section also covers the addition of the obstacle avoidance feature along with a showcase of the corresponding simulation results.</p>

<p>In Section 2, the discussion will be focused on the introduction of the lane-keeping feature by building upon a high-level reference generator algorithm. This reference generator algorithm is inspired by the work in [1]. After showcasing the corresponding simulation results, the section ends on an interesting note about the safety constraints.</p>

<p>Under Section 3, the obstacle avoidance feature will be combined with the lane-keeping feature to produce an autonomous vehicle capable of navigating around known static obstacles whilst staying on a predetermined track. The section finishes with a showcase of the simulation results.
</p>

<p>
In Section 4, to add some <em>realism</em> to the simulation, a simple virtual sensor concept will be introduced to mimic the sensing mechanics of a real-world autonomous vehicle. The section ends with corresponding simulation results.</p>
</p>

<p>The post ends with Section 5, where a special function will be introduced to the reference generator algorithm that helps with <em>simulation-time</em> Dynamic Obstacle Switching and Avoidance (<b>DOSA</b>, pun intended). The discussion then moves on to how this special function can help with avoiding obstacles in motion, and ends with the showcase of corresponding simulation results.</p>

### Software Used
<p>The software used for the simulations in this post are same as in the previous post and are listed below.</p>

1. <b>[Python3](https://www.python.org/):</b> An open-source high-level interpreted programming language.
2. <b>[NumPy](https://numpy.org/):</b> Numerical Python in short NumPy is an open-source scientific computational tool for multidimensional array operations. 
3. <b>[Matplotlib](https://matplotlib.org/):</b> Is an open-source visualisation library for Python.
4. <b>[CasADi](https://web.casadi.org/):</b> Is an open-source tool for nonlinear optimisation and algorithmic (automatic) differentiation. 
5. <b>[Python-control](https://python-control.readthedocs.io/en/0.10.1/):</b> An open-source control systems library for Python.
6. <b>[OpEn](https://alphaville.github.io/optimization-engine/):</b> Optimization Engine in full, is a framework for designing and solving non-linear and non-convex optimisation problems. 

## Notation
<p>Once again the notation from the previous post is reintroduced below.</p>

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


## ఠ Section 1: Obstacle avoidance

<p>

In this section, obstacle avoidance feature (*constraint*) will be added to the NMPC formulation. This feature is of importance for two reasons, first being that it extends the concept of *safety* for both the vehicle and the environment that it is navigating in. The obstacle could be a tree, another vehicle, or a human. Enabling such a feature prohibits the vehicle from injuring living beings and prevents self-damage when navigating around objects. The second reason being that this concept can be extended and reused when constraining the vehicle to stay on a track (road).
</p>

<p><b>Part 1: <em>un-</em>Break Brakes</b></p>
<p>
Before introducing this feature, the fix for simultaneous activation of $\tilde{d}$ and $\tilde{b}$ should be addressed. The reason for not addressing this in Section 2 of the previous post is because, similar to obstacle avoidance, the fix has to do with modifying the formulation and not the dynamics as in Section 2 of the previous post. The simplest fix is to modify the cost function in the minimisation problem as follows,
</p>

<p>
$$
\|p_N - p^d\|_{Q_{1}}^2 + \sum_{k=0}^{N-1} \left(\|u_k - u_{k-1}\|_{Q_2}^2 + R \cdot \tilde{d}_k \cdot \tilde{b}_k \right)
$$
</p>

<p>

Where $R \gg 0$ and must be a very large number. This imposes that the best (*optimal*) way to avoid a large cost, is to make the selected control actions $\tilde{d}$ and $\tilde{b}$ not be greater than zero at the same time. Meaning, to avoid simultaneous activation of both the control actions. Since $\tilde{d}$ and $\tilde{b} \in [0, 1]$, their product need not be squared in the cost function.
</p>

<p>
In Python3, the modified stage cost function can be written as,
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

<p>
With this modified stage cost function, the corresponding simulations results starting from $z = 0_{n_x \times 1}$, the modified system is simulated in Python3 with $N=50,\ S = 300,\ T=0.01, \text{ and } p^d = [5, 5]^\mathsf{T}$ and the results are shown in the following GIF.
</p>

<div>
<img src="/mpc_car_st_slope_with_brake_constraint.gif" alt="Simulation results" width="640" height="400">
</div>

<p>
As it can be seen from the above GIF, given the same simulation setting as in Section 2 of previous post, the NMPC controller has avoided the simultaneous activation of $\tilde{d}$ and $\tilde{b}$. 
</p>

<p><b>Part 2: Obstacle tackle</b></p>
<p>
The task of avoiding obstacles (and collision avoidance) can be alternatively viewed as prohibiting (excluding) the vehicle from reaching (entering) certain regions (positions) of space. Mathematically, this is very similar to constraining the vehicle's velocity i.e., prohibiting the vehicle from reaching certain velocities. In this regard, the obstacle avoidance feature shall be introduced in the form of additional constraints to the formulation.</p> 

<p>In general, it is easy to both design and implement ellipsoidal constraints, and for this reason, moving forward all obstacles will be approximated as either ellipses or circles with radii selected in such a way that guarantees <em>safety</em> (collision avoidance). For that, the radii shall address both the size of the obstacle plus some safe distance and a buffer zone that takes the size of the vehicle into account. 
</p>

<p>

The buffer zone is needed because the position dynamics of the car are calculated for the center of gravity $(C_g)$ of the vehicle. This means, if the buffer zone is excluded, the calculated optimal path might end-up pushing some part of the vehicle into the prohibited region (further clarification provided after presenting the simulation results). It is also to be ensured that the geometric centre of the object coincides with the centre of the ellipse. From here on, although inaccurate, such ellipses are called as safety circles.
</p>

<p>
The below figure shows a colour coded representation of the composition of a safety circle for a rectangular object. The object (in white) is first approximated into an object ellipse in blue. Then a green safety ellipse is layered on top of the object ellipse to account for the safety distance. Finally, the dark teal obstacle ellipse forms the final layer taking into account the vehicle size (its width).
</p>

<div>
<img src="/safety_circle.svg" alt="FBD bicycle model -- NMPC" style="width: 550px; height: 450px; object-fit: cover;"/>
</div>

<p>

For demonstration, an obstacle is placed with its centre at the origin and the radius of its safety circle set to $2 + \frac{w_c}{2}$ mts, where $w_c = 0.25$ mts is the width of the car. The corresponding obstacle avoidance constraint can be formulated as follows,
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

Needless to mention that it should be ensured that $p^2_{x, 0} + p^2_{y, 0} \gt (2 + \frac{w_c}{2})^2$ i.e., the system (vehicle) does not start from a position within the safety circle. With these changes, the modified NMPC formulation can be re-written as follows,
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

Here, $Q_ 1 \in \mathbb{S}^{2}_ {++}$ and $Q_ 2 \in \mathbb{S}^{n_ u}_ {++}$. The modified formulation of on the NMPC in Python3 can be done as follows,</p>

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

<p><b>Note:</b> The above representation of the safety circle, in blue, excludes the car width (dark teal obstacle ellipse) only in its visualisation. Had it been included, it would falsely appear as if the car is driving inside the obstacle. Going forward this would be the case, where the car width will be included in the constraint formulation but will be excluded from the visual representation.</p>

<p>

From the above GIF, it can be seen that the NMPC controlled vehicle is indeed avoiding the obstacle and reaching close to the destination. It must be observed that, before $t=1.5$, the vehicle drives very close (tangential) to the safety circle. This is because, it is an optimal path that minimises the above formulated cost function. This is the reason why the radii (radius in this case) must include a buffer zone around the actual object. This ensures that the vehicle never comes <em>dangerously</em> close to the actual object. 
</p>

<p>

Absence of the buffer zone would mean that the calculated optimal path only prevents the $(C_g)$ of the vehicle from entering the blue circle. This makes the rest of the vehicle free to enter (<em>collide</em>) with the object.
</p>

<p>

It should also be noted that before $t<1$, there was a noticeable magnitude of $v_y$. This could mean that the vehicle is drifting, which is undesirable when it comes to <em>road safety</em>. However, this can simply be fixed by adding another velocity constraint on $v_y$, which is left to the reader as an exercise. But, for Section 2, the goal is to have the NMPC drive on a predetermined track without exiting.
</p>

## ఠ Section 2: Lane-keeping (sort of)

<p>
So far, we have seen how to get the vehicle to a desired position. In this section, we want to make the vehicle to keep traveling on a predetermined track without exiting. This introduces the need for dynamically switching between reference (desired) positions for two reasons, one is that a static reference will let the vehicle stall once reached, and the other is that we want the vehicle to travel (trace) any arbitrary path in general.
</p>

<p>

So, for the vehicle to follow a desired path, the reference position not only need to keep changing but also should follow the desired path. The change of reference position should be systematic and deterministic, for such, a higher-level reference generator algorithm needs to be introduced. This reference generator sits one level above the NMPC controller, thus higher-level, and generates (changes the old reference) a new reference position at every simulation instant (iteration).
</p>

<p>

This makes the vehicle (enabled by the NMPC controller) continuously chase after the changing reference. If the reference generator enables the changing reference to trace the desired path, this would, to some extent, guarantee that the vehicle also follows that particular path. One particular limitation of this <em>guarantee</em> will be evident from the simulation results of this section, emphasising on the importance of formulating proper constraints.
</p>

<p>

Inspired from the reference generator in [1], the reference generator used for this article can be summarised in the following algorithm.
</p>

### Reference generator algorithm

<p>

Consider a path that is sampled into $s \in \N_{>0}$ equidistant (Euclidean distance between any two successive samples is constant) samples. Let the ordered collection of this sampled space be $\bm{p}^\text{traj}$. Let the $i^\text{th}$ point (element) in this collection be represented as $\bm{p}^\text{traj}[i] = p^\text{traj}_ i = [p^\text{traj}_ {x, i},\ p^\text{traj}_ {y, i}]^\mathsf{T}$.
</p>

<p>

Let $p^\text{traj}_ {i_ p}$ be the <em>projection</em> of the current position of the vehicle $p^c = [p_x^c,\ p_y^c]^\mathsf{T}$ on to the sampled space. The goal is to find a reference position in this sampled space ($\bm{p}^\text{traj}$), that is $l \in \N_ {>0}$ points ahead of $p^\text{traj}_ {i_ p}$. Then the reference generator algorithm can be written as,
</p>

<p>

<b>Algorithm steps:</b>

1. Sample the desired trajectory into an ordered collection of equidistant position points as: $\bm{p}^\text{traj}$.
2. Project the current position of the vehicle on to $\bm{p}^ \text{traj}$ and find the corresponding index as: $i_ p = \text{min}\left\\{\underset{i}{\text{argmin}} \\|p^c - p^\text{traj}_ i\\|^2 \right\\}$.
3. With $p^\text{traj}_ {i_ p}$ being the closest point to the vehicle on the trajectory, find a reference position that is $l$ points ahead as: $p^d = p^\text{traj}_ {i_ p + l} = \bm{p}^\text{traj}[i_ p + l]$.
4. Set $p^d$ as the new reference the of NMPC controller and run the simulation for one iteration.
5. repeat steps $2$ to $4$ till the end of the simulation.
</p>

<p><b>Note:</b> This approach for finding $i_ p$ is naive, in that the reference generator can be easily fooled to generate a reference position that is either too far ahead or (too far) behind the current vehicle position. In simple terms, this reference generator approach cannot guarantee the robustness of the NMPC controlled system for any arbitrary path. It is a good exercise for the reader to experiment and see why this is true. Addressing this issue is beyond the scope of this article and the reader is encouraged to do their own research.</p>

<p>
Due to the increased complexity, the implementation of this algorithm and the rest of the code is divided in to three classes in Python3 as follows,
</p>

<ol> 
<li> <code>class BicycleModel:</code>
    <ol type="a">
        <li>Dependencies:</li>
            <ul>
                <li><code>NumPy:</code> For linear algebra and trigonometry.</li>
                <li><code>CasADi:</code> For <code>OpEN</code> compatible trigonometric functions.</li>
            </ul>
        <li>Implements:</li>
            <ul>
                <li>Continuous and discrete time bicycle model dynamics using <code>NumPy</code>, as per [1].</li>
                <li><code>OpEn</code> compatible discrete time bicycle model dynamics using <code>CasADi</code>, as per [1].</li>
            </ul>
        <!-- <li>Methods:</li>
            <ol type="i">
                <li><code>__init__:</code></li>
                    <ul>
                        <li>Argument <code>Ts:</code> Sampling time of discrete time bicycle model dynamics.</li>
                        <li>Initialises required simulation variables of the bicycle model.</li>
                    </ul>
                <li><code>dynamics_generic_dt(z, u, lib=cs):</code></li>                
                    <ul>
                        <li>Argument <code>z:</code> Current state of the vehicle bicycle model.</li>
                        <li>Argument <code>u:</code> Current input to the vehicle bicycle model.</li>
                        <li>Argument <code>lib:</code> Module selection between <code>NumPy</code> and <code>CasADi</code> to use for next state somputation. Uses <code>CasADi</code> by default.</li>
                        <li>Initialises required simulation variables of the bicycle model.</li>
                    </ul>
                <li><code>dynamics_generic_ct(z, u, lib=cs):</code></li>
                <li><code>dynamics_cs(z, u):</code></li>
            </ol> -->
    </ol> 
</li>

<li> <code>class TrackGenerator:</code> 
    <ol type="a">
        <li>Dependencies:</li>
            <ul>
                <li><code>numpy:</code> For linear algebra and trigonometry.</li>
                <li><code>matplotlib.patches.Ellipse:</code> For simulation graphics.</li>
            </ul>
        <li>Implements (<em>Spoiler Alert!!!</em>):</li>
            <ul>
                <li>Track generation for both simulation and visualisation purposes.</li> 
                <li>For simulation, it generates elliptical tracks of required size and samples the track boundaries and centre in to desired number of samples.</li> 
                <li>For track visualisation, it generates <code>matplotlib</code> elliptical patches.</li> 
                <li>It is also used for generating elliptical obstacle boundaries (<em>safety circles</em>).</li>
            </ul>
    </ol> 
</li>

<li> <code>class ReferenceGenerator:</code> 
    <ol type="a">
        <li>Dependencies:</li>
            <ul>
                <li><code>numpy:</code> For linear algebra and trigonometry.</li>
                <li><code>TrackGenerator:</code> For regenerating <em>safety circles</em> for moving obstacles every instant.</li>
            </ul>
        <li>Implements (<em>Spoiler Alert!!!</em>):</li>
            <ul>
                <li>The aforementioned reference generator algorithm.</li> 
                <li>Ability to add obstacles to the track.</li>
                <li>Ability to move obstacles on the track.</li>
                <li>Ability to detect obstacles within the vehicle's virtual sensor range.</li>
                <li>Extends the aforementioned reference generator algorithm to avoid generating reference close to obstacles.</li>
            </ul>
    </ol> 
</li>

</ol>

<p>
<b>Note:</b> From here on, the Python3 implementation code snippets for the simulation side of the code is not presented in order to avoid clumsy presentation. Instead the reader is requested to visit this ***insert github link*** GitHub repository.
</p>

<p>
<b>Note:</b> The <code>TrackGenerator</code> and <code>ReferenceGenerator</code> class explanations contain obstacle avoidance related spoilers from upcoming sections. As the necessity of these features will become clear in the sections that follow, the reader is advised to just keep them in mind for the time being.
</p>

<p>

This algorithm is implemented in Python3 with a elliptical track of width $w = 1$ m. The major axis radius of the centre of the track is equal to $a = 5$ m and the corresponding minor axis radius is equal to $b = 4.5$ m. The track centre lane is sampled at a constant rate of $s_ p = 40$ samples per metre and the number of points to look ahead is set to $l = 120$ samples. The system is simulated with $N=50$, $S=1500$, $T=0.01$ s, and $z=[5, 0]^\mathsf{T}$ and the simulation results are shown in the GIF below.
</p>

<div>
<img src="/track_drive_higher_resolution_track.gif" alt="Simulation results: with track sampling = 40 samples/m and 120 samples look ahead." width="640" height="400">
</div>

<p>

In the GIF above, the black path represents the elliptical track and yellow curve represents the centre division lane of the elliptical path. It seems like the vehicle is successfully navigating around the track. A slowed down version of the simulation results are shown in the GIF below. 
</p>

<p>

For further clarity, a white dot representing the projection of the current position of the vehicle on to the centre lane ($p^\text{traj}_ {i_ p}$) and a red dot representing the generated reference position $p^d$ are added to the plot.</p>

<div>
<img src="/track_drive_higher_resolution_track_with_ref.gif" alt="Simulation results: A slowed version of the simulation results with track sampling = 40 samples/m and 120 samples look ahead." width="640" height="400">
</div>

<p>

As it can be seen from the simulation results above, the vehicle successfully follows (chases after) the reference without exiting the track (is it though?). To verify this, the actual trajectory of the vehicle is plotted with a blue trail in the GIF below.
</p>

<div>
<img src="/track_drive_higher_resolution_track_with_ref_trail.gif" alt="Simulation results: with vehicle trail in blue." width="640" height="400">
</div>

<p>

Though, it seems like the trail of the vehicle never exits the track, this is only because the choice of the track dimensions and the look ahead $l = 120$ samples coincidentally leave enough time and space for the vehicle to trace an elliptical path of comparable dimensions to the actual track. Had the dimensions of the track been a bit different or the look ahead been any larger, the vehicle could have exited the track. To prove this, the same simulation is repeated with $l = 200$ samples and the results are shown in the GIF below.
</p>

<div>
<img src="/track_drive_higher_resolution_track_distant_destination.gif" alt="Simulation results: track sampling = 40 samples/m and 200 samples look ahead" width="640" height="400">
</div>

<p>

A slower version with reference and vehicle projections is shown in the GIF below.
</p>

<div>
<img src="/track_drive_higher_resolution_track_distant_destination_with_ref.gif" alt="Simulation results: track sampling = 40 samples/m and 200 samples look ahead" width="640" height="400">
</div>

<p>

Now, it can be clearly seen that the NMPC controlled vehicle is only aiming to catch up to the reference, and it staying on the track was a coincidence. This is because, there is no constraint (<em>safety</em>) in the NMPC formulation that explicitly states that the vehicle must stay on the track. The same simulation with the vehicle trail is presented below.
</p>

<div>
<img src="/track_drive_higher_resolution_track_distant_destination_with_ref_trail.gif" alt="Simulation results: track sampling = 40 samples/m and 200 samples look ahead" width="640" height="400">
</div>

<p>

This is not true lane-keeping but rather the first step towards it. True lane-keeping is achieved when the system actively puts effort to not exit the track. This will be discussed in the next section, but for now this finding is important for two reasons:
1. Safety constraints must be explicit, meaning if it is not in the formulation, there is no guarantee that the NMPC controlled system will obey it.
2. Safety must take priority over reference tracking.
</p>

## ఠ Section 3: Lane-keeping + Obstacle Avoidance

<p>

In this section, the obstacle avoidance feature will be combined with the lane-keeping feature to enable our NMPC controlled vehicle to navigate around know static obstacles on a predetermined track.
</p>

<p>

In the previous section, it was observed that the NMPC controlled vehicle is successfully tracking the dynamic reference, but is prone to exiting the road. The fix for it will addressed in this section because it is closely related to the formulation of the obstacle avoidance constraints. As seen in Section 1, given the right obstacle avoidance constraint, the NMPC controlled vehicle avoids (entering) collision with the obstacle.
</p>

<p>

What if the blue obstacle in the simulation results of Section 1 is not an obstacle, but a region in which the vehicle is prohibited to entre. Turns out, the formulation remains the same and the simulation results will also remain unaltered. This is because in the NMPC controller formulation, there is no mathematical difference between an obstacle avoidance constraint and a state constraint where the vehicle is prohibited to entre a circular (elliptical in this case) region. 
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

<p><b>Note:</b> For simplicity, the road tracks demonstrated in this article are assumed to have their centres coincide with the global origin, their major axis parallel to the global x-axis, and their minor axis parallel to the global y-axis. Nonetheless, the generalised reformulation of obstacle constraints below, features translated and rotated elliptical constraints for the interested. While it is possible to extend this and make arbitrary paths by <em>switching</em> between different ellipses (something similar to <b>DOSA</b>, see Section 5), it is beyond the scope of this article.</p>

<p>
As an example, the track inner region exclusion constraints can be generalised as,
</p>

<p>
$$
\begin{aligned}
&\tilde{p}_{x, k} = (p_{x, k} - c_ x)\cos(\theta) + (p_{y, k} - c_ y)\sin(\theta), \\
&\tilde{p}_{y, k} = -(p_{x, k} - c_ x)\sin(\theta) + (p_{y, k} - c_ y)\cos(\theta), \\
&\left(\frac{\tilde{p}_{x, k}}{a_ i + \frac{w_c}{2}}\right)^2 + \left(\frac{\tilde{p}_{y, k}}{b_ i + \frac{w_c}{2}}\right)^2 \gt 1 \\
\implies &\left(\frac{\tilde{p}_{x, k}}{a_ i + \frac{w_c}{2}}\right)^2 + \left(\frac{\tilde{p}_{y, k}}{b_ i + \frac{w_c}{2}}\right)^2 - 1 \gt 0,\ \forall k \in \Z_{[1, N]}
\end{aligned}
$$
</p>

<p>
Where, $(p_{x, k}, p_{y, k})$ is the position of the vehicle's centre at $k^\text{th}$ step w.r.t. the global co-ordinate system, $(c_x, c_y)$ is the centre of the track also w.r.t. the global co-ordinate system, $\theta$ is the angle of the track's major axis made with the global x-axis measured in CCW direction, $(a_i, b_i)$ is the major and minor axis radii respectively, and $w_c$ is the width of the vehicle.
</p>



<p>
For the purpose of this article, the simplified version of the track with its centre at global origin and major axis parallel to the global x-axis will be used for simulation. However, the generalisation above will be reused later for obstacle avoidance with objects in motion. The NMPC formulation will now take the following form,
</p>

<p>
$$
\begin{aligned}
\mathbb{P}_ N(z, u^\star_ {\text{p}}) :\ &\underset{\bm{u}_ N}{\text{minimise}}\ \|p_ N - p^d\|_ {Q_ {1}}^2 + \sum_ {k=0}^{N-1} \left( \|u_ k - u_ {k-1}\|_ {Q_ 2}^2 + R \cdot \tilde{d}_k \cdot \tilde{b}_k \right) \\
\text{s.t.}\ & z = z^\star_ {\text{p}}, \\
\ &u_ {-1} = u^\star_ {\text{p}}, \\
\ &z_ {k+1} = \mathbf{g}(z_ k, u_ k),\ k \in \N_ {[0, N-1]}, \\
\ &u_ k \in \mathbf{U},\ k \in \N_ {[0, N-1]}, \\
\ &v_ {x, k} \in \bm{\Gamma},\ k \in \N_ {[0, N-1]}, \\
\ &1 - \left(\frac{p_{x, k}}{a_ i + \frac{w_c}{2}}\right)^2 - \left(\frac{p_{y, k}}{b_ i + \frac{w_c}{2}}\right)^2 \lt 0,\ \forall k \in \Z_{[1, N]}, \text{ and} \\
\ &\left(\frac{p_{x, k}}{a_ o + \frac{w_c}{2}}\right)^2 + \left(\frac{p_{y, k}}{b_ o + \frac{w_c}{2}}\right)^2 - 1 \lt 0,\ \forall k \in \Z_{[1, N]}.
\end{aligned}
$$
</p>

<p>
The above changes in the NMPC formulation can be realised in existing Python3 code as follows,
</p>

```Python
nz = 6
nu = 3
N = 50

zmin = [-100, -100, -np.inf, 0, 0, -np.inf]
zmax = [100, 100, np.inf, 5, 5, np.inf]
umin_seq = [0, -np.pi/3, 0] * N
umax_seq = [1, np.pi/3, 1] * N

Q1 = np.diagflat([10000, 10000, 0, 0, 0, 0])
Q2 = np.diagflat([10, 10, 10])
R = 1000000

u_seq = cs.SX.sym("u_seq", N * nu, 1)
problem_params = cs.SX.sym("problem_params", 2*nz + 2*nu, 1)
z_0 = problem_params[ : nz]
z_ref = problem_params[nz : 2 * nz]
u_ref = problem_params[2 * nz : 2 * nz + nu]  # currently un-used
u_prev = problem_params[2 * nz + nu : 2 * nz + 2 * nu]  # u_{-1} value of inputs before t=0
obstacles_functions = []
obstacle_params = [0]*num_simultaneous_obstacles_to_track
def obstacle_function(x, y, obstacle_param):
    X_ = (x - obstacle_param[0])
    Y_ = (y - obstacle_param[1])
    a = obstacle_param[2]
    b = obstacle_param[3]
    theta = 2*cs.pi - obstacle_param[4]
    is_present = obstacle_param[5]

    X = (X_ * cs.cos(theta) - Y_ * cs.sin(theta)) / a
    Y = (X_ * cs.sin(theta) + Y_ * cs.cos(theta)) / b

    return cs.fmax(0, is_present - X ** 2 - Y ** 2)

for i in range(num_simultaneous_obstacles_to_track):
    obstacle_params[i] = problem_params[2*(nz + nu) + num_data_params_per_obstacle*i:2*(nz + nu) + num_data_params_per_obstacle*(i+1)]
    obstacles_functions.append(partial(obstacle_function, obstacle_param=obstacle_params[i]))

z_t = z_0

def stage_cost(u_current, u_prev):
    return (u_current.T - u_prev.T) @ R @ (u_current - u_prev) + R * u_current[0] * u_current[2]

def terminal_cost(z):
    return (z.T - z_ref.T) @ Q @ (z - z_ref)

total_cost = 0
penality_constraint_position = 0
penality_constraint_obstacles = 0
penality_constraint_tracks = 0
penality_constraint_vx = 0
penality_constraint_vy = 0

for t in range(N):
    u_current = u_seq[t * nu : (t + 1) * nu]
    total_cost += stage_cost(u_current, u_prev)
    z_t = bm.dynamics_cs(z_t, u_current)
    u_prev = u_current
    penality_constraint_position += cs.fmax(0, (2 + bm.car_width / 2)**2 - z_t[0]**2 - z_t[1]**2)
    penality_constraint_vx += cs.fmax(0, cs.norm_1(z_t[3]) - zmax[3])
    penality_constraint_vx += cs.norm_1(cs.fmin(zmin[3], z_t[3]))
    for obstacle_function in obstacles_functions:
        penality_constraint_obstacles += obstacle_function(z_t[0], z_t[1])
    penality_constraint_tracks += cs.fmax(0, 1 - (z_t[0]/(5 + .25))**2 - (z_t[1]/(4+.25))**2)
    penality_constraint_tracks += cs.fmax(0, (z_t[0]/(6-.25))**2 + (z_t[1]/(5-.25))**2 - 1)


total_cost += terminal_cost(z_t)

U = og.constraints.Rectangle(umin_seq, umax_seq)

problem = og.builder.Problem(u_seq, problem_params, total_cost)
problem = problem.with_constraints(U)
problem = problem.with_penalty_constraints(penality_constraint_position 
                                           + penality_constraint_vx + penality_constraint_vy 
                                           + penality_constraint_obstacles + penality_constraint_tracks)

build_config = og.config.BuildConfiguration() \
    .with_build_directory("optimizer") \
    .with_tcp_interface_config()

meta = og.config.OptimizerMeta().with_optimizer_name("bicycle_model")

solver_config = og.config.SolverConfiguration()\
    .with_tolerance(1e-4)\
    .with_penalty_weight_update_factor(3)

builder = []
builder = og.builder.OpEnOptimizerBuilder(problem, meta,
                                          build_config, solver_config).with_verbosity_level(0)

builder.build()
```

<div>
<img src="/track_drive_multiple_obs_no_disappear.gif" alt="Simulation results: track sampling = 40 samples/m and 200 samples look ahead" width="640" height="400">
</div>

## Section 4: Virtual sensor
<div>
<img src="/track_drive_multiple_obs_disappear.gif" alt="Simulation results: track sampling = 40 samples/m and 200 samples look ahead" width="640" height="400">
</div>

## Section 5: DOSA (very healthy if prepared right)

<p>In particular we are interested in a formulation the guarantees Dynamic Obstacle Switching and Avoidance (<b>DOSA</b>) with objects in motion. To explain this, let us consider the following scenario,
</p>

<p>
Imagine yourself driving a car on a typical high-way/ motor-way. At a basic level, while driving, you intend to keep the vehicle on the correct side of the road, avoid hitting obstacles, and reach your destination. Now, to avoid hitting obstacles, depending on your velocity, you need only keep check of vehicles and objects within a certain vicinity of your vehicle, say within a $20$ m radius. This means that, your decision now is less likely to be influenced by another vehicle that is more than $20$ m away from your vehicle and this likelihood quickly reaches zero the further the other vehicle is. Moreover, irrespective of the total number of vehicles on the road, you would only want to keep track of a limited number of vehicles within this vicinity. 
</p>

<p>
Given that there are objects (stationary or moving) and vehicles (stationary or moving) with possibly different velocities than your's, the relative position of these vehicles and objects with you change over time. This also implies that objects and vehicles <em>come and go</em> into your vicinity. Say, as vehicle-a goes out of your vicinity some vehicle-b comes by, there is no point in tracking vehicle-a anymore and there is a need for tracking the newly arrived vehicle-b. This raises the necessity of dynamically dropping old objects of interest, the ones that are gone by, and track new objects of interest, the ones that have newly arrived into your vicinity. This <em>dynamic switching</em> between objects (<em>obstacles</em>) and actively avoiding them is <b>DOSA</b>. 
</p>

<p>
In general, an autonomous car would be equipped with sensors to help with object tracking. These sensors work <em>similar</em> to how a person would track their <em>vicinity</em> (limited tracking space) for a <em>few objects</em> (limited obstacle count). Mathematically, it is straightforward to integrate the sensory data and include a few, say $5$, more obstacle avoidance constraints into the existing formulation to keep track of the said objects. In this regard, It would make sense to ask the following questions, 
</p>

1. what happens when the vehicle and the objects are moving at different/ same velocities?
2. What if the formulation was set to track $5$ obstacles but only $3$ were present? Should such formulation still account for vehicles that are no longer in the vicinity of the vehicle? 
3. Or even worse what if there are $6$ or $7$ objects in the vicinity?
4. What about the numerical stability in all these cases?

<p>
Before answering these questions, let us introduce the special reformulation as, 
</p>

<p>
$$
\begin{aligned}
&\tilde{p}_{x, k, j} = (p_{x, k} - c_ {x, j})\cos(\theta_j) + (p_{y, k} - c_ {y, j})\sin(\theta_j),\\
&\tilde{p}_{y, k, j} = -(p_{x, k} - c_ {x, j})\sin(\theta_j) + (p_{y, k} - c_ {y, j})\cos(\theta_j),\\
&\left(\frac{\tilde{p}_{x, k, j}}{a_ j + \frac{w_c}{2}}\right)^2 + \left(\frac{\tilde{p}_{y, k, j}}{b_ j + \frac{w_c}{2}}\right)^2 \gt f(j) \\
\implies &\left(\frac{\tilde{p}_{x, k, j}}{a_ j + \frac{w_c}{2}}\right)^2 + \left(\frac{\tilde{p}_{y, k, j}}{b_ j + \frac{w_c}{2}}\right)^2 - f(j) \gt 0,\ \forall k \in \Z_{[1, N]}, \text{ and } \forall j \in \Z_{[1, n_o]}.
\end{aligned}
$$
</p>


<p>
Where, $(p_{x, k}, p_{y, k})$ is the position of the vehicle's centre at $k^\text{th}$ step w.r.t the global co-ordinate system, $(c_{x, j}, c_{y, j})$ is the centre of the $j^\text{th}$ obstacle <em>constraint</em> also w.r.t. the global co-ordinate system, $\theta_j$ is the angle of the $j^\text{th}$ obstacle <em>constraint's</em> major axis made with the global x-axis measured in CCW direction, $(a_j > 0$ and $b_j > 0)$ are the major and minor axis radii respectively of the $j^\text{th}$ obstacle <em>constraint</em>, $w_c$ is the width of the vehicle, $n_o$ is the total number of obstacle <em>constraints</em>, and $f(j)$ is the special function mentioned in the beginning of the article.
</p>

<p>
<b>Note:</b> The reader should take a note of the emphasis on the word constraint in obstacle constraint above. The formulation is not an expression in reference to any particular obstacle, but is a generic constraint, which will be a part of the optimisation problem formulation. Meaning, the obstacle constraint parameters mentioned above are not fixed constants related to an obstacle (say vehicle-a), but will be changing dynamically as the algorithm does <b>DOSA</b>. For example, when vehicle-a goes away as vehicle-b comes by, the parameters of the $j^\text{th}$ obstacle constraint will be switching from constants that describe the centre, orientation and radii of vehicle-a to the corresponding constants that describe vehicle-b.
</p>

<p>
The special function is defined as follows,
</p>

<p>
$$
f(j) = \left\{ 
            \begin{array} {ll}
            1 &\text{if object in vicinity}, \\ 
            0 &\text{otherwise}. 
            \end{array} \right.
$$
</p>

<p>
For the sake of simplicity, the idea of <em>vicinity</em> will be restricted to the range of a virtual sensor. The virtual sensor scans a few meters ahead of the vehicle and returns the aforementioned obstacle constraint parameters for $n_o$ number of constraints. For example, say that there are $3$ obstacles within the sensor range and $o_n = 4$, it would return the corresponding $(c_{x, j}, c_{y, j})$, $\theta_j$, $(a_j > 0$, $b_j > 0)$, and $f(j)=1$ for $j \in \{1, 2, 3\}$, and would return $(c_{x, j} = 0, c_{y, j} = 0)$, $\theta_j=0$, $(a_j = 1$ and $b_j = 1)$, and $f(j)=0$ for $j = 4$. 
</p>

<p>
The first three constraints i.e., for $j \in \{1, 2, 3\}$, would work as any other obstacle avoidance constraint would, but the last constraint i.e., for $j=4$, setting $f(j)=0$ effectively removes the constraint and $(a_j = 1$ and $b_j = 1)$ ensures numerical stability by avoiding any division by zero errors (if they were to occur). This allows for dynamical switching between obstacles as these constraint parameters are updated for every simulation step. From here on, such a virtual sensor is referred to as obstacle sensor.
</p>

<p>
<b>Caution:</b> Setting $f(j)$ to $0$ will still exclude the vehicle from entering the point $(c_{x, j}, c_{y, j})$. So, in addition to making $f(j)=0$, $(c_{x, j}, c_{y, j})$ should be changed to a point outside the track. Since the regions outside the track are excluded to begin with, fixing $(c_{x, j}, c_{y, j})$ outside the track makes little to no difference. For convenience it will be set to the origin, as it lies outside the track.
</p>

<p>
<b>Note:</b>It should be noted that the above formulated constraints does not contain any information regarding the obstacles' velocities and accelerations. As the NMPC computes $N$ solutions, it <em>sees</em> these obstacles as stationary. It is only in the next iteration that the relative position of these obstacles are updated. So, in every iteration, only the first computed solution (out of $N$ control actions) is optimal because the obstacles' positions might evolve over the next $N-1$ computations. Since, the NMPC algorithm only utilises the first computed solution, the above method can still be used. Also, addressing this issue is beyond the scope of this article and is left to the curious as an exercise. 
</p>

<p>
Addressing the questions posed before, although the above formulation guarantees <em>safety</em> with regards to collision prevention through obstacle avoidance, it is difficult to extend this <em>safety</em> for all possible relative velocities between the vehicle and the obstacles. In this regard, questions $1$ and $4$ require a more complex formulation and for the sake keeping things simple, they will remain unanswered. But, some form of numerical stability is still maintained because of the introduction of the special function. As for the questions $2$ and $3$, by the virtue of DOSA and the special function, the formulation gets some flexibility in terms of tackling an undisclosed number of obstacles, provided that the path is not completely blocked.
</p>

<p>With these changes we rewrite parts of our python code as follows, ***Insert Python3 code***</p>

<p>Below are the simulation results ***provide simulation configuration***</p>

## References
<p>

[1] Cataffo, Vittorio & Silano, Giuseppe & Iannelli, Luigi & Puig, Vicenç & Glielmo, Luigi. (2022). A Nonlinear Model Predictive Control Strategy for Autonomous Racing of Scale Vehicles. 10.1109/SMC53654.2022.9945279. 
</p>
