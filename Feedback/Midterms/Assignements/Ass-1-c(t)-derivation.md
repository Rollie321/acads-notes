# Skill-Assessment 4.3 — Full Derivation of $c(t)$ from $G(s)$

## Part A: General Step-by-Step Process

Given $G(s)=\dfrac{C(s)}{R(s)}$ and a **unit step input** $r(t)=1 \Rightarrow R(s)=\dfrac{1}{s}$, here is the complete procedure to find $c(t)$:

**Step 1 — Find $C(s)$.**
$$C(s) = G(s)\,R(s) = G(s)\cdot\frac{1}{s}$$

**Step 2 — Factor the denominator of $G(s)$.**
Write $s^2+2\zeta\omega_n s+\omega_n^2$ in factored form to find the poles (roots), using the quadratic formula:
$$s_{1,2} = -\zeta\omega_n \pm \omega_n\sqrt{\zeta^2-1}$$
This tells you whether the poles are real and distinct, real and repeated, complex conjugate, or purely imaginary — which determines the *form* of $c(t)$.

**Step 3 — Perform Partial Fraction Expansion.**
Break $C(s)$ into simple terms whose inverse Laplace transforms are known, matching the pole type from Step 2:

| Pole type                             | Expansion form                                                 |
| ------------------------------------- | -------------------------------------------------------------- |
| Complex conjugate poles ($0<\zeta<1$) | $\dfrac{A}{s} + \dfrac{Bs+C}{s^2+2\zeta\omega_n s+\omega_n^2}$ |
| Two distinct real poles ($\zeta>1$)   | $\dfrac{A}{s}+\dfrac{B}{s+s_1}+\dfrac{C}{s+s_2}$               |
| Repeated real pole ($\zeta=1$)        | $\dfrac{A}{s}+\dfrac{B}{s+\omega_n}+\dfrac{C}{(s+\omega_n)^2}$ |
| Purely imaginary poles ($\zeta=0$)    | $\dfrac{A}{s}+\dfrac{Bs}{s^2+\omega_n^2}$                      |

Solve for the unknown constants $A,B,C$ by clearing denominators and matching coefficients (or using the residue method).

**Step 4 — Take the Inverse Laplace Transform** of each term using standard transform pairs:
$$\mathcal{L}^{-1}\left\{\frac{1}{s}\right\}=1 \qquad \mathcal{L}^{-1}\left\{\frac{1}{s+a}\right\}=e^{-at} \qquad \mathcal{L}^{-1}\left\{\frac{1}{(s+a)^2}\right\}=te^{-at}$$
$$\mathcal{L}^{-1}\left\{\frac{s+a}{(s+a)^2+\omega_d^2}\right\}=e^{-at}\cos(\omega_d t) \qquad \mathcal{L}^{-1}\left\{\frac{\omega_d}{(s+a)^2+\omega_d^2}\right\}=e^{-at}\sin(\omega_d t)$$

**Step 5 — Combine sine/cosine terms** (if present) into a single phase-shifted cosine using:
$$M\cos(\omega_d t) + N\sin(\omega_d t) = R\cos(\omega_d t - \phi), \quad R=\sqrt{M^2+N^2},\ \phi=\tan^{-1}\!\left(\frac{N}{M}\right)$$

**Step 6 — Write the final $c(t)$**, generally as a constant (steady-state) plus decaying/oscillating transient terms.

---

## Part B: Worked Solutions for Each Case

### 1. $G(s)=\dfrac{400}{s^2+12s+400}$, unit step input

**Step 1:**
$$C(s) = \frac{400}{s(s^2+12s+400)}$$

**Step 2 — Poles:**
$$s_{1,2}=-6\pm\sqrt{36-400}=-6\pm j19.08 \quad\text{(complex conjugate → underdamped)}$$

**Step 3 — Partial fractions:**
$$\frac{400}{s(s^2+12s+400)} = \frac{A}{s}+\frac{Bs+C}{s^2+12s+400}$$
Multiply both sides by $s(s^2+12s+400)$:
$$400 = A(s^2+12s+400)+(Bs+C)s$$
Set $s=0$: $\ 400=400A \Rightarrow A=1$
Match $s^2$: $\ 0=A+B \Rightarrow B=-1$
Match $s^1$: $\ 0=12A+C \Rightarrow C=-12$

$$C(s)=\frac{1}{s}-\frac{s+12}{s^2+12s+400}$$

**Step 4 — Complete the square** in the denominator: $s^2+12s+400=(s+6)^2+(19.08)^2$
Rewrite the numerator to match the derivative/cosine and sine forms:
$$\frac{s+12}{(s+6)^2+19.08^2}=\frac{(s+6)+6}{(s+6)^2+19.08^2}=\frac{s+6}{(s+6)^2+19.08^2}+\frac{6}{19.08}\cdot\frac{19.08}{(s+6)^2+19.08^2}$$

Taking the inverse transform:
$$c(t)=1-e^{-6t}\cos(19.08t)-0.314\,e^{-6t}\sin(19.08t)$$

**Step 5 — Combine into a single cosine:**
$$R=\sqrt{1^2+0.314^2}=1.048,\quad \phi=\tan^{-1}(0.314)=17.3^\circ$$

**Step 6 — Final answer:**
$$\boxed{c(t) = 1 - 1.048\,e^{-6t}\cos(19.08t-17.3^\circ)}$$

which matches the general form given in the assessment:
$$c(t)=A+Be^{-6t}\cos(19.08t+\phi)$$

---

### 2. $G(s)=\dfrac{900}{s^2+90s+900}$, unit step input

**Step 1:**
$$C(s)=\frac{900}{s(s^2+90s+900)}$$

**Step 2 — Poles:**
$$s_{1,2}=-45\pm\sqrt{2025-900}=-45\pm33.54 \Rightarrow s_1=-11.46,\ s_2=-78.54$$
(two distinct real poles → overdamped)

**Step 3 — Partial fractions:**
$$\frac{900}{s(s+11.46)(s+78.54)}=\frac{A}{s}+\frac{B}{s+11.46}+\frac{C}{s+78.54}$$

Solve using the residue method:
$$A = \left.\frac{900}{(s+11.46)(s+78.54)}\right|_{s=0}=\frac{900}{(11.46)(78.54)}=1$$
$$B = \left.\frac{900}{s(s+78.54)}\right|_{s=-11.46}=\frac{900}{(-11.46)(67.08)}=-1.17$$
$$C = \left.\frac{900}{s(s+11.46)}\right|_{s=-78.54}=\frac{900}{(-78.54)(-67.08)}=0.17$$

**Step 4 — Inverse transform:**
$$c(t)=1-1.17e^{-11.46t}+0.17e^{-78.54t}$$

**Step 6 — Final answer (general form):**
$$\boxed{c(t)=A+Be^{-78.54t}+Ce^{-11.46t}}$$

---

### 3. $G(s)=\dfrac{225}{s^2+30s+225}$, unit step input

**Step 1:**
$$C(s)=\frac{225}{s(s^2+30s+225)}=\frac{225}{s(s+15)^2}$$

**Step 2 — Poles:** $s=-15$ (repeated) → critically damped

**Step 3 — Partial fractions:**
$$\frac{225}{s(s+15)^2}=\frac{A}{s}+\frac{B}{s+15}+\frac{C}{(s+15)^2}$$

$$A=\left.\frac{225}{(s+15)^2}\right|_{s=0}=\frac{225}{225}=1$$

Multiply out: $225 = A(s+15)^2+Bs(s+15)+Cs$

Set $s=-15$: $\ 225=C(-15)\Rightarrow C=-15$

Match $s^2$ coefficient: $\ 0=A+B\Rightarrow B=-1$

**Step 4 — Inverse transform:**
$$c(t)=1-e^{-15t}-15te^{-15t}$$

**Step 6 — Final answer (general form):**
$$\boxed{c(t)=A+Be^{-15t}+Cte^{-15t}}$$

---

### 4. $G(s)=\dfrac{625}{s^2+625}$, unit step input

**Step 1:**
$$C(s)=\frac{625}{s(s^2+625)}$$

**Step 2 — Poles:** $s=\pm j25$ (purely imaginary) → undamped

**Step 3 — Partial fractions:**
$$\frac{625}{s(s^2+625)}=\frac{A}{s}+\frac{Bs}{s^2+625}$$
$$625=A(s^2+625)+Bs^2$$
Set $s=0$: $A=1$. Match $s^2$: $0=A+B \Rightarrow B=-1$

**Step 4 — Inverse transform:**
$$c(t)=1-\cos(25t)$$

**Step 6 — Final answer (general form):**
$$\boxed{c(t)=A+B\cos(25t+\phi)}$$

---

## Part C: Recovering $G(s)$ Back from $c(t)$

If you're instead given $c(t)$ (the step response) and asked to find the transfer function $G(s)=\dfrac{C(s)}{R(s)}$, run the process in reverse:

**Step 1 — Take the Laplace Transform of $c(t)$** to get $C(s)$, using the standard forward transform pairs:
$$\mathcal{L}\{1\}=\frac{1}{s}\qquad \mathcal{L}\{e^{-at}\}=\frac{1}{s+a}\qquad \mathcal{L}\{te^{-at}\}=\frac{1}{(s+a)^2}$$
$$\mathcal{L}\{e^{-at}\cos(\omega_d t)\}=\frac{s+a}{(s+a)^2+\omega_d^2}\qquad \mathcal{L}\{e^{-at}\sin(\omega_d t)\}=\frac{\omega_d}{(s+a)^2+\omega_d^2}$$

**Step 2 — Combine the terms of $C(s)$ over a common denominator.**

**Step 3 — Since $R(s)=\dfrac{1}{s}$** (unit step), solve:
$$G(s)=\frac{C(s)}{R(s)}=C(s)\cdot s$$
i.e., **multiply $C(s)$ by $s$** and simplify — the $s$ in the numerator cancels the $s$ that the step input's Laplace transform put in the denominator, since $A/s$ (the steady-state term) cancels a pole at the origin.

**Step 4 — Verify the result** is a proper second-order transfer function of the form $\dfrac{\omega_n^2}{s^2+2\zeta\omega_n s+\omega_n^2}$ (its DC gain, $G(0)$, should equal 1 for a system with a unit step final value of 1).

### Example — reversing Case 1:

Start from
$$c(t)=1-1.048\,e^{-6t}\cos(19.08t-17.3^\circ)$$
which, once expanded back into separate cosine/sine terms, Laplace-transforms to:
$$C(s)=\frac{1}{s}-\frac{s+12}{(s+6)^2+19.08^2}=\frac{1}{s}-\frac{s+12}{s^2+12s+400}$$

Combine over a common denominator:
$$C(s)=\frac{(s^2+12s+400)-s(s+12)}{s(s^2+12s+400)}=\frac{400}{s(s^2+12s+400)}$$

Multiply by $s$ (i.e., divide out $R(s)=1/s$):
$$G(s)=s\cdot C(s)=\frac{400}{s^2+12s+400}$$

This recovers the **original transfer function**, confirming the round trip:
$$\boxed{G(s)=\frac{400}{s^2+12s+400}}$$

The same reverse process — Laplace transform $c(t)$, combine into one fraction, multiply by $s$ — works for all four cases and returns exactly the $G(s)$ each problem started with.
