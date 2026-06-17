{[date]Last updated June 11, 2026}
# Acceleration
{[author]{pp::petr-zelinka}Petr Zelinka}
{[read_time]4 min read}
{[copy_raw_file]Copy Markdown}
{[view_as_md]View as Markdown}

## Fundamental Definition of Acceleration

In classical mechanics, **acceleration** is defined as a vector physical quantity that quantifies the rate of change of the velocity vector with respect to time. It is conventionally denoted by the symbol $\vec{a}$. Within the International System of Units (SI), its derived unit is the meter per second squared ($\text{m}\cdot\text{s}^{-2}$ or $\text{m/s}^2$).

> ### Critical Concept Note
> Because velocity is a vector quantity possessing both magnitude (speed) and direction, acceleration occurs when either the magnitude **or** the direction of the velocity vector changes. Consequently, a body moving in a circular path at a constant speed is actively accelerating due to the continuous change in its directional vector (centripetal acceleration).

### Mathematical Representation

The average acceleration $\vec{a}$ over a given time interval $t$ is expressed as the first derivative of velocity with respect to time:

$$\boxed{\vec{a} = \frac{\Delta \vec{v}}{t}}$$

---

## Uniformly Accelerated Linear Motion

When a body undergoes motion with a constant acceleration ($a = \text{const.}$), the standard linear equation for uniform motion ($s = vt$) is mathematically invalid. To determine the spatial displacement ($s$), we must account for the continuously changing velocity.

### Displacement Equation

The total displacement of a particle experiencing uniform acceleration is governed by the following kinematic equation:

$$\boxed{s = v_0 t + \frac{1}{2} a t^2}$$

**Variable Definitions:**
* $v_0$ — Initial velocity of the body at time $t = 0$.
* $t$ — Elapsed time interval of the acceleration phase.
* $a$ — Constant acceleration rate.

### Calculus-Based Derivation

This kinematic relation is systematically derived by integrating the time-dependent velocity function $v(t) = v_0 + at$ over the time interval $[0, t]$, representing the definite area under the velocity-time curve ($v\text{-}t$ graph):

$$s = \int_{0}^{t} v(t) \, \mathrm{d}t = \int_{0}^{t} (v_0 + at) \, \mathrm{d}t = \boxed{v_0 t + \frac{1}{2} a t^2}$$

### Alternative Formulations

By substituting the definition of uniform acceleration into the primary displacement equation, we can derive an expression independent of the final acceleration magnitude:

$$s = v_0 t + \frac{1}{2}\left(\frac{v - v_0}{t}\right)t^2 = \boxed{\frac{v_0 + v}{2} \cdot t}$$

This represents the product of the average velocity and the total elapsed time.

---

## Kinematics of Free Fall

**Free fall** represents a specific case of uniformly accelerated linear motion occurring within a gravitational field, operating under the assumption of zero aerodynamic drag (vacuum conditions). 

The acceleration is dictated by the local gravitational acceleration constant, denoted as $g$. For calculations on Earth's surface, this value is standardized as:

$$g \approx 9.81 \, \text{m/s}^2 \quad \left(\text{approximated as } 10 \, \text{m/s}^2 \text{ for simplified analyses}\right)$$

### Practical Analytical Application

#### Scenario:
An object (e.g., an apple) falls from a stationary position in a tree from a baseline height ($h$) of $3 \text{ m}$ under the influence of Earth's gravity. Calculate the total duration of the descent ($t$).

#### Mathematical Solution:
Given that the initial velocity $v_0 = 0$, we substitute height $h$ for displacement $s$, and gravitational acceleration $g$ for $a$:

$$h = \frac{1}{2}gt^2$$

Isolating the temporal variable $t$ via algebraic rearrangement yields:

$$2h = gt^2 \implies t^2 = \frac{2h}{g}$$

Applying the square root to both sides provides the definitive time equation:

$$t = \sqrt{\frac{2h}{g}}$$

Substituting the empirical parameters ($h = 3 \text{ m}$, $g = 10 \text{ m/s}^2$):

$$t = \sqrt{\frac{2 \cdot 3}{10}} = \sqrt{0.6} \approx 0.775 \, \text{s}$$

$$\boxed{t \approx 0.775 \, \text{s}}$$

The total duration of the descent is precisely **0.775 seconds**.

<br><br>