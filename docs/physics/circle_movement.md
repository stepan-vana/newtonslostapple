{[date]Last updated June 11, 2026}
# Circular Movement
{[author]{pp::petr-zelinka}Petr Zelinka}
{[read_time]6 min read}
{[copy_raw_file]Copy Markdown}
{[view_as_md]View as Markdown}

## Kinematics of Circular Trajectories

When an object transitions from a linear trajectory into a circular path, it must experience a continuous acceleration directed orthogonally to its velocity vector, pointing directly toward the geometric center of the circular trajectory. This acceleration must continuously shift its orientation to remain directed toward the center at **every** discrete point along the path of motion.

---

## Temporal Characteristics: Period and Frequency

To quantify periodic rotational motion, two interdependent scalar quantities are defined: **Period** and **Frequency**.

* **Period ($T$):** The time required for a body to complete one full revolution along its circular orbit. The SI base unit is the second ($\text{s}$).
* **Frequency ($f$):** The number of complete revolutions executed by the body per unit of time (specifically, per second). The SI derived unit is the hertz ($\text{Hz}$), where $1 \, \text{Hz} = 1 \, \text{s}^{-1}$.

### Reciprocal Relationship

The mathematical conversion between frequency and period is expressed via the following reciprocal equations:

$$\boxed{f = \frac{1}{T}} \quad \text{and} \quad \boxed{T = \frac{1}{f}}$$

---

## Angular Velocity

**Angular velocity** ($\omega$) measures the rate of change of the angular displacement (the angle swept by the position vector) over time, rather than the linear distance traversed. 

The SI derived unit for angular velocity is the radian per second ($\text{rad}\cdot\text{s}^{-1}$ or $\text{rad/s}$), where a complete $360^\circ$ rotation corresponds precisely to $2\pi \, \text{rad}$.

### Mathematical Calculation

Angular velocity can be calculated derived from either the frequency or the period of rotation:

$$\boxed{\omega = 2\pi f = \frac{2\pi}{T}}$$

---

## Linear (Tangential) Velocity

The linear velocity ($v$) of a body moving in a circle represents its instantaneous speed tangent to the circular path. It is directly proportional to both the angular velocity and the radius ($r$) of the trajectory:

$$\boxed{v = \omega r = 2\pi f r = \frac{2\pi r}{T}}$$

---

## Centripetal Acceleration

**Centripetal acceleration** ($\vec{a}_c$) is the vector quantity responsible exclusively for altering the directional component of the velocity vector, thereby curving the trajectory into a circular arc without modifying the object's tangential speed.

### Formulation

Centripetal acceleration is quantified as a function of tangential velocity, angular velocity, and the trajectory radius:

$$\boxed{a_c = \frac{v^2}{r} = \omega^2 r}$$

---

## Centripetal Force and Dynamics

According to **Newton's Second Law of Motion**, any net acceleration acting upon a mass ($m$) implies the presence of a net external force acting in the same direction. The force required to continuously deflect the body from its rectilinear path into a circle is defined as the **centripetal force** ($F_c$).



### Derivation from Fundamental Laws

By substituting the expressions for centripetal acceleration into the classical equation $F = ma$, we obtain the dynamic equations for circular motion:

$$F_c = m a_c$$

$$\boxed{F_c = m \frac{v^2}{r} = m \omega^2 r}$$

<br><br>
