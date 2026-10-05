# Control Systems — Seat Work No. 1 (Midterm) — Solutions

---

## 1. Pole-Zero Identification and System Classification (6 pts)

$$
G(s) = \frac{10(s+2)}{(s+1)(s+4)(s^2+4s+13)}
$$

**a. Zeros:** Set the numerator to zero:

$$
s + 2 = 0 \;\Rightarrow\; z = -2
$$

**b. Poles:** Set each denominator factor to zero:

$$
s+1=0 \Rightarrow s=-1, \qquad s+4=0 \Rightarrow s=-4
$$

$$
s^2+4s+13=0 \;\Rightarrow\; s = \frac{-4 \pm \sqrt{16-52}}{2} = -2 \pm j3
$$

$$
\text{Poles: } s = -1,\ -4,\ -2+j3,\ -2-j3
$$

**c. Location in the s-plane:** Zero at $(-2,0)$; real poles at $(-1,0)$ and $(-4,0)$; complex-conjugate pole pair at $(-2,\pm3)$ — all in the left half-plane.

**d. Stability:** All poles have negative real parts (all lie in the LHP), so:

$$
\boxed{\text{The system is stable.}}
$$

---

## 2. Time Response from Poles and Zeros (6 pts)

$$
T(s) = \frac{25}{s^2+6s+25}
$$

Compare with the standard second-order form:

$$
s^2 + 2\zeta\omega_n s + \omega_n^2
$$

$$
\omega_n^2 = 25 \;\Rightarrow\; \omega_n = 5\ \text{rad/s}
$$

$$
2\zeta\omega_n = 6 \;\Rightarrow\; \zeta = \frac{6}{10} = 0.6
$$

**a. Closed-loop poles:**

$$
s = -\zeta\omega_n \pm j\omega_n\sqrt{1-\zeta^2} = -3 \pm j4
$$

**b.** Since $0<\zeta<1$: **underdamped**

**c.** $\omega_n = 5\ \text{rad/s}, \quad \zeta = 0.6$

**d. Damped natural frequency:**

$$
\omega_d = \omega_n\sqrt{1-\zeta^2} = 5(0.8) = 4\ \text{rad/s}
$$

---

## 3. Effect of Pole Location on Transient Response (5 pts)

**System A:** $p_1=-2,\ p_2=-8$ — real, negative → stable, non-oscillatory (overdamped)

**System B:** $p_{1,2} = -2 \pm j6$ — complex, negative real part → stable, oscillatory (underdamped)

**System C:** $p_1=+2,\ p_2=-8$ — one pole in the RHP → **unstable**

**a.** A: stable, B: stable, C: unstable

**b.** A: non-oscillatory, B: oscillatory, C: non-oscillatory (but diverges)

**c.** System **B** exhibits oscillatory behavior (complex-conjugate poles)

**d.** System **C** is unstable (pole at $s=+2$ in the RHP)

**e.** Moving a pole farther to the left increases $|\text{Re}(p)|$, which decreases the time constant

$$
\tau = \frac{1}{|\text{Re}(p)|}
$$

so the exponential term decays faster, giving a **faster** transient response.

---

## 4. Determining Damping Ratio and Natural Frequency (6 pts)

$$
G(s) = \frac{64}{s^2+8s+64}
$$

$$
\omega_n^2 = 64 \;\Rightarrow\; \omega_n = 8\ \text{rad/s}
$$

$$
2\zeta\omega_n = 8 \;\Rightarrow\; \zeta = \frac{8}{16} = 0.5
$$

$$
\omega_d = \omega_n\sqrt{1-\zeta^2} = 8\sqrt{0.75} \approx 6.93\ \text{rad/s}
$$

**d.** Since $\zeta = 0.5 < 1$: **underdamped**

**e. Pole locations:**

$$
s = -\zeta\omega_n \pm j\omega_d = -4 \pm j6.93
$$

---

## 5. Second-Order System from Given Specifications (7 pts)

Given: $\zeta = 0.5, \quad \omega_n = 10\ \text{rad/s}$

**a. Characteristic equation:**

$$
s^2 + 2\zeta\omega_n s + \omega_n^2 = s^2 + 10s + 100 = 0
$$

**b. Closed-loop poles:**

$$
s = -\zeta\omega_n \pm j\omega_n\sqrt{1-\zeta^2} = -5 \pm j(10 \times 0.866) = -5 \pm j8.66
$$

**c. Damped natural frequency:**

$$
\omega_d = 8.66\ \text{rad/s}
$$

**d. Percent overshoot:**

$$
\%OS = 100\,e^{-\zeta\pi/\sqrt{1-\zeta^2}} = 100\,e^{-0.5\pi/0.866} = 100\,e^{-1.814} \approx 16.3\%
$$

**e. Peak time:**

$$
T_p = \frac{\pi}{\omega_d} = \frac{\pi}{8.66} \approx 0.363\ \text{s}
$$

**f. Settling time (2% criterion):**

$$
T_s = \frac{4}{\zeta\omega_n} = \frac{4}{5} = 0.8\ \text{s}
$$

---

## 6. Determine $\zeta$ from Percent Overshoot (5 pts)

Given: $\%OS = 20\% = 0.20$

$$
\%OS = 100\,e^{-\zeta\pi/\sqrt{1-\zeta^2}}
$$

**Step 1 — Take the natural log:**

$$
\ln(0.20) = \frac{-\zeta\pi}{\sqrt{1-\zeta^2}} \;\Rightarrow\; -1.6094 = \frac{-\zeta\pi}{\sqrt{1-\zeta^2}}
$$

$$
1.6094 = \frac{\zeta\pi}{\sqrt{1-\zeta^2}}
$$

**Step 2 — Square both sides:**

$$
(1.6094)^2 = \frac{\zeta^2\pi^2}{1-\zeta^2} \;\Rightarrow\; 2.590 = \frac{9.8696\,\zeta^2}{1-\zeta^2}
$$

$$
2.590(1-\zeta^2) = 9.8696\,\zeta^2 \;\Rightarrow\; 2.590 = 12.4596\,\zeta^2
$$

**Step 3 — Solve for $\zeta$:**

$$
\zeta^2 = \frac{2.590}{12.4596} = 0.2078 \;\Rightarrow\; \zeta = \sqrt{0.2078} \approx 0.456
$$

$$
\boxed{\zeta \approx 0.456}
$$

*(Equivalent to the direct formula $\zeta = \dfrac{-\ln(\%OS/100)}{\sqrt{\pi^2+\ln^2(\%OS/100)}}$.)*

---

## 7. Complete Transient Response Analysis (8 pts)

$$
T(s) = \frac{100}{s^2+10s+100}
$$

$$
\omega_n^2 = 100 \;\Rightarrow\; \omega_n = 10\ \text{rad/s}
$$

$$
2\zeta\omega_n = 10 \;\Rightarrow\; \zeta = 0.5
$$

$$
\omega_d = 10\sqrt{1-0.25} = 8.66\ \text{rad/s}
$$

**d. Settling time:**

$$
T_s = \frac{4}{\zeta\omega_n} = \frac{4}{5} = 0.8\ \text{s}
$$

**e. Peak time:**

$$
T_p = \frac{\pi}{\omega_d} = \frac{\pi}{8.66} \approx 0.363\ \text{s}
$$

**f. Percent overshoot:**

$$
\%OS = 100\,e^{-0.5\pi/0.866} \approx 16.3\%
$$

**g. Rise time** (0%–100%, using $T_r = \dfrac{\pi-\beta}{\omega_d}$, where $\beta=\cos^{-1}\zeta$):

$$
\beta = \cos^{-1}(0.5) = 60^\circ = 1.047\ \text{rad}
$$

$$
T_r = \frac{\pi - 1.047}{8.66} = \frac{2.094}{8.66} \approx 0.242\ \text{s}
$$

**h.** Since $\zeta = 0.5 < 1$: **the system is underdamped**

---

## 8. Design a Second-Order System Based on Time Specifications (8 pts)

Requirements: $T_s \le 2\ \text{s}$ and $\%OS \le 10\%$

**a. Minimum damping ratio (from $\%OS \le 10\%$):**

$$
\zeta_{min} = \frac{-\ln(0.1)}{\sqrt{\pi^2+\ln^2(0.1)}} = \frac{2.3026}{\sqrt{9.8696+5.302}} = \frac{2.3026}{3.895} \approx 0.591
$$

**b. Minimum $\zeta\omega_n$ (from settling-time spec):**

$$
T_s = \frac{4}{\zeta\omega_n} \le 2 \;\Rightarrow\; \zeta\omega_n \ge \frac{4}{2} = 2
$$

$$
(\zeta\omega_n)_{min} = 2
$$

**c. Suitable value of $\omega_n$:**

Choose $\zeta = 0.6$ (satisfies $\zeta \ge 0.591$ with margin). Then:

$$
\omega_n = \frac{(\zeta\omega_n)_{min}}{\zeta} = \frac{2}{0.6} \approx 3.33\ \text{rad/s}
$$

**d. Approximate characteristic equation:**

$$
s^2 + 2\zeta\omega_n s + \omega_n^2 \approx s^2 + 4s + 11.1 = 0
$$

**e. Dominant pole location:**

$$
s = -\zeta\omega_n \pm j\omega_n\sqrt{1-\zeta^2} = -2 \pm j(3.33 \times 0.8) = -2 \pm j2.67
$$

*(Note: any $\zeta \ge 0.591$ with $\zeta\omega_n \ge 2$ satisfies both specifications — this is one valid design.)*

---

## 9. RLC Electrical Circuit (5 pts)

Series RLC circuit: $R=20\,\Omega,\ L=1\,\text{H},\ C=0.01\,\text{F}$, output $v_o(t)$ across the capacitor.

**a. Derive the transfer function:**

For the series loop with current $I(s)$:

$$
V_i(s) = I(s)\left[Ls + R + \frac{1}{Cs}\right], \qquad V_o(s) = I(s)\cdot\frac{1}{Cs}
$$

$$
G(s) = \frac{V_o(s)}{V_i(s)} = \frac{1/Cs}{Ls+R+1/Cs} = \frac{1}{LCs^2+RCs+1}
$$

Substitute values: $LC = (1)(0.01) = 0.01$, $\ RC = (20)(0.01) = 0.2$

$$
G(s) = \frac{1}{0.01s^2+0.2s+1}
$$

**b. Standard second-order form** (multiply numerator and denominator by 100):

$$
G(s) = \frac{100}{s^2+20s+100}
$$

**c. Natural frequency:**

$$
\omega_n^2 = 100 \;\Rightarrow\; \omega_n = 10\ \text{rad/s}
$$

**d. Damping ratio:**

$$
2\zeta\omega_n = 20 \;\Rightarrow\; \zeta = \frac{20}{20} = 1
$$

**e.** Since $\zeta = 1$: **the system is critically damped**

---

## 10. Mass-Spring-Damper System (5 pts)

Given: $M=2\,\text{kg},\ b=8\,\text{N·s/m},\ k=18\,\text{N/m}$

Governing equation:

$$
M\ddot{x} + b\dot{x} + kx = F(t)
$$

**a. Transfer function** (Laplace transform, zero initial conditions):

$$
Ms^2X(s) + bsX(s) + kX(s) = F(s)
$$

$$
G(s) = \frac{X(s)}{F(s)} = \frac{1}{Ms^2+bs+k} = \frac{1}{2s^2+8s+18}
$$

Dividing numerator and denominator by $M=2$ (standard form):

$$
G(s) = \frac{0.5}{s^2+4s+9}
$$

**b. Natural frequency:**

$$
\omega_n^2 = 9 \;\Rightarrow\; \omega_n = 3\ \text{rad/s}
$$

**c. Damping ratio:**

$$
2\zeta\omega_n = 4 \;\Rightarrow\; \zeta = \frac{4}{6} \approx 0.667
$$

**d. Damped frequency:**

$$
\omega_d = \omega_n\sqrt{1-\zeta^2} = 3\sqrt{1-0.444} = 3(0.745) \approx 2.24\ \text{rad/s}
$$

**e. Pole locations:**

$$
s = -\zeta\omega_n \pm j\omega_d = -2 \pm j2.24
$$

**f. Type of damping:** Since $\zeta \approx 0.667 < 1$: **underdamped**

**g. Expected transient response:** Because the system is underdamped, a step force input causes the mass's displacement $x(t)$ to overshoot its final steady-state value and oscillate at the damped frequency $\omega_d \approx 2.24\ \text{rad/s}$, with the oscillation amplitude decaying under an envelope of $e^{-2t}$ (set by the real part of the poles), before settling to the final value.
