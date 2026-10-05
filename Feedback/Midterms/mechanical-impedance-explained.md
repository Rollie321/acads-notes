# Understanding Mechanical Impedance: Spring, Damper, and Mass

This explains, from scratch, where every formula in the table comes from — the physical law, the calculus that connects force/velocity/displacement, and finally the "impedance" idea borrowed from electrical circuits.

---

## 1. The big picture: three ways to describe the same relationship

For each mechanical component, the table gives you **three equivalent descriptions** of how force relates to motion:

1. **Force–velocity**: force as a function of velocity $v(t)$
2. **Force–displacement**: force as a function of position $x(t)$
3. **Impedance**: the ratio $Z_M(s) = F(s)/X(s)$ in the Laplace domain

These aren't three different facts — they're the *same physical law* written in different variables. Since velocity is just the rate of change of displacement, and displacement is the accumulation of velocity over time, you can always convert between them using calculus:

$$
v(t) = \frac{dx(t)}{dt} \qquad \text{(velocity is the derivative of position)}
$$

$$
x(t) = \int_0^t v(\tau)\, d\tau \qquad \text{(position is the integral of velocity)}
$$

That single relationship is the key that unlocks every row of the table.

---

## 2. The Spring

### Physical law: Hooke's Law
A spring resists being stretched or compressed. The restoring force is proportional to how far it's displaced from its rest position:

$$
f(t) = K x(t)
$$

- $K$ = spring constant (N/m) — stiffness. Bigger $K$ = stiffer spring.
- $x(t)$ = displacement (m)

This is the **force–displacement** column directly — it's the fundamental definition of a spring.

### Getting force–velocity
Since $x(t) = \int_0^t v(\tau)\,d\tau$ (displacement is the running total of velocity over time), just substitute:

$$
f(t) = K x(t) = K \int_0^t v(\tau)\, d\tau
$$

That's exactly the force–velocity formula in the table. Intuitively: a spring "remembers" *all* the motion that has happened up to now (it cares about total accumulated displacement), so its force depends on the **integral** of velocity.

### Getting impedance
Impedance uses the Laplace transform, where — critically — **integration in time becomes division by $s$**. So transforming $f(t) = Kx(t)$ into the Laplace domain gives:

$$
F(s) = K X(s)
$$

And impedance is defined as $Z_M(s) = F(s)/X(s)$, so:

$$
Z_M(s) = K
$$

A spring's impedance is just the constant $K$ — it doesn't depend on frequency at all.

---

## 3. The Viscous Damper (Dashpot)

### Physical law
A damper (think of a shock absorber, or a piston moving through thick oil) resists **motion itself**, not position. The faster you try to move it, the more it pushes back:

$$
f(t) = f_v\, v(t)
$$

- $f_v$ = damping coefficient (N·s/m)
- $v(t)$ = velocity (m/s)

This is the **force–velocity** column, and it's the fundamental definition of a damper — unlike the spring, the damper's defining law is stated in terms of velocity, not displacement.

### Getting force–displacement
Now go the other direction. Since $v(t) = \dfrac{dx(t)}{dt}$ (velocity is the derivative of position), substitute:

$$
f(t) = f_v\, v(t) = f_v\, \frac{dx(t)}{dt}
$$

That's the table's force–displacement formula. Intuitively: a damper only cares about *how fast* position is changing right now, i.e., the **derivative** of displacement.

### Getting impedance
In the Laplace domain, **differentiation in time becomes multiplication by $s$**. Transforming $f(t) = f_v \dfrac{dx(t)}{dt}$ gives:

$$
F(s) = f_v\, s\, X(s)
$$

So:

$$
Z_M(s) = \frac{F(s)}{X(s)} = f_v s
$$

A damper's impedance grows linearly with $s$ (i.e., with frequency) — it resists fast motion more than slow motion, which matches the physical intuition.

---

## 4. The Mass

### Physical law: Newton's Second Law
A mass resists **acceleration**. Force equals mass times acceleration:

$$
f(t) = M\, \frac{dv(t)}{dt}
$$

- $M$ = mass (kg)
- $\dfrac{dv(t)}{dt}$ = acceleration (rate of change of velocity)

This is the **force–velocity** column.

### Getting force–displacement
Acceleration is the rate of change of velocity, and velocity is itself the rate of change of displacement — so acceleration is the **second derivative** of displacement:

$$
\frac{dv(t)}{dt} = \frac{d}{dt}\left[\frac{dx(t)}{dt}\right] = \frac{d^2x(t)}{dt^2}
$$

Substituting:

$$
f(t) = M\, \frac{d^2x(t)}{dt^2}
$$

That's the table's force–displacement formula.

### Getting impedance
Each time-derivative becomes one factor of $s$ in the Laplace domain. Since force–displacement involves the *second* derivative, you pick up $s$ **twice**:

$$
F(s) = M s^2 X(s)
$$

$$
Z_M(s) = \frac{F(s)}{X(s)} = M s^2
$$

A mass's impedance grows with the **square** of $s$ — it resists rapid changes in motion (acceleration) far more than slow ones.

---

## 5. Why does this pattern repeat? The "calculus ladder"

Notice the clean pattern across all three rows:

| Component | Relates force to... | # of derivatives from $x(t)$ | Power of $s$ |
|---|---|---|---|
| Spring | position ($x$) | 0 (no derivative — or one integral) | $s^0 \to$ constant $K$ |
| Damper | velocity ($v = dx/dt$) | 1 derivative | $s^1$ |
| Mass | acceleration ($d^2x/dt^2$) | 2 derivatives | $s^2$ |

This is the heart of why the impedance column looks the way it does:

- Displacement $x(t)$ is the "base" quantity.
- Velocity is its 1st derivative; acceleration is its 2nd derivative.
- Every time you take a derivative in the time domain, the Laplace transform multiplies by $s$.
- Every time you integrate, it divides by $s$.

So the impedance $Z_M(s) = F(s)/X(s)$ directly reflects **how many times you had to differentiate $x(t)$ to get the force law**: 0 times for the spring, 1 time for the damper, 2 times for the mass.

---

## 6. Why bother with "impedance" at all?

This comes from an analogy with **electrical circuits**, where impedance $Z(s) = V(s)/I(s)$ (voltage/current) describes how a resistor, capacitor, or inductor opposes current flow. Mechanical impedance does the same job for mechanical systems: it packages a component's entire dynamic behavior (spring-like, damper-like, or mass-like) into a single algebraic expression in $s$, instead of a differential/integral equation in time.

The huge practical payoff: once every component is expressed as $Z_M(s) = F(s)/X(s)$, you can combine springs, dampers, and masses in a mechanical system using the **same algebraic rules** used for combining resistors, capacitors, and inductors in circuits (impedances in series add, etc.). This turns messy differential-equation problems into algebra problems — which is the whole reason this table exists in textbooks on system dynamics and controls.

---

## 7. Quick reference: the full derivation chain

$$
\begin{array}{ccc}
\textbf{Spring} & \textbf{Damper} & \textbf{Mass} \\
f = Kx & f = f_v \dfrac{dx}{dt} & f = M\dfrac{d^2x}{dt^2} \\[2mm]
\Downarrow & \Downarrow & \Downarrow \\[1mm]
Z = K & Z = f_v s & Z = M s^2
\end{array}
$$

Each component's impedance is found by:
1. Writing down its fundamental physical law (Hooke's Law for springs, Newton's Second Law for masses, the defining velocity-proportional law for dampers).
2. Converting between velocity and displacement forms using $v = dx/dt$ (differentiate) or $x = \int v\, dt$ (integrate).
3. Laplace-transforming: replace $d/dt$ with multiplication by $s$, and $\int dt$ with division by $s$.
4. Dividing: $Z_M(s) = F(s)/X(s)$.
