#### Skill Assessment 4.3
###### Find each of the following transfer functions, write, by inspection the general form of the stop response:
1. $G(s)=\frac{400}{s^2+12s+400}$
2. $G(s)=\frac{900}{s^2+90s+900}$
3. $G(s)=\frac{225}{s^2+30s+225}$
4. $G(s)=\frac{625}{s^2+625}$
##### Answers:
1. $c(t) = A + Be^{-6t}cos(19.08t+\phi)$
2. $c(t)=A+Be^{-78.54t}+Ce^{-11.46t}$
3. $c(t)=A+Be^{-15t}+Cte(-15t)$
4. $c(t)=A+B\ cos(25t+\phi)$
---

#### Skill Assessment 4.4
###### For each of the transfer functions in Skill-Assessment 4.3, do the following: (1)Find the values of $\zeta$ and $\omega_n$; (2) characterize the nature of the response.
###### Answers:
1. $\zeta =0.3, \omega_n=20$; underdamped
2. $\zeta =1.5, \omega_n=30$; overdamped 
3. $\zeta =1, \omega_n=15$; critically damped 
4. $\zeta =0, \omega_n=25$; undamped 
---

##### Skill-Assessment 4.5
###### Find the $\zeta,\ \omega_n,\ T_s,\ T_p,\ T_r$ and $\%OS$ for the system whose transfer function is: 
$$G(s)=\frac{361}{s^2+16s+361}$$

###### Answer:
$$
\begin{aligned}
\zeta=0.421\\
\omega_n=19\\
T_s=0.5s\\
T_p=0.182s\\
T_r=0.079s\\
\%OS=23.3\%
\end{aligned}
$$

---
---

## Solutions

Every second-order transfer function here has the standard form

$$G(s)=\frac{\omega_n^2}{s^2+2\zeta\omega_n s+\omega_n^2}$$

so the general strategy for **all** problems is:

1. Match $\omega_n^2$ to the constant in the numerator (and denominator's constant term) $\Rightarrow$ solve for $\omega_n$.
2. Match $2\zeta\omega_n$ to the coefficient of $s$ $\Rightarrow$ solve for $\zeta$.
3. Use $\zeta$ to classify the system:
   - $\zeta = 0$ → **undamped**
   - $0<\zeta<1$ → **underdamped**
   - $\zeta = 1$ → **critically damped**
   - $\zeta > 1$ → **overdamped**
4. Write the time-domain step response using the standard form for that damping case.

---

### Skill-Assessment 4.3 — Step Response Forms

#### 1. $G(s)=\dfrac{400}{s^2+12s+400}$

**Step 1 — Find $\omega_n$:**
$$\omega_n^2 = 400 \implies \omega_n = \sqrt{400} = 20$$

**Step 2 — Find $\zeta$:**
$$2\zeta\omega_n = 12 \implies \zeta = \frac{12}{2(20)} = \frac{12}{40} = 0.3$$

**Step 3 — Classify:** Since $0<\zeta<1$, the system is **underdamped**.

**Step 4 — Find the damped frequency $\omega_d$:**
$$\omega_d = \omega_n\sqrt{1-\zeta^2} = 20\sqrt{1-(0.3)^2} = 20\sqrt{0.91} = 19.08$$

**Step 5 — Write the response.** An underdamped system has poles at $s=-\zeta\omega_n \pm j\omega_d$, giving an exponentially decaying sinusoid riding on a constant:

$$\boxed{c(t) = A + Be^{-6t}\cos(19.08t+\phi)}$$

(the real part of the pole is $-\zeta\omega_n = -(0.3)(20) = -6$)

---

#### 2. $G(s)=\dfrac{900}{s^2+90s+900}$

**Step 1 — Find $\omega_n$:**
$$\omega_n^2 = 900 \implies \omega_n = \sqrt{900} = 30$$

**Step 2 — Find $\zeta$:**
$$2\zeta\omega_n = 90 \implies \zeta = \frac{90}{2(30)} = \frac{90}{60} = 1.5$$

**Step 3 — Classify:** Since $\zeta>1$, the system is **overdamped**.

**Step 4 — Find the two real poles** using
$$s_{1,2} = -\zeta\omega_n \pm \omega_n\sqrt{\zeta^2-1}$$
$$s_{1,2} = -(1.5)(30) \pm 30\sqrt{(1.5)^2-1} = -45 \pm 30\sqrt{1.25} = -45\pm 33.54$$

$$s_1 = -45+33.54 = -11.46 \qquad s_2=-45-33.54=-78.54$$

**Step 5 — Write the response.** An overdamped system is the sum of two decaying exponentials plus a constant:

$$\boxed{c(t)=A+Be^{-78.54t}+Ce^{-11.46t}}$$

---

#### 3. $G(s)=\dfrac{225}{s^2+30s+225}$

**Step 1 — Find $\omega_n$:**
$$\omega_n^2 = 225 \implies \omega_n = \sqrt{225}=15$$

**Step 2 — Find $\zeta$:**
$$2\zeta\omega_n = 30 \implies \zeta = \frac{30}{2(15)} = \frac{30}{30} = 1$$

**Step 3 — Classify:** Since $\zeta=1$, the system is **critically damped**.

**Step 4 — Find the repeated pole:**
$$s = -\zeta\omega_n = -(1)(15) = -15 \quad \text{(repeated)}$$

**Step 5 — Write the response.** A critically damped system has a repeated real pole, so the response contains both a pure exponential and a $t\,e^{-\zeta\omega_n t}$ term:

$$\boxed{c(t)=A+Be^{-15t}+Cte^{-15t}}$$

---

#### 4. $G(s)=\dfrac{625}{s^2+625}$

**Step 1 — Find $\omega_n$:**
$$\omega_n^2 = 625 \implies \omega_n=\sqrt{625}=25$$

**Step 2 — Find $\zeta$:**
Here the $s$ term is missing, so its coefficient is $0$:
$$2\zeta\omega_n = 0 \implies \zeta = 0$$

**Step 3 — Classify:** Since $\zeta=0$, the system is **undamped**.

**Step 4 — Write the response.** With no damping, the poles are purely imaginary ($s=\pm j\omega_n$), giving a pure, undecayed sinusoid on top of a constant:

$$\boxed{c(t)=A+B\cos(25t+\phi)}$$

---

### Skill-Assessment 4.4 — $\zeta$, $\omega_n$, and Nature of Response

This reuses the same $\omega_n$ and $\zeta$ values found step-by-step above.

#### 1. $G(s)=\dfrac{400}{s^2+12s+400}$
$$\omega_n = \sqrt{400}=20 \qquad \zeta = \frac{12}{2(20)}=0.3$$
Since $0<\zeta<1$: **underdamped**

$$\boxed{\zeta=0.3,\ \omega_n=20;\ \text{underdamped}}$$

#### 2. $G(s)=\dfrac{900}{s^2+90s+900}$
$$\omega_n=\sqrt{900}=30 \qquad \zeta=\frac{90}{2(30)}=1.5$$
Since $\zeta>1$: **overdamped**

$$\boxed{\zeta=1.5,\ \omega_n=30;\ \text{overdamped}}$$

#### 3. $G(s)=\dfrac{225}{s^2+30s+225}$
$$\omega_n=\sqrt{225}=15 \qquad \zeta=\frac{30}{2(15)}=1$$
Since $\zeta=1$: **critically damped**

$$\boxed{\zeta=1,\ \omega_n=15;\ \text{critically damped}}$$

#### 4. $G(s)=\dfrac{625}{s^2+625}$
$$\omega_n=\sqrt{625}=25 \qquad \zeta=\frac{0}{2(25)}=0$$
Since $\zeta=0$: **undamped**

$$\boxed{\zeta=0,\ \omega_n=25;\ \text{undamped}}$$

---

### Skill-Assessment 4.5 — Full Transient Response Specs

$$G(s)=\frac{361}{s^2+16s+361}$$

**Step 1 — Find $\omega_n$:**
$$\omega_n^2 = 361 \implies \omega_n = \sqrt{361} = 19$$

**Step 2 — Find $\zeta$:**
$$2\zeta\omega_n = 16 \implies \zeta = \frac{16}{2(19)} = \frac{16}{38} = 0.421$$

Since $0<\zeta<1$, this is an underdamped system, so all the transient-response formulas below apply.

**Step 3 — Settling Time $T_s$** (2% criterion):
$$T_s = \frac{4}{\zeta\omega_n} = \frac{4}{(0.421)(19)} = \frac{4}{8.0} = 0.5\ \text{s}$$

**Step 4 — Peak Time $T_p$:**

First find the damped natural frequency:
$$\omega_d = \omega_n\sqrt{1-\zeta^2} = 19\sqrt{1-(0.421)^2}=19\sqrt{1-0.1773}=19\sqrt{0.8227}=19(0.9070)=17.23$$

Then:
$$T_p = \frac{\pi}{\omega_d} = \frac{\pi}{17.23} = 0.182\ \text{s}$$

**Step 5 — Percent Overshoot $\%OS$:**
$$\%OS = 100\,e^{-\left(\zeta\pi/\sqrt{1-\zeta^2}\right)}$$
$$= 100\,e^{-(0.421)(\pi)/0.9070} = 100\,e^{-1.3232/0.9070}=100\,e^{-1.459}$$
$$= 100(0.2325) = 23.3\%$$

**Step 6 — Rise Time $T_r$:**

Rise time doesn't have a clean closed form, so it's read from the normalized rise-time vs. $\zeta$ table (Nise, *Control Systems Engineering*) at $\zeta = 0.421$, which gives a normalized rise time of approximately $1.5$:
$$\omega_n T_r \approx 1.5 \implies T_r \approx \frac{1.5}{19} \approx 0.079\ \text{s}$$

**Summary of Results:**

$$
\boxed{
\begin{aligned}
\zeta &= 0.421\\
\omega_n &= 19\ \text{rad/s}\\
T_s &= 0.5\ \text{s}\\
T_p &= 0.182\ \text{s}\\
T_r &= 0.079\ \text{s}\\
\%OS &= 23.3\%
\end{aligned}
}
$$
