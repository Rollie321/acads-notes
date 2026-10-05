# Section 8.2E — Inverse Laplace Transform: Step-by-Step Solutions

Throughout, these standard pairs are used:

$$\mathcal{L}^{-1}\left\{\frac{n!}{(s-a)^{n+1}}\right\}=t^ne^{at}$$

$$\mathcal{L}^{-1}\left\{\frac{s-a}{(s-a)^2+\omega^2}\right\}=e^{at}\cos\omega t \qquad
\mathcal{L}^{-1}\left\{\frac{\omega}{(s-a)^2+\omega^2}\right\}=e^{at}\sin\omega t$$

$$\mathcal{L}^{-1}\left\{\frac{\omega}{(s-a)^2-\omega^2}\right\}=e^{at}\sinh\omega t \qquad
\mathcal{L}^{-1}\left\{\frac{s-a}{(s-a)^2-\omega^2}\right\}=e^{at}\cosh\omega t$$

$$\mathcal{L}^{-1}\left\{\frac{s^2-\omega^2}{(s^2+\omega^2)^2}\right\}=t\cos\omega t \qquad
\mathcal{L}^{-1}\left\{\frac{2\omega s}{(s^2+\omega^2)^2}\right\}=t\sin\omega t$$

---

## 1. Table lookup — complete the square / match the pattern

**a.** $\frac{3}{(s-7)^4}$
1. Write $3=\frac{3}{3!}\cdot 3! = \frac{1}{2}\cdot 3!$
2. So $\frac{3}{(s-7)^4}=\frac{1}{2}\cdot\frac{3!}{(s-7)^4}$, matching $n=3,\,a=7$.
3. **Result:** $\frac{t^3e^{7t}}{2}$

**b.** $\frac{2s-4}{s^2-4s+13}$
1. Complete the square: $s^2-4s+13=(s-2)^2+9$.
2. Write numerator in terms of $(s-2)$: $2s-4=2(s-2)$.
3. Match $\frac{s-a}{(s-a)^2+\omega^2}$ with $a=2,\ \omega=3$.
4. **Result:** $2e^{2t}\cos 3t$

**c.** $\frac{1}{s^2+4s+20}$
1. Complete the square: $s^2+4s+20=(s+2)^2+16$.
2. Write $1=\frac{1}{4}\cdot 4$ to get the $\omega$ in the numerator.
3. Match $\frac{\omega}{(s-a)^2+\omega^2}$ with $a=-2,\ \omega=4$.
4. **Result:** $\frac{e^{-2t}}{4}\sin 4t$

**d.** $\frac{2}{s^2+9}$
1. Write $2=\frac{2}{3}\cdot 3$.
2. Match $\frac{\omega}{s^2+\omega^2}$ with $\omega=3$.
3. **Result:** $\frac{2}{3}\sin 3t$

**e.** $\frac{s^2-1}{(s^2+1)^2}$
1. Recognize this is exactly the pattern $\frac{s^2-\omega^2}{(s^2+\omega^2)^2}$ with $\omega=1$.
2. **Result:** $t\cos t$

**f.** $\frac{1}{(s-2)^2-4}$
1. Write $1=\frac{1}{2}\cdot2$.
2. Match $\frac{\omega}{(s-a)^2-\omega^2}$ with $a=2,\ \omega=2$.
3. **Result:** $\frac{e^{2t}}{2}\sinh 2t$

**g.** $\frac{12s-24}{(s^2-4s+85)^2}$
1. Complete the square inside: $s^2-4s+85=(s-2)^2+81$, so $\omega=9,\ a=2$.
2. Write numerator as multiple of $(s-2)$: $12s-24=12(s-2)$.
3. The shifted $t\sin\omega t$ pattern needs $2\omega(s-a)=18(s-2)$ in the numerator; $12(s-2)=\frac{2}{3}\cdot18(s-2)$.
4. **Result:** $\frac{2te^{2t}}{3}\sin 9t$

**h.** $\frac{2}{(s-3)^2-9}$
1. Here $a=3,\ \omega=3$; write $2=\frac{2}{3}\cdot3$.
2. Match $\frac{\omega}{(s-a)^2-\omega^2}$.
3. **Result:** $\frac{2e^{3t}}{3}\sinh 3t$

**i.** $\frac{s^2-4s+3}{(s^2-4s+5)^2}$
1. Let $u=s-2$, so $s^2-4s+5=u^2+1$.
2. Rewrite the numerator using the denominator: $s^2-4s+3=(s^2-4s+5)-2=u^2-1$.
3. Now it's $\frac{u^2-1}{(u^2+1)^2}$, the same pattern as part (e) (with $\omega=1$), giving $t\cos t$ in the shifted variable.
4. Shifting back ($u=s-2\Rightarrow$ multiply by $e^{2t}$) gives **Result:** $e^{2t}\,t\cos t$

---

## 2. Theorem 8.2.1 (shift theorem) + table

**a.** $\frac{2s+3}{(s-7)^4}$
1. Let $u=s-7$; then $2s+3=2u+17$.
2. Split: $\frac{2u+17}{u^4}=\frac{2}{u^3}+\frac{17}{u^4}$.
3. Invert termwise using $1/u^{n+1}\to t^n e^{7t}/n!$: $\frac{2}{u^3}\to t^2e^{7t}$, $\frac{17}{u^4}\to\frac{17}{6}t^3e^{7t}$.
4. **Result:** $t^2e^{7t}+\frac{17}{6}t^3e^{7t}$

**b.** $\frac{s^2-1}{(s-2)^6}$
1. Let $u=s-2$; then $s^2-1=(u+2)^2-1=u^2+4u+3$.
2. Split: $\frac{1}{u^4}+\frac{4}{u^5}+\frac{3}{u^6}$.
3. Invert termwise (each $1/u^{n+1}\to t^n e^{2t}/n!$).
4. **Result:** $e^{2t}\left(\frac{1}{6} t^3+\frac{1}{6}t^4+\frac1{40}t^5\right)$

**c.** $\frac{s+5}{s^2+6s+18}$
1. Complete the square: $s^2+6s+18=(s+3)^2+9$.
2. Split numerator: $s+5=(s+3)+2$.
3. Invert each piece: $(s+3)/[(s+3)^2+9]\to e^{-3t}\cos3t$; $2/[(s+3)^2+9]=\frac{2}{3}\cdot3/[\cdots]\to\frac{2}{3}e^{-3t}\sin3t$.
4. **Result:** $e^{-3t}\left(\cos3t+\frac{2}{3}\sin3t\right)$

**d.** $\frac{2s+1}{s^2+9}$
1. Split: $\frac{2s}{s^2+9}+\frac{1}{s^2+9}$.
2. Invert each: $2\cos3t$ and $\frac{1}{3}\sin3t$.
3. **Result:** $2\cos3t+\frac{1}{3}\sin3t$

**e.** $\frac{s}{s^2+2s+1}=\frac{s}{(s+1)^2}$
1. Let $u=s+1$, so $s=u-1$: numerator becomes $u-1$.
2. Split: $\frac{1}{u}-\frac1{u^2}$.
3. Invert: $e^{-t}-te^{-t}$.
4. **Result:** $(1-t)e^{-t}$

**f.** $\frac{s+1}{s^2-9}$
1. Split: $\frac{s}{s^2-9}+\frac{1}{s^2-9}$.
2. Invert each using cosh/sinh pairs ($\omega=3$).
3. **Result:** $\cosh3t+\frac{1}{3}\sinh3t$

**g.** $\frac{s^3+2s^2-s-3}{(s+1)^4}$
1. Let $u=s+1$ ($s=u-1$); expand the numerator in powers of $u$: $s^3+2s^2-s-3=u^3-u^2-2u-1$.
2. Split over $u^4$: $\frac{1}{u}-\frac1{u^2}-\frac2{u^3}-\frac1{u^4}$.
3. Invert termwise with $a=-1$: $e^{-t}-te^{-t}-2\cdot\frac{t^2}{2}e^{-t}-\frac{t^3}{6}e^{-t}$.
4. **Result:** $\left(1-t-t^2-\frac{1}{6}t^3\right)e^{-t}$

**h.** $\frac{2s+3}{(s-1)^2+4}$
1. Let $u=s-1$: $2s+3=2u+5$.
2. Split: $\frac{2u}{u^2+4}+\frac{5}{u^2+4}=2\cdot\frac{u}{u^2+4}+\frac{5}{2}\cdot\frac{2}{u^2+4}$.
3. Invert with $a=1,\ \omega=2$: $2e^t\cos2t+\frac{5}{2}e^t\sin2t$.
4. **Result:** $e^t\left(2\cos2t+\frac{5}{2}\sin2t\right)$

**i.** $\frac{1}{s}-\frac{s}{s^2+1}$
1. Invert each term directly: $1/s\to1$, $s/(s^2+1)\to\cos t$.
2. **Result:** $1-\cos t$

**j.** $\frac{3s+4}{s^2-1}$
1. Split: $3\cdot\frac{s}{s^2-1}+4\cdot\frac{1}{s^2-1}$.
2. Invert: $3\cosh t+4\sinh t$.
3. **Result:** $3\cosh t+4\sinh t$

**k.** $\frac3{s-1}+\frac{4s+1}{s^2+9}$
1. First term $\to 3e^t$.
2. Second term splits into $4s/(s^2+9)\to4\cos3t$ and $1/(s^2+9)\to\frac{1}{3}\sin3t$.
3. **Result:** $3e^t+4\cos3t+\frac{1}{3}\sin3t$

**l.** $\frac3{(s+2)^2}-\frac{2s+6}{s^2+4}$
1. First term $\to 3te^{-2t}$.
2. Second: $2s/(s^2+4)\to2\cos2t$, $6/(s^2+4)=3\cdot2/(s^2+4)\to3\sin2t$; combine with minus sign.
3. **Result:** $3te^{-2t}-2\cos2t-3\sin2t$

---

## 3. Heaviside's cover-up method

**General recipe** for $F(s)=\frac{N(s)}{(s-r_1)(s-r_2)\cdots(s-r_n)}$ (distinct real roots):
1. Write $F(s)=\sum_k \frac{A_k}{s-r_k}$.
2. Find each $A_k$ by "covering up" $(s-r_k)$ and evaluating the rest at $s=r_k$: $A_k=\frac{N(r_k)}{\prod_{j\ne k}(r_k-r_j)}$.
3. Invert termwise: $\frac{A_k}{s-r_k}\to A_ke^{r_kt}$.

**a.** $\frac{3-(s+1)(s-2)}{(s+1)(s+2)(s-2)}$, poles $-1,-2,2$
Cover-up gives $A_{-1}=-1,\ A_{-2}=-\frac{1}{4},\ A_{2}=\frac{1}{4}$.
**Result:** $\frac{1}{4}e^{2t}-\frac{1}{4}e^{-2t}-e^{-t}$

**b.** $\frac{7+(s+4)(18-3s)}{(s-3)(s-1)(s+4)}$, poles $3,1,-4$
Cover-up gives $A_3=5,\ A_1=-\frac{41}{5},\ A_{-4}=\frac{1}{5}$.
**Result:** $\frac{1}{5}e^{-4t}-\frac{41}{5}e^{t}+5e^{3t}$

**c.** $\frac{2+(s-2)(3-2s)}{(s-2)(s+2)(s-3)}$, poles $2,-2,3$
Cover-up gives $A_2=-\frac{1}{2},\ A_{-2}=-\frac{13}{10},\ A_3=-\frac{1}{5}$.
**Result:** $-\frac{1}{2}e^{2t}-\frac{13}{10}e^{-2t}-\frac{1}{5}e^{3t}$

**d.** $\frac{3-(s-1)(s+1)}{(s+4)(s-2)(s-1)}$, poles $-4,2,1$
Cover-up gives $A_{-4}=-\frac{2}{5},\ A_2=0,\ A_1=-\frac{3}{5}$ (the $s=2$ term vanishes).
**Result:** $-\frac{2}{5}e^{-4t}-\frac{3}{5}e^{t}$

**e.** $\frac{3+(s-2)(10-2s-s^2)}{(s-2)(s+2)(s-1)(s+3)}$, poles $2,-2,1,-3$
Cover-up gives $A_2=\frac{3}{20},\ A_{-2}=-\frac{37}{12},\ A_1=\frac{1}{3},\ A_{-3}=\frac{8}{5}$.
**Result:** $\frac{3}{20}e^{2t}-\frac{37}{12}e^{-2t}+\frac{1}{3}e^{t}+\frac{8}{5}e^{-3t}$

**f.** $\frac{3+(s-3)(2s^2+s-21)}{(s-3)(s-1)(s+4)(s-2)}$, poles $3,1,-4,2$
Cover-up gives $A_3=\frac{3}{14},\ A_1=\frac{39}{10},\ A_{-4}=\frac{23}{105},\ A_2=-\frac{7}{3}$.
**Result:** $\frac{39}{10}e^{t}+\frac{3}{14}e^{3t}+\frac{23}{105}e^{-4t}-\frac{7}{3}e^{2t}$

---

## 4. Mixed simple, repeated, and irreducible-quadratic factors

**General recipe:** write $F(s)$ as a sum of terms — $\frac{A}{s-r}$ for each simple real pole, $\frac{A}{(s-r)^k}$ terms for repeated poles, and $\frac{Cs+D}{(s-p)^2+q^2}$ for each irreducible quadratic — then clear denominators, equate coefficients (or use cover-up for the real poles), and invert each piece.

**a.** $\frac{2+3s}{(s^2+1)(s+2)(s+1)}$
1. Form: $\frac{A}{s+2}+\frac{B}{s+1}+\frac{Cs+D}{s^2+1}$.
2. Cover-up gives $A=\frac{4}{5}$ (at $s=-2$), $B=-\frac{1}{2}$ (at $s=-1$); matching remaining coefficients gives $C=-\frac{3}{10},\ D=\frac{11}{10}$.
3. Invert: $\frac{Cs+D}{s^2+1}\to C\cos t+D\sin t$.
4. **Result:** $\frac{4}{5}e^{-2t}-\frac{1}{2}e^{-t}-\frac{3}{10}\cos t+\frac{11}{10}\sin t$

**b.** $\frac{3s^2+2s+1}{(s^2+1)(s^2+2s+2)}$
1. Complete the square in the second factor: $s^2+2s+2=(s+1)^2+1$.
2. Form: $\frac{As+B}{s^2+1}+\frac{C(s+1)+D}{(s+1)^2+1}$; equate coefficients to solve.
3. This yields $A=\frac{6}{5},\ B=\frac{2}{5},\ C=-\frac{6}{5},\ D=\frac{7}{5}$.
4. Invert each piece using cos/sin (undamped) and $e^{-t}\cos t,\ e^{-t}\sin t$ (damped).
5. **Result:** $\frac{2}{5}\sin t+\frac{6}{5}\cos t+\frac{7}{5}e^{-t}\sin t-\frac{6}{5}e^{-t}\cos t$

**c.** $\frac{3s+2}{(s-2)(s^2+2s+5)}$
1. Complete the square: $s^2+2s+5=(s+1)^2+4$.
2. Form: $\frac{A}{s-2}+\frac{B(s+1)+C}{(s+1)^2+4}$. Cover-up at $s=2$ gives $A=\frac{8}{13}$; matching remaining terms gives $B=-\frac{8}{13},\ C=\frac{15}{13}$ (so the sine coefficient after dividing by $\omega=2$ is $\frac{15}{26}$).
3. Invert: $Ae^{2t}$, $B e^{-t}\cos2t + \frac{C}{2}e^{-t}\sin2t$.
4. **Result:** $\frac{8}{13}e^{2t}-\frac{8}{13}e^{-t}\cos2t+\frac{15}{26}e^{-t}\sin2t$

**d.** $\frac{3s^2+2s+1}{(s-1)^2(s+2)(s+3)}$
1. Form: $\frac{A}{s-1}+\frac{B}{(s-1)^2}+\frac{C}{s+2}+\frac{D}{s+3}$.
2. Cover-up at the simple poles: $C=1$ (at $s=-2$), $D=-\frac{11}{8}$ (at $s=-3$); the repeated pole gives $B=\frac{3}{8}$ and, after differentiating, $A=\frac{1}{2}$.
3. Invert: $B/(s-1)^2\to Bte^t$, others $\to$ pure exponentials.
4. **Result:** $\frac{1}{2}te^{t}+\frac{3}{8}e^{t}+e^{-2t}-\frac{11}{8}e^{-3t}$

**e.** $\frac{2s^2+s+3}{(s-1)^2(s+2)^2}$
1. Form: $\frac{A}{s-1}+\frac{B}{(s-1)^2}+\frac{C}{s+2}+\frac{D}{(s+2)^2}$.
2. Evaluating at the double poles gives $B=\frac{1}{9},\ D=1$; matching remaining coefficients gives $A=\frac{2}{3},\ C=-\frac{1}{9}$.
3. Invert: repeated-pole terms $\to te^{at}$ factors, simple terms $\to$ exponentials.
4. **Result:** $\frac{2}{3}te^{t}+\frac{1}{9}e^{t}+te^{-2t}-\frac{1}{9}e^{-2t}$

**f.** $\frac{3s+2}{(s^2+1)(s-1)^2}$
1. Form: $\frac{As+B}{s^2+1}+\frac{C}{s-1}+\frac{D}{(s-1)^2}$.
2. At the double pole, $D=\frac{5}{2}$; matching remaining coefficients gives $A=1,\ B=-\frac{3}{2},\ C=-1$.
3. Invert: $\cos t,\sin t$ terms, plus $-e^t$ and $\frac{5}{2}te^t$.
4. **Result:** $-e^{t}+\frac{5}{2}te^{t}+\cos t-\frac{3}{2}\sin t$

---

## 5. Two undamped quadratics — undetermined coefficients (Example 8.2.9 method)

**General recipe** for $\frac{as+b}{(s^2+\alpha^2)(s^2+\beta^2)}$:
1. Write $\frac{as+b}{(s^2+\alpha^2)(s^2+\beta^2)}=\frac{Cs+D}{s^2+\alpha^2}+\frac{Es+F}{s^2+\beta^2}$.
2. Clear denominators and match coefficients of $s^3,s^2,s^1,s^0$ to solve for $C,D,E,F$.
3. Invert each piece as $C\cos\alpha t+\frac{D}{\alpha}\sin\alpha t+E\cos\beta t+\frac{F}{\beta}\sin\beta t$.

**a.** $\frac{3s+2}{(s^2+4)(s^2+9)}$ → coefficients solve to give
**Result:** $\frac{3}{5}\cos2t+\frac{1}{5}\sin2t-\frac{3}{5}\cos3t-\frac{2}{15}\sin3t$

**b.** $\frac{-4s+1}{(s^2+1)(s^2+16)}$ → 
**Result:** $-\frac{4}{15}\cos t+\frac{1}{15}\sin t+\frac{4}{15}\cos4t-\frac{1}{60}\sin4t$

**c.** $\frac{5s+3}{(s^2+1)(s^2+4)}$ →
**Result:** $\frac{5}{3}\cos t+\sin t-\frac{5}{3}\cos2t-\frac{1}{2}\sin2t$

**d.** $\frac{-s+1}{(4s^2+1)(s^2+1)}$
1. Rewrite $4s^2+1=4(s^2+\frac{1}{4})$ so $\alpha=\frac{1}{2}$; proceed as above.
**Result:** $-\frac{1}{3}\cos\frac{t}{2}+\frac{2}{3}\sin\frac{t}{2}+\frac{1}{3}\cos t-\frac{1}{3}\sin t$

**e.** $\frac{17s-34}{(s^2+16)(16s^2+1)}$
1. Rewrite $16s^2+1=16(s^2+\frac1{16})$ so $\beta=\frac{1}{4}$.
**Result:** $\frac{1}{15}\cos\frac{t}{4}-\frac{8}{15}\sin\frac{t}{4}-\frac{1}{15}\cos4t+\frac{1}{30}\sin4t$

**f.** $\frac{2s-1}{(4s^2+1)(9s^2+1)}$
1. Rewrite both factors as $4(s^2+\frac{1}{4})$ and $9(s^2+\frac{1}{9})$, so $\alpha=\frac{1}{2},\ \beta=\frac{1}{3}$.
**Result:** $\frac{2}{5}\cos\frac{t}{3}-\frac{3}{5}\sin\frac{t}{3}-\frac{2}{5}\cos\frac{t}{2}+\frac{2}{5}\sin\frac{t}{2}$

---

## 6. Two damped quadratics

**General recipe** for $\frac{as+b}{[(s-p_1)^2+q_1^2][(s-p_2)^2+q_2^2]}$: same undetermined-coefficient idea as Section 5, but with $\frac{C(s-p_1)+D}{(s-p_1)^2+q_1^2}+\frac{E(s-p_2)+F}{(s-p_2)^2+q_2^2}$, then invert with the damped cos/sin pairs.

**a.** $\frac{17s-15}{(s^2-2s+5)(s^2+2s+10)}$; centers $p_1=1,q_1=2$ and $p_2=-1,q_2=3$
**Result:** $e^{t}(\cos2t+\sin2t)-e^{-t}\left(\cos3t+\frac{4}{3}\sin3t\right)$

**b.** $\frac{8s+56}{(s^2-6s+13)(s^2+2s+5)}$; centers $p_1=3,q_1=2$ and $p_2=-1,q_2=2$
**Result:** $e^{3t}\left(-\cos2t+\frac{3}{2}\sin2t\right)+e^{-t}\left(\cos2t+\frac{1}{2}\sin2t\right)$

**c.** $\frac{s+9}{(s^2+4s+5)(s^2-4s+13)}$; centers $p_1=-2,q_1=1$ and $p_2=2,q_2=3$
**Result:** $e^{-2t}\left(\frac{1}{8}\cos t+\frac{1}{4}\sin t\right)-e^{2t}\left(\frac{1}{8}\cos3t-\frac1{12}\sin3t\right)$

**d.** $\frac{3s-2}{(s^2-4s+5)(s^2-6s+13)}$; centers $p_1=2,q_1=1$ and $p_2=3,q_2=2$
**Result:** $e^{2t}\left(\cos t+\frac{1}{2}\sin t\right)-e^{3t}\left(\cos2t-\frac{1}{4}\sin2t\right)$

**e.** $\frac{3s-1}{(s^2-2s+2)(s^2+2s+5)}$; centers $p_1=1,q_1=1$ and $p_2=-1,q_2=2$
**Result:** $e^{t}\left(\frac{1}{5}\cos t+\frac{2}{5}\sin t\right)-e^{-t}\left(\frac{1}{5}\cos2t+\frac{2}{5}\sin2t\right)$

**f.** $\frac{20s+40}{(4s^2-4s+5)(4s^2+4s+5)}$
1. Rewrite $4s^2\mp4s+5=4\left[(s\mp\frac{1}{2})^2+1\right]$, so both centers have $q=1,\ p=\pm\frac{1}{2}$.
**Result:** $e^{t/2}\left(-\cos t+\frac{9}{8}\sin t\right)+e^{-t/2}\left(\cos t-\frac{1}{8}\sin t\right)$

---

## 7. Simple pole + one quadratic

**General recipe** for $\frac{N(s)}{(s-r)[(s-p)^2+q^2]}$:
1. Cover-up at $s=r$ to get the coefficient $A$ of $\frac1{s-r}$.
2. Solve for the remaining $\frac{B(s-p)+C}{(s-p)^2+q^2}$ by matching coefficients.
3. Invert: $Ae^{rt}+Be^{pt}\cos qt+\frac{C}{q}e^{pt}\sin qt$.

**a.** $\frac{1}{s(s^2+1)}$
1. $\frac1{s(s^2+1)}=\frac{1}{s}-\frac{s}{s^2+1}$ (cover-up at $s=0$, then match).
**Result:** $1-\cos t$

**b.** $\frac1{(s-1)(s^2-2s+17)}$; center $p=1,q=4$
1. Cover-up at $s=1$: $A=\frac1{16}$.
**Result:** $\frac{e^{t}}{16}(1-\cos4t)$

**c.** $\frac{3s+2}{(s-2)(s^2+2s+10)}$; center $p=-1,q=3$
1. Cover-up at $s=2$: $A=\frac{4}{9}$.
**Result:** $\frac{4}{9}e^{2t}+\frac{5}{9}e^{-t}\sin3t-\frac{4}{9}e^{-t}\cos3t$

**d.** $\frac{34-17s}{(2s-1)(s^2-2s+5)}$
1. Factor out the 2: $2s-1=2(s-\frac{1}{2})$; cover-up at $s=\frac{1}{2}$.
**Result:** $3e^{t/2}-\frac{7}{2}e^{t}\sin2t-3e^{t}\cos2t$

**e.** $\frac{s+2}{(s-3)(s^2+2s+5)}$; center $p=-1,q=2$
1. Cover-up at $s=3$: $A=\frac{1}{4}$.
**Result:** $\frac{1}{4}e^{3t}-\frac{1}{4}e^{-t}\cos2t$

**f.** $\frac{2s-2}{(s-2)(s^2+2s+10)}$; center $p=-1,q=3$
1. Cover-up at $s=2$: $A=\frac{1}{9}$.
**Result:** $\frac{1}{9}e^{2t}-\frac{1}{9}e^{-t}\cos3t+\frac{5}{9}e^{-t}\sin3t$

---

## 8. Combinations of quadratics and simple poles

**a.** $\frac{2s+1}{(s^2+1)(s-1)(s-3)}$
1. Form $\frac{As+B}{s^2+1}+\frac{C}{s-1}+\frac{D}{s-3}$; cover-up at $s=1,3$ gives $C=-\frac{3}{4},\ D=\frac{7}{20}$; matching remaining terms gives $A,B$.
**Result:** $-\frac{3}{10}\sin t+\frac{2}{5}\cos t-\frac{3}{4}e^{t}+\frac{7}{20}e^{3t}$

**b.** $\frac{s+2}{(s^2+2s+2)(s^2-1)}$
1. Center for the quadratic: $p=-1,q=1$. Form $\frac{A(s+1)+B}{(s+1)^2+1}+\frac{C}{s-1}+\frac{D}{s+1}$; cover-up at $s=\pm1$ gives $C=\frac{3}{10},\ D=-\frac{1}{2}$.
**Result:** $-\frac{3}{5}e^{-t}\sin t+\frac{1}{5}e^{-t}\cos t-\frac{1}{2}e^{-t}+\frac{3}{10}e^{t}$

**c.** $\frac{2s-1}{(s^2-2s+2)(s+1)(s-2)}$
1. Center: $p=1,q=1$. Cover-up at $s=-1,2$ gives the exponential coefficients $\frac{1}{5},\frac{1}{2}$.
**Result:** $-\frac1{10}e^{t}\sin t-\frac{7}{10}e^{t}\cos t+\frac{1}{5}e^{-t}+\frac{1}{2}e^{2t}$

**d.** $\frac{s-6}{(s^2-1)(s^2+4)}$
1. Form $\frac{A}{s-1}+\frac{B}{s+1}+\frac{Cs+D}{s^2+4}$; cover-up at $s=\pm1$ gives $A=-\frac{1}{2},\ B=\frac{7}{10}$.
**Result:** $-\frac{1}{2}e^{t}+\frac{7}{10}e^{-t}-\frac{1}{5}\cos2t+\frac{3}{5}\sin2t$

**e.** $\frac{2s-3}{s(s-2)(s^2-2s+5)}$
1. Center: $p=1,q=2$. Cover-up at $s=0,2$ gives the constant term $\frac{3}{10}$ and $e^{2t}$ coefficient $\frac1{10}$.
**Result:** $\frac{3}{10}+\frac1{10}e^{2t}+\frac1{10}e^{t}\sin2t-\frac{2}{5}e^{t}\cos2t$

**f.** *(problem statement for part f is missing from the uploaded pages, but by the same method as above the answer key gives)*
**Result:** $-\frac{4}{9}e^{2t}\cos3t+\frac{1}{3}e^{2t}\sin3t-\frac{5}{9}e^{2t}+e^{t}$

---

## 9. General scaling property

If $\mathcal{L}\{f(t)\}=F(s)$, then to invert $F(as-b)$:
1. Write $as-b=a\left(s-\frac{b}{a}\right)$, so $F(as-b)=F\!\left(a\left(s-\frac{b}{a}\right)\right)$.
2. Apply the frequency-scaling and shift rules for the Laplace transform together.

$$\mathcal{L}^{-1}\{F(as-b)\}=\frac{1}{a}\,e^{\frac{b}{a}t}\,f\!\left(\frac{t}{a}\right)$$

---

*Source: LibreTexts, "8.2E: The Inverse Laplace Transform (Exercises)" — problems and answer key by William F. Trench, licensed CC BY-NC-SA 3.0. Step-by-step derivations added above.*
