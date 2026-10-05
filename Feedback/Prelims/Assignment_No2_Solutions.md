# Assignment No. 2 — Feedback and Control System
## Solutions

---

## Problem 1: Laplace Transforms of Time Functions

### (a) $e^{-at}\cos(\omega t)\,u(t)$

**Starting point — the standard cosine transform:**

$$
\mathcal{L}\{\cos(\omega t)\,u(t)\} = \frac{s}{s^2+\omega^2}
$$

**Apply the frequency-shift (damping) property:**

$$
\mathcal{L}\{e^{-at}f(t)\} = F(s+a)
$$

Substituting $F(s) = \dfrac{s}{s^2+\omega^2}$ and replacing $s \to (s+a)$:

$$
\boxed{\mathcal{L}\{e^{-at}\cos(\omega t)\,u(t)\} = \frac{s+a}{(s+a)^2+\omega^2}}
$$

**Verification by direct integration:**

$$
X(s) = \int_0^\infty e^{-st}e^{-at}\cos(\omega t)\,dt = \int_0^\infty e^{-(s+a)t}\cos(\omega t)\,dt
$$

Using $\displaystyle\int_0^\infty e^{-pt}\cos(\omega t)\,dt = \frac{p}{p^2+\omega^2}$ with $p = s+a$ confirms the result above.

---

### (b) $t^3\,u(t)$

**Using the general power-of-t formula:**

$$
\mathcal{L}\{t^n u(t)\} = \frac{n!}{s^{n+1}}, \qquad n = 0,1,2,\dots
$$

**Derivation (repeated integration by parts):**

$$
X(s) = \int_0^\infty t^3 e^{-st}\,dt
$$

Let $u = t^3,\ dv = e^{-st}dt \Rightarrow du = 3t^2 dt,\ v = -\dfrac{1}{s}e^{-st}$

$$
X(s) = \left.-\frac{t^3}{s}e^{-st}\right|_0^\infty + \frac{3}{s}\int_0^\infty t^2 e^{-st}dt = \frac{3}{s}\int_0^\infty t^2 e^{-st}dt
$$

Repeating this two more times (each step reduces the power of $t$ by one and introduces another factor):

$$
\int_0^\infty t^2 e^{-st}dt = \frac{2}{s}\int_0^\infty t\,e^{-st}dt, \qquad \int_0^\infty t\,e^{-st}dt = \frac{1}{s}\int_0^\infty e^{-st}dt = \frac{1}{s^2}
$$

Combining:

$$
X(s) = \frac{3}{s}\cdot\frac{2}{s}\cdot\frac{1}{s}\cdot\frac{1}{s^2} = \frac{3!}{s^4}
$$

$$
\boxed{\mathcal{L}\{t^3 u(t)\} = \frac{6}{s^4}}
$$

---

## Problem 2: Inverse Laplace Transforms

### (a) $\dfrac{X(s)}{F(s)} = \dfrac{7}{s^2+5s+10}$

**Step 1 — Complete the square in the denominator:**

$$
s^2+5s+10 = \left(s+\frac{5}{2}\right)^2 + \left(10-\frac{25}{4}\right) = \left(s+\frac{5}{2}\right)^2 + \frac{15}{4}
$$

So:

$$
X(s) = \frac{7}{\left(s+\frac{5}{2}\right)^2 + \left(\frac{\sqrt{15}}{2}\right)^2}
$$

This is an underdamped (complex-pole) form with $a = \dfrac{5}{2}$ and $\omega_d = \dfrac{\sqrt{15}}{2} \approx 1.9365$.

**Step 2 — Match to the standard pair:**

$$
\mathcal{L}^{-1}\left\{\frac{\omega}{(s+a)^2+\omega^2}\right\} = e^{-at}\sin(\omega t)
$$

Write $7 = \dfrac{7}{\omega_d}\cdot \omega_d$ so the numerator becomes $\omega_d$:

$$
X(s) = \frac{7}{\omega_d}\cdot\frac{\omega_d}{(s+\frac52)^2+\omega_d^2}
$$

**Step 3 — Inverse transform:**

$$
\boxed{x(t) = \frac{14}{\sqrt{15}}\,e^{-2.5t}\sin\!\left(\frac{\sqrt{15}}{2}t\right)\,u(t) \approx 3.615\,e^{-2.5t}\sin(1.9365\,t)\,u(t)}
$$

---

### (b) $\dfrac{X(s)}{F(s)} = \dfrac{15}{(s+10)(s+11)}$

**Step 1 — Partial fraction decomposition:**

$$
\frac{15}{(s+10)(s+11)} = \frac{A}{s+10} + \frac{B}{s+11}
$$

**Step 2 — Solve for A and B (cover-up method):**

$$
A = \left.\frac{15}{s+11}\right|_{s=-10} = \frac{15}{1} = 15
$$

$$
B = \left.\frac{15}{s+10}\right|_{s=-11} = \frac{15}{-1} = -15
$$

So:

$$
X(s) = \frac{15}{s+10} - \frac{15}{s+11}
$$

**Step 3 — Inverse transform** using $\mathcal{L}^{-1}\left\{\dfrac{1}{s+a}\right\} = e^{-at}u(t)$:

$$
\boxed{x(t) = \left(15\,e^{-10t} - 15\,e^{-11t}\right)u(t)}
$$

---

### (c) $\dfrac{X(s)}{F(s)} = \dfrac{s+3}{s^3+11s^2+12s+18}$

**Step 1 — Find the poles (roots of the denominator).**

The denominator does not have integer/rational roots (rational-root candidates $\pm1,\pm2,\pm3,\pm6,\pm9,\pm18$ were all tested and fail), so the cubic is solved numerically:

$$
s^3+11s^2+12s+18 = 0
$$

$$
\Rightarrow\quad s_1 = -9.9782,\qquad s_{2,3} = -0.5109 \pm j\,1.2421
$$

So the denominator factors as:

$$
s^3+11s^2+12s+18 = (s+9.9782)\left[(s+0.5109)^2+(1.2421)^2\right]
$$

**Step 2 — Partial fraction decomposition:**

$$
X(s) = \frac{A}{s+9.9782} + \frac{Bs+C}{(s+0.5109)^2+(1.2421)^2}
$$

Solving (equating coefficients / residue method) gives:

$$
A = -0.07654,\qquad B = 0.07654,\qquad C = 0.31438
$$

**Step 3 — Rewrite the complex-pole term around the pole's real part** so it splits into a cosine part and a sine part:

$$
Bs + C = B(s+0.5109) + \big(C - 0.5109B\big) = 0.07654(s+0.5109) + 0.27527
$$

$$
X(s) = \frac{-0.07654}{s+9.9782} + \frac{0.07654(s+0.5109)}{(s+0.5109)^2+(1.2421)^2} + \frac{0.27527}{(s+0.5109)^2+(1.2421)^2}
$$

**Step 4 — Inverse transform**, using:

$$
\mathcal{L}^{-1}\!\left\{\frac{s+a}{(s+a)^2+\omega^2}\right\}=e^{-at}\cos(\omega t),\qquad
\mathcal{L}^{-1}\!\left\{\frac{\omega}{(s+a)^2+\omega^2}\right\}=e^{-at}\sin(\omega t)
$$

The sine term coefficient is $\dfrac{0.27527}{1.2421} = 0.22167$.

$$
\boxed{x(t) = -0.0765\,e^{-9.978\,t} + e^{-0.5109\,t}\Big[0.0765\cos(1.2421\,t) + 0.2217\sin(1.2421\,t)\Big]\;u(t)}
$$

**Interpretation:** the response consists of a fast-decaying exponential mode (from the real pole near $s=-9.98$) that dies out almost immediately, plus a slower, lightly-damped oscillatory mode (from the complex pole pair at $s=-0.511\pm j1.242$) that dominates the transient response for larger $t$.

