{[date]Last updated June 11, 2026}
# Friction
{[author]{pp::petr-zelinka}Petr Zelinka}
{[read_time]3 min read}
{[copy_raw_file]Copy Markdown}
{[view_as_md]View as Markdown}

**Friction** is a dissipative contact force that opposes the relative lateral motion, or the tendency toward such motion, of two surfaces in contact. It converts mechanical kinetic energy into thermal energy. In classical mechanics, friction is broadly categorized based on the nature of the contact interface and the type of motion involved.

---

## Dry Friction (Sliding Friction)

Dry friction occurs when two solid surfaces in contact move, or attempt to move, relative to one another without the presence of a fluid lubricant. A classic example is a book sliding across a table; the frictional force acts in the direction opposite to the velocity vector, eventually bringing the object to a complete stop.



### Mathematical Definition

The maximum magnitude of dry friction is directly proportional to the normal force acting perpendicular to the contact interface:

$$\boxed{F_f = \mu \cdot F_N}$$

**Variable Definitions:**
* $F_f$ — The force of friction ($\text{N}$).
* $F_N$ — The normal force ($\text{N}$). On a flat, horizontal surface with no vertical external forces applied, the normal force equals the magnitude of the object's weight, derived as $F_N = mg$ (where $m$ is mass and $g$ is gravitational acceleration).
* $\mu$ — The **coefficient of friction** (often denoted as $f$ or $\mu$). It is a dimensionless scalar quantity that depends entirely on the materials and roughness of the interacting surfaces. For most common material pairings, its value is bounded between zero and one:

$$\mu \in (0, 1)$$

---

## Rolling Resistance

Rolling resistance (sometimes referred to as rolling friction) occurs when a spherical or cylindrical object—such as a ball, wheel, or tire—rolls over a surface. It is primarily caused by the non-elastic deformation of the rolling object, the supporting surface, or both, at the point of contact. Because less microscopic shearing occurs compared to sliding, rolling resistance is typically orders of magnitude smaller than dry sliding friction.



### Mathematical Definition

The force of rolling resistance is inversely proportional to the radius of the rolling object and directly proportional to the normal force:

$$\boxed{F_r = \frac{\xi}{r} \cdot F_N}$$

**Variable Definitions:**
* $F_r$ — The force of rolling resistance ($\text{N}$).
* $F_N$ — The normal force or weight load acting perpendicular to the surface ($F_N = mg$).
* $\xi$ — The **rolling resistance coefficient** (often denoted by the Greek letter xi, $\xi$, or $c_r$). It possesses the SI dimension of length and is typically expressed in meters ($\text{m}$) or millimeters ($\text{mm}$).
* $r$ — The radius of the rolling geometric body ($\text{m}$).

<br><br>