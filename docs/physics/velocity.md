{[date]Last updated June 11, 2026}
# Velocity
{[author]{pp::petr-zelinka}Petr Zelinka}
{[read_time]3 min read}
{[copy_raw_file]Copy Markdown}
{[view_as_md]View as Markdown}

In classical mechanics, **velocity** is a vector physical quantity that quantifies the rate of change of an object's position with respect to a frame of reference over time. It is conventionally denoted by the symbol $\vec{v}$. Within the International System of Units (SI), its base derived unit is the meter per second ($\text{m}\cdot\text{s}^{-1}$ or $\text{m/s}$).

---

## Modalities of Velocity

Kinematic analysis distinguishes between two primary classifications of velocity depending on the temporal scope of the measurement:

* **Average Velocity ($v_{\text{avg}}$):** A scalar or vector quantity defined as the total displacement or distance traveled ($\Delta s$) divided by the total elapsed time interval ($\Delta t$) required to traverse that distance.
* **Instantaneous Velocity ($\vec{v}$):** The velocity of an object at a specific, infinitely small increment of time. Mathematically, it is defined as the limit of the average velocity as the time interval approaches zero, representing the first derivative of position with respect to time:

$$\vec{v} = \lim_{\Delta t \to 0} \frac{\Delta \vec{s}}{\Delta t} = \frac{\mathrm{d}\vec{s}}{\mathrm{d}t}$$



---

## Fundamental Kinematic Formulations

For uniform linear motion (where velocity remains constant, meaning $v = v_{\text{avg}}$), the algebraic relationships between velocity ($v$), displacement ($s$), and time ($t$) are defined by the following expressions:

### Velocity Calculation
$$\boxed{v = \frac{s}{t}}$$

### Displacement Calculation
$$\boxed{s = v \cdot t}$$

### Time Calculation
$$\boxed{t = \frac{s}{v}}$$

---

## Unit Conversion Metrics

While $\text{m/s}$ is the mandatory SI standard for scientific calculations, macroscopic transport metrics frequently utilize kilometers per hour ($\text{km/h}$ or $\text{km}\cdot\text{h}^{-1}$). The exact scaling factor between these units is derived from the relationships $1 \text{ km} = 1000 \text{ m}$ and $1 \text{ h} = 3600 \text{ s}$, yielding a constant coefficient of $3.6$.

### Conversion from Meters per Second to Kilometers per Hour
$$\boxed{v_{\text{m/s}} \cdot 3.6 = v_{\text{km/h}}}$$

### Conversion from Kilometers per Hour to Meters per Second
$$\boxed{\frac{v_{\text{km/h}}}{3.6} = v_{\text{m/s}}}$$

---

## Practical Analytical Scenarios

### Scenario A: Calculation of Average Velocity
A transport vehicle transits between two urban centers over an elapsed time ($t$) of $1.5 \text{ hours}$. The recorded odometric distance ($s$) between the centers is $80 \text{ km}$. Determine the average velocity ($v$).

$$v = \frac{s}{t} = \frac{80 \text{ km}}{1.5 \text{ h}} \approx 53.33 \text{ km/h}$$

$$\boxed{v \approx 53.33 \text{ km/h}}$$

### Scenario B: Calculation of Temporal Duration
A sphere migrates across a horizontal plane at a constant velocity ($v$) of $0.5 \text{ m/s}$. The total linear span ($s$) of the plane is $2 \text{ m}$. Assuming dissipative forces (friction and drag) are negligible, calculate the total time ($t$) required to clear the plane.

$$t = \frac{s}{v} = \frac{2 \text{ m}}{0.5 \text{ m/s}} = 4 \text{ s}$$

$$\boxed{t = 4 \text{ s}}$$

### Scenario C: Calculation of Linear Displacement
A cyclist maintains a constant average velocity ($v$) of $10 \text{ km/h}$ over a continuous operational duration ($t$) of $2 \text{ hours}$. Calculate the total spatial displacement ($s$).

$$s = v \cdot t = 10 \text{ km/h} \cdot 2 \text{ h} = 20 \text{ km}$$

$$\boxed{s = 20 \text{ km}}$$

<br><br>