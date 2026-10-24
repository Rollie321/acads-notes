
---

## title: "Skill-Assessment Exercise 6.1: Seventh-Order Routh Table" date: 2026-10-23 tags: [control-systems, stability, routh-hurwitz, nise, chapter-6, practice] aliases: [Skill Assessment 6.1, Seventh-order Routh table]

# Skill-Assessment Exercise 6.1: Seventh-Order Routh Table

> [!question] Problem Make a Routh table and tell how many roots of the polynomial are in the right half-plane (rhp) and in the left half-plane (lhp).
> 
> $$P(s)=3s^7+9s^6+6s^5+4s^4+7s^3+8s^2+2s+6$$

> [!success] Book answer **4 in the rhp, 3 in the lhp.**

**Source:** Nise, _Control Systems Engineering_, Chapter 6, Section 6.2. Builds on the method in [Example 6.1](https://claude.ai/chat/Example-6.1-Routh-Table.md).

---

## 1. Rule to apply

> [!important] Routh-Hurwitz criterion The number of roots in the right half-plane equals the number of **sign changes in the first column** of the Routh table.

Here the polynomial is given directly, so there is no closed-loop reduction step. We go straight to the table.

---

## 2. Coefficients

| $s^7$ | $s^6$ | $s^5$ | $s^4$ | $s^3$ | $s^2$ | $s^1$ | $s^0$ |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
|   3   |   9   |   6   |   4   |   7   |   8   |   2   |   6   |

All coefficients are positive and none is missing, so the quick test is inconclusive. We need the full table.

---

## 3. Set up the first two rows

- **Row $s^7$:** coefficients at positions 1, 3, 5, 7: $3,\ 6,\ 7,\ 2$
- **Row $s^6$:** coefficients at positions 2, 4, 6, 8: $9,\ 4,\ 8,\ 6$

|Row|Col 1|Col 2|Col 3|Col 4|
|:-:|:-:|:-:|:-:|:-:|
|$s^7$|3|6|7|2|
|$s^6$|9|4|8|6|

Every lower entry follows the same rule:

$$ \text{entry}=\frac{-\begin{vmatrix} a & b \ c & d \end{vmatrix}}{c}=\frac{bc-ad}{c} $$

where $a, b$ are from the row two above, and $c, d$ are from the row directly above (with $c$ in column 1).

---

## 4. Build each row

### Row $s^5$ (from rows $s^7$ and $s^6$, divisor $9$)

$$ \begin{aligned} \text{col 1:}&\quad \frac{-\begin{vmatrix} 3 & 6 \ 9 & 4 \end{vmatrix}}{9}=\frac{-(12-54)}{9}=\frac{42}{9}=\frac{14}{3}\[4pt] \text{col 2:}&\quad \frac{-\begin{vmatrix} 3 & 7 \ 9 & 8 \end{vmatrix}}{9}=\frac{-(24-63)}{9}=\frac{39}{9}=\frac{13}{3}\[4pt] \text{col 3:}&\quad \frac{-\begin{vmatrix} 3 & 2 \ 9 & 6 \end{vmatrix}}{9}=\frac{-(18-18)}{9}=0 \end{aligned} $$

Multiply the row by $3$ for convenience: $[14,\ 13,\ 0]$.

### Row $s^4$ (from rows $s^6$ and $s^5$, divisor $14$)

$$ \begin{aligned} \text{col 1:}&\quad \frac{-\begin{vmatrix} 9 & 4 \ 14 & 13 \end{vmatrix}}{14}=\frac{-(117-56)}{14}=-\frac{61}{14}\[4pt] \text{col 2:}&\quad \frac{-\begin{vmatrix} 9 & 8 \ 14 & 0 \end{vmatrix}}{14}=\frac{-(0-112)}{14}=8\[4pt] \text{col 3:}&\quad \frac{-\begin{vmatrix} 9 & 6 \ 14 & 0 \end{vmatrix}}{14}=\frac{-(0-84)}{14}=6 \end{aligned} $$

Multiply by $14$: $[-61,\ 112,\ 84]$.

### Row $s^3$ (from rows $s^5$ and $s^4$, divisor $-61$)

$$ \begin{aligned} \text{col 1:}&\quad \frac{-\begin{vmatrix} 14 & 13 \ -61 & 112 \end{vmatrix}}{-61}=\frac{-(1568+793)}{-61}=\frac{2361}{61}\approx 38.7\[4pt] \text{col 2:}&\quad \frac{-\begin{vmatrix} 14 & 0 \ -61 & 84 \end{vmatrix}}{-61}=\frac{-(1176-0)}{-61}=\frac{1176}{61}\approx 19.3 \end{aligned} $$

Multiply by $\tfrac{61}{3}$: $[787,\ 392]$.

### Row $s^2$ (from rows $s^4$ and $s^3$, divisor $787$)

$$ \begin{aligned} \text{col 1:}&\quad \frac{-\begin{vmatrix} -61 & 112 \ 787 & 392 \end{vmatrix}}{787}=\frac{-(-23912-88144)}{787}=\frac{112056}{787}\approx 142.4\[4pt] \text{col 2:}&\quad \frac{-\begin{vmatrix} -61 & 84 \ 787 & 0 \end{vmatrix}}{787}=\frac{-(0-66108)}{787}=84 \end{aligned} $$

### Row $s^1$ (from rows $s^3$ and $s^2$, divisor $142.38$)

$$ \text{col 1:}\quad \frac{-\begin{vmatrix} 787 & 392 \ 142.38 & 84 \end{vmatrix}}{142.38}=\frac{-(66108-55814)}{142.38}\approx -72.3 $$

### Row $s^0$ (from rows $s^2$ and $s^1$, divisor $-72.3$)

$$ \text{col 1:}\quad \frac{-\begin{vmatrix} 142.4 & 84 \ -72.3 & 0 \end{vmatrix}}{-72.3}=\frac{-(0+6073)}{-72.3}=84 $$

> [!note] About row scaling Multiplying a row by a **positive** constant rescales the numbers below it but **never changes their signs**. Since the answer depends only on signs in the first column, the sign-change count is unaffected. Never scale by a negative constant.

---

## 5. Completed Routh table

|  Row  |   Col 1   | Col 2 | Col 3 | Col 4 |
| :---: | :-------: | :---: | :---: | :---: |
| $s^7$ |   **3**   |   6   |   7   |   2   |
| $s^6$ |   **9**   |   4   |   8   |   6   |
| $s^5$ |  **14**   |  13   |   0   |       |
| $s^4$ |  **−61**  |  112  |  84   |       |
| $s^3$ |  **787**  |  392  |       |       |
| $s^2$ | **142.4** |  84   |       |       |
| $s^1$ | **−72.3** |   0   |       |       |
| $s^0$ |  **84**   |       |       |       |

---

## 6. Interpret the first column

First column: $3,\ 9,\ 14,\ -61,\ 787,\ 142.4,\ -72.3,\ 84$

| Transition    |   Signs   |   Change?   |
| :------------ | :-------: | :---------: |
| $s^7 \to s^6$ | $+ \to +$ |     no      |
| $s^6 \to s^5$ | $+ \to +$ |     no      |
| $s^5 \to s^4$ | $+ \to -$ | **yes (1)** |
| $s^4 \to s^3$ | $- \to +$ | **yes (2)** |
| $s^3 \to s^2$ | $+ \to +$ |     no      |
| $s^2 \to s^1$ | $+ \to -$ | **yes (3)** |
| $s^1 \to s^0$ | $- \to +$ | **yes (4)** |

$$\text{4 sign changes};\Rightarrow;\text{4 roots in the rhp}$$

The polynomial is seventh order and no row of zeros appeared, so there are no $j\omega$-axis roots:

$$7-4=3\ \text{roots in the lhp}$$

> [!success] Answer **4 roots in the right half-plane and 3 roots in the left half-plane**, matching the book. The system is **unstable**.

---

## 7. Check yourself

- Quick numeric check in MATLAB: `roots([3 9 6 4 7 8 2 6])`, then count roots with positive real part.
- Arithmetic slip hints: the $s^4$ entry must be negative, and the $s^0$ entry should equal the last nonzero entry of the $s^2$ row (84 here).

---

## 8. Takeaways

> [!abstract] Method recap
> 
> 1. Fill rows 1 and 2 with alternating coefficients.
> 2. Each new entry is $\dfrac{bc-ad}{c}$ using the two rows above.
> 3. Scale rows by **positive** constants to keep numbers manageable.
> 4. Count sign changes in column 1; that count is the number of rhp roots.
> 5. Remaining roots (if no zero row appears) are in the lhp.

Related: [Example 6.1](https://claude.ai/chat/Example-6.1-Routh-Table.md), Nise Chapter 6 Section 6.2