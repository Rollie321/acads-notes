
## tags: [reviewer, cpe07, boolean-algebra, karnaugh-map, midterm] aliases: [CPE07 Reviewer, Boolean Logic Reviewer]

# CPE07 – Boolean Algebra and Karnaugh Map Reviewer

> [!info] Coverage Lecture 04 (Boolean Algebra), Lecture 05 (Karnaugh Map) · _Digital Electronics 1: Logic Circuits and Switching Theory_

**Jump to:**
- [[#1. Boolean Algebra]]
- [[#2. Karnaugh Map]]
- [[#Formula Sheet]]
- [[#Practice Questions]]
---

## 1. Boolean Algebra

- Named after **George Boole** (1815–1864)
- Algebra of logic, now applied to computer systems and devices; rules are based on **human reasoning**
- Stayed in the realm of philosophy until **1938**, when **Claude E. Shannon** used it to solve **relay logic** problems
- Advantages over other tools: **simplicity, speed, accuracy**

### Laws of Boolean Algebra (26 laws)

**OR laws**

| Law | Rule                   |
| :-- | :--------------------- |
| 1   | $A + 0 = A$            |
| 2   | $A + 1 = 1$            |
| 3   | $A + A = A$            |
| 4   | $A + \overline{A} = 1$ |

**AND laws**

| Law | Rule                       |
| :-- | :------------------------- |
| 5   | $A \cdot 0 = 0$            |
| 6   | $A \cdot 1 = A$            |
| 7   | $A \cdot A = A$            |
| 8   | $A \cdot \overline{A} = 0$ |

**Laws of complementation**

| Law | Rule                                              |
| :-- | :------------------------------------------------ |
| 9   | $\overline{0} = 1$                                |
| 10  | $\overline{1} = 0$                                |
| 11  | if $A = 0$, then $\overline{A} = 1$               |
| 12  | if $A = 1$, then $\overline{A} = 0$               |
| 13  | $\overline{\overline{A}} = A$ (double complement) |

**Commutative laws** — _change in the position of variables_

| Law | Rule                    |
| :-- | :---------------------- |
| 14  | $A + B = B + A$         |
| 15  | $A \cdot B = B \cdot A$ |

**Associative laws** — _removal of brackets / regrouping_

| Law | Rule                                |
| :-- | :---------------------------------- |
| 16  | $A + (B + C) = (A + B) + C$         |
| 17  | $(A + B) + (C + D) = A + B + C + D$ |
| 18  | $A(BC) = (AB)C$                     |

**Distributive laws** — _factoring or multiplying out_

| Law | Rule                        |
| :-- | :-------------------------- |
| 19  | $A(B + C) = AB + AC$        |
| 20  | $A + BC = (A + B)(A + C)$   |
| 21  | $A + \overline{A}B = A + B$ |

**Absorptive laws** — _absorb terms into existing terms_

| Law | Rule                       |
| :-- | :------------------------- |
| 22  | $A + AB = A$               |
| 23  | $A(A + B) = A$             |
| 24  | $A(\overline{A} + B) = AB$ |

**De Morgan's Theorem**

| Law | Rule                                                 |
| :-- | :--------------------------------------------------- |
| 25  | $\overline{A + B} = \overline{A}\cdot\overline{B}$   |
| 26  | $\overline{A \cdot B} = \overline{A} + \overline{B}$ |

> [!tip] De Morgan in one line **Break the bar, change the sign** (AND ↔ OR), and keep the complements on the individual variables.

### Designing logic circuits with Boolean algebra

- **SOP – Sum of Products** → AND terms that are ORed together
    - e.g., $\overline A,\overline B + \overline A BC$
- **POS – Product of Sums** → OR terms that are ANDed together
    - e.g., $(A + \overline B)(\overline A + B + C)$

### Truth table → Boolean expression

- **Minterm** → used for the rows where output $X = 1$ (builds the **SOP**)
    - Write it as **products** (AND)
    - Variables with **0 input** get the **NOT operation**
- **Maxterm** → used for the rows where output $X = 0$ (builds the **POS**)
    - Write it as **sums** (OR)
    - Variables with **1 input** get the **NOT operation**

> [!warning] Notation trap Write a minterm like $\overline A,\overline B C$, **not** $\overline{AB}C$. $\overline{AB} = \overline A + \overline B$ (De Morgan), which is not the same as $\overline A,\overline B$.

---

### Problems


> [!question]- Example 1: Prove $A+\overline A.B=A+B$
>
Apply the **distributive law** (Law 20):
$$ {\color{yellow}A + \overline A.B} = A + B $$ $$ {\color{yellow}(A+\overline A) . (A+B)} = A + B $$
>
Apply the **OR law** (Law 4): $$ {\color{yellow}(A+\overline A)} . (A+B) = A + B $$ $$ {\color{yellow}1}. (A+B) = A + B $$
>
Apply the **AND law** (Law 6): $$ {\color{yellow}1. (A+B)} = A + B $$ $$ {\color{yellow}(A+B)} = A + B $$ $$ A+B = A+B $$
>

> [!question]- Example 2: Prove $A+AB=A$
>
> Factor out the **A**: $$ {\color{yellow}A}+{\color{yellow}A}B = A $$ $$ {\color{yellow}A}(1+B)=A $$
>
>Apply the **OR law** (Law 2): $$ A({\color{yellow}1+B})=A $$ $$ A.{\color{yellow}1} = A $$
>
>Apply the **AND law** (Law 6): $$ {\color{yellow}A.1} = A $$ $$ A = A $$

> [!question]- Example 3: Demorganize $\overline {A,\overline{(B+\overline C)},D}$
>
>Apply **De Morgan's theorem** (break the long bar): $$ \overline {\color{yellow}A,\overline{(B+\overline C)},D} $$ $$ {\color{yellow}\overline A + \overline{\overline{(B+\overline C)}} + \overline D} $$
>
>Apply the **double complement** (Law 13): $$ \overline A + {\color{yellow}\overline{\overline{(B+\overline C)}}} + \overline D $$ $$ \overline A + {\color{yellow}(B+\overline C)} + \overline D $$
>
>Remove the brackets (**associative law**): $$ \overline A + B + \overline C + \overline D $$ $$ \boxed{\overline A + B + \overline C + \overline D} $$

> [!question]- Example 4: Determine the output X
>
> ![[Pasted image 20260910101802.png]] $$ (A\oplus B)(A+\overline B) $$
>
> |A|B|B'|A⊕B|B'+A|X|
> |:--|---|---|---|---|---|
> |0|0|1|0|1|0|
> |0|1|0|1|0|0|
> |1|0|1|1|1|1|
> |1|1|0|0|1|0|
>
>$X = 1$ only when $A = 1$ and $B = 0$, so $X = A\overline B$ (confirmed algebraically in Example 6).

> [!question]- Example 5: Determine the Boolean expression, then simplify
>
> ![[Pasted image 20260910102950.png]]
> 
> Expression: $$\overline {((AB) +\overline C)}(BC)$$
> 
> Simplify:
> 
> Apply **De Morgan's theorem** to the first term: $$ \overline {\color{yellow}{(AB +\overline C)}}.BC $$ $$ {\color{yellow} \overline{AB}. \overline {\overline C}}.BC $$
> 
> Apply the **double complement** (Law 13): $$ \overline{AB}.{\color{yellow}\overline{\overline C}}.BC $$ $$ \overline{AB}.{\color{yellow}C}.BC $$
>
> Apply **De Morgan's theorem** again: $$ {\color{yellow}\overline{AB}}.C.BC $$ $$ {\color{yellow}(\overline A + \overline B)}.C.B.C $$
> 
> Apply the **AND law** (Law 7): $$ (\overline A + \overline B).B.{\color{yellow}C.C} $$ $$ (\overline A + \overline B).B.{\color{yellow}C} $$
> 
> Apply the **distributive law** (Law 19): $$ {\color{yellow}(\overline A + \overline B).B}.C $$ $$ {\color{yellow}(\overline AB + \overline BB)}.C $$
> 
> Apply the **AND law** (Law 8): $$ (\overline AB + {\color{yellow}\overline BB}).C $$ $$ (\overline AB + {\color{yellow}0}).C $$
> 
> Apply the **OR law** (Law 1): $$ {\color{yellow}(\overline AB + 0)}.C $$ $$ {\color{yellow}(\overline AB)}.C $$ $$ \overline ABC $$
>
> $$ \boxed{\overline {((AB) +\overline C)}(BC) = \overline ABC} $$

> [!question]- Example 6: Determine the output, then simplify
>
> ![[logic-diagram-2.png]]
>
> This circuit can be written as: $$ (A \oplus B)(A+\overline B) $$
> 
> The $\oplus$ operator can be rewritten as a sum of products: $$A\oplus B = \overline AB + A\overline B$$
> 
> Hence, we can rewrite the circuit as: $$ ({\color{yellow}A \oplus B})(A+\overline B) = ({\color{yellow}\overline AB + A\overline B})(A+\overline B) $$ $$ \boxed{(\overline AB + A\overline B)(A+\overline B)} $$
> 
> Multiply the terms using the **FOIL** method: $$ \overline AB.A + \overline AB.\overline B + A\overline B.A + A\overline B.\overline B $$ $$ A\overline AB + \overline AB\overline B + AA\overline B + A\overline B,\overline B $$
> 
> Apply the **AND laws**:
> 
> |Law|Rule|
> |:--|---|
> |Law 8|$A\overline A = 0$ and $B\overline B = 0$|
> |Law 7|$AA = A$ and $\overline B,\overline B = \overline B$|
> 
> $$ {\color{red}A\overline A}B + \overline A{\color{red} B\overline B} + {\color{yellow}AA}\overline B + A{\color{yellow}\overline B,\overline B} $$ $$ {\color{red}0}B + \overline A{\color{red} 0} + {\color{yellow}A}\overline B + A{\color{yellow}\overline B} $$
> 
> Anything multiplied by zero is cancelled out (Law 5), then apply the **OR law** (Law 3): $$ {\color{red}0B} + {\color{red}\overline A 0} + {\color{yellow}A\overline B}+ {\color{yellow}A\overline B} $$ $$ A\overline B + A \overline B = A\overline B $$
>
> Now we have the simplified circuit: $$ \boxed{A\overline B} $$

> [!question]- Example 7: Find the equation from the given truth table
>
> Write the **minterm** and **maxterm** of each row:
>
> |A|B|C|X|Minterm|Maxterm|
> |:--|---|---|---|---|---|
> |**0**|0|0|0|$\overline A,\overline B,\overline C$|$A + B + C$|
> |0|0|1|1|$\overline A,\overline B C$|$A + B + \overline C$|
> |0|1|0|0|$\overline AB\overline C$|$A + \overline B + C$|
> |0|1|1|0|$\overline A BC$|$A + \overline B + \overline C$|
> |1|0|0|1|$A\overline B,\overline C$|$\overline A + B + C$|
> |1|0|1|0|$A\overline B C$|$\overline A + B + \overline C$|
> |1|1|0|0|$AB\overline C$|$\overline A + \overline B + C$|
> |1|1|1|1|$ABC$|$\overline A + \overline B + \overline C$|
> 
> Use only the minterms where $X = 1$ and the maxterms where $X = 0$.
> 
> SOP (sum of products), rows where $X = 1$:
>
>$$ \overline A,\overline B C + A\overline B,\overline C + ABC $$
> 
> ##### POS (product of sums), rows where $X=0$:
>
> $$ (A+B+C)(A+\overline B+C)(A+\overline B+\overline C)(\overline A+B+\overline C)(\overline A+\overline B+C) $$
>
>##### Simplify?
>
>The three 1s (001, 100, 111) each differ from one another by **two** variables, so no two terms can be combined. The SOP is already in its simplest form: $$ \boxed{X = \overline A,\overline B C + A\overline B,\overline C + ABC} $$
>


> [!question]- Example 8: Obtain the simplified Boolean expression
>
>|A|B|C|X|
>|:--|---|---|---|
>|0|0|0|0|
>|0|0|1|1|
>|0|1|0|0|
>|0|1|1|1|
>|1|0|0|0|
>|1|0|1|0|
>|1|1|0|1|
>|1|1|1|1|
>
>##### Step 1: Find the minterm and maxterm
>
>|A|B|C|X|Minterm|Maxterm|
>|:--|---|---|---|---|---|
>|0|0|0|0||$A+B+C$|
>|0|0|1|1|$\overline A,\overline B C$||
>|0|1|0|0||$A+\overline B+C$|
>|0|1|1|1|$\overline ABC$||
>|1|0|0|0||$\overline A+B+C$|
>|1|0|1|0||$\overline A+B+\overline C$|
>|1|1|0|1|$AB\overline C$||
>|1|1|1|1|$ABC$||
>
>##### Step 2A: Find the SOP
>
>$$(\overline A,\overline B C)+(\overline ABC)+(AB\overline C)+(ABC)$$
>
>##### Step 2B: Find the POS
>
>$$ (A+B+C)(A+\overline B+C)(\overline A+B+C)(\overline A+B+\overline C) $$
>
>##### Step 3A: Simplify the SOP
>
>$$(\overline A,\overline B C)+(\overline ABC)+(AB\overline C)+(ABC)$$
>
>Factor out the common terms: $$ {\color{yellow}\overline AC}(\overline B + B) + {\color{yellowgreen}AB}(\overline C+C) $$
>
>Apply the **OR law** (Law 4): $$ \overline AC({\color{yellow}\overline B + B}) + AB({\color{yellowgreen}\overline C+C}) $$ $$ \overline AC({\color{yellow}1}) + AB({\color{yellowgreen}1}) $$
>
>Apply the **AND law** (Law 6): $$ \boxed{\overline AC + AB} $$
>
>##### Step 3B: Simplify the POS
>
>$$ {\color{yellow}(A+B+C)(A+\overline B+C)}{\color{yellowgreen}(\overline A+B+C)(\overline A+B+\overline C)} $$
>
>Apply the **distributive law** (Law 20): $$ {\color{yellow}(A+C)(B+\overline B)}{\color{yellowgreen}(\overline A + B)(\overline C+C)} $$
>
>Apply the **OR law** (Law 4) and the **AND law** (Law 6): $$ (A+C)\cancel{(B+\overline B)}(\overline A + B)\cancel{(\overline C+C)} $$ $$ (A+C)(\overline A + B) $$
>
>Multiply using the **FOIL** method: $$ A \overline A + AB + \overline AC + BC $$
>
>Apply the **AND law** (Law 8): $$ {\color{red}A \overline A} + AB + \overline AC + BC $$ $$ AB + \overline AC + BC $$
>
>Multiply by an imaginary 1: $$ AB + \overline AC + BC{\color{yellow}(1)} $$
>
>Use the **OR law** in reverse (Law 4): $$ AB + \overline AC + BC{\color{yellow}(A+\overline A)} $$
>
>Distribute: $$ AB + \overline AC + ABC + \overline ABC $$
>
>Rearrange, then factor out: $$ {\color{yellow}AB} + {\color{yellow}AB}C + {\color{yellowgreen}\overline AC}+{\color{yellowgreen}\overline AC}B $$ $$ {\color{yellow}AB}(1+C)+{\color{yellowgreen}\overline AC}(1 + B) $$
>
>Apply the **OR law** (Law 2): $$ AB(\cancel{1+C})+\overline AC(\cancel{1 + B}) $$ $$ \boxed{AB + \overline AC} $$
>
>##### Check: the answers from the SOP and POS should be equal
>
>$$POS = SOP$$ $$\boxed{AB+\overline AC = AB +\overline AC}$$

> [!question]- Example 9: Prove $AC + ABC = AC$ _(lecture)_
> 
> Factor out the **AC**: $$ {\color{yellow}AC}+{\color{yellow}AC}B = AC $$ $$ {\color{yellow}AC}(1+B)=AC $$
> 
> Apply the **OR law** (Law 2): $$ AC({\color{yellow}1+B})=AC $$ $$ AC.{\color{yellow}1} = AC $$
> 
> Apply the **AND law** (Law 6): $$ {\color{yellow}AC.1} = AC $$ $$ AC = AC $$

> [!question]- Example 10: Prove $ABC + A\overline BC + AB\overline C = A(B+C)$ _(lecture)_
> 
> Group the first two terms and factor out **AC**: $$ {\color{yellow}ABC + A\overline BC} + AB\overline C = A(B+C) $$ $$ {\color{yellow}AC(B+\overline B)} + AB\overline C = A(B+C) $$
> 
> Apply the **OR law** (Law 4) and the **AND law** (Law 6): $$ AC({\color{yellow}B+\overline B}) + AB\overline C = A(B+C) $$ $$ AC.{\color{yellow}1} + AB\overline C = A(B+C) $$ $$ AC + AB\overline C = A(B+C) $$
> 
> Factor out the **A**: $$ {\color{yellow}A}C + {\color{yellow}A}B\overline C = A(B+C) $$ $$ A(C + B\overline C) = A(B+C) $$
> 
> Apply the **distributive law** (Law 21): $$ A({\color{yellow}C + \overline C B}) = A(B+C) $$ $$ A({\color{yellow}C + B}) = A(B+C) $$
> 
> Apply the **commutative law** (Law 14): $$ A(C+B) = A(B+C) $$ $$ A(B+C) = A(B+C) $$

> [!question]- Example 11: Demorganize $\overline{(A+B)(C+D)}$ _(lecture)_
>
>Apply **De Morgan's theorem** (break the long bar): $$ {\color{yellow}\overline{(A+B)} + \overline{(C+D)}} $$
>
>Apply **De Morgan's theorem** (break the short bars): $$ {\color{yellow}\overline{(A+B)}} + {\color{yellowgreen}\overline{(C+D)}} $$ $$ \boxed{\overline A,\overline B + \overline C,\overline D} $$

---

## 2. Karnaugh Map

- **K-map** = similar to a truth table (shows all input combinations and outputs) but arranged as an **array of squares (cells)**, each cell = one binary combination of inputs
- An **alternative to Boolean-algebra simplification**: transfer values from a Boolean statement or truth table into the map
- Practical for **2, 3, or 4 variables**
- **Number of cells = $2^n$** (n = number of variables)
    - 2 variables → $2^2 = 4$ cells
    - 3 variables → $2^3 = 8$ cells
    - 4 variables → $2^4 = 16$ cells

### Gray code ordering

- Row/column labels go **00, 01, 11, 10** (never 00, 01, 10, 11) so neighbors differ by **only one variable**

**3-variable map** (rows = AB, columns = C)

|AB \ C|0|1|
|:-:|:-:|:-:|
|**00**|m0|m1|
|**01**|m2|m3|
|**11**|m6|m7|
|**10**|m4|m5|

**4-variable map** (rows = AB, columns = CD)

|AB \ CD|00|01|11|10|
|:-:|:-:|:-:|:-:|:-:|
|**00**|m0|m1|m3|m2|
|**01**|m4|m5|m7|m6|
|**11**|m12|m13|m15|m14|
|**10**|m8|m9|m11|m10|

### Square adjacency

- **Adjacent = differ by exactly one variable**
- Each square is adjacent to the squares **immediately next to it on its 4 sides**
- **NOT adjacent diagonally**
- **Wrap-around adjacency:** top row ↔ bottom row, left column ↔ right column

### Simplification procedure

- **Step 1 — Group the 1s** (aim: _biggest groups, fewest groups_)
    - a) A group must contain **1, 2, 4, 8, or 16** squares (powers of 2)
    - b) Each square in a group must be adjacent to one or more squares in the group (groups are rectangles)
    - c) Always include the **largest possible** number of 1s in a group
    - d) **Every 1 must be in at least one group** (groups may overlap)
- **Step 2 — Determine the product term of each group**
    - Variables that **stay the same** across the group → keep them (uncomplemented if 1, complemented if 0)
    - Variables that **change** inside the group → **eliminate** them
- **Step 3 — Sum (OR) the resulting products**

> [!tip] Bigger group = fewer variables Group of 2 eliminates 1 variable · group of 4 eliminates 2 · group of 8 eliminates 3.

### 2-variable example: OR gate

|A|B|Y|
|:-:|:-:|:-:|
|0|0|0|
|0|1|1|
|1|0|1|
|1|1|1|

|B \ A|0|1|
|:-:|:-:|:-:|
|**0**|0|1|
|**1**|1|1|

- Group column A=1 (two 1s) → $A$
- Group row B=1 (two 1s) → $B$
- **Y = A + B**

### Worked examples: from Boolean expressions

> [!example]- K-map Ex 1: $X = \overline{A}B + \overline{A},\overline{B}C + AB\overline{C} + A\overline{B},\overline{C}$ Expand $\overline{A}B$ → 010, 011. Other terms → 001, 110, 100.
> 
> |AB \ C|0|1|
> |:-:|:-:|:-:|
> |**00**|0|1|
> |**01**|1|1|
> |**11**|1|0|
> |**10**|1|0|
> 
> Groups (pairs): $\overline{A}C$ (001, 011) · $\overline{A}B$ (010, 011) · $A\overline{C}$ (110, 100) **$X = \overline{A}B + \overline{A}C + A\overline{C}$** _(equivalent minimal answer: $\overline{A}C + A\overline{C} + B\overline{C}$)_

> [!example]- K-map Ex 2: $X = \overline{A}B\overline{C}D + A\overline{B},\overline{C}D + \overline{A},\overline{B},\overline{C}D + AB\overline{C}D + AB\overline{C},\overline{D} + ABCD$ Minterms: 5, 9, 1, 13, 12, 15
> 
> |AB \ CD|00|01|11|10|
> |:-:|:-:|:-:|:-:|:-:|
> |**00**|0|1|0|0|
> |**01**|0|1|0|0|
> |**11**|1|1|1|0|
> |**10**|0|1|0|0|
> 
> Groups: **quad** of the whole CD=01 column → $\overline{C}D$ · pair 12, 13 → $AB\overline{C}$ · pair 13, 15 → $ABD$ **$X = \overline{C}D + AB\overline{C} + ABD$**

> [!example]- K-map Ex 3: $X = B\overline{C},\overline{D} + \overline{A}B\overline{C}D + AB\overline{C}D + \overline{A}BCD + ABCD$ _(overbars read from the slide image; double-check against your copy)_ Minterms: 4, 12, 5, 13, 7, 15
> 
> |AB \ CD|00|01|11|10|
> |:-:|:-:|:-:|:-:|:-:|
> |**00**|0|0|0|0|
> |**01**|1|1|1|0|
> |**11**|1|1|1|0|
> |**10**|0|0|0|0|
> 
> Groups: quad (4, 5, 12, 13) → $B\overline{C}$ · quad (5, 7, 13, 15) → $BD$ **$X = B\overline{C} + BD = B(\overline{C} + D)$**

### Worked examples: from truth tables

> [!example]- Example 1 (A B C → Y): 1s at 000, 001, 100, 101, 110
> 
> |AB \ C|0|1|
> |:-:|:-:|:-:|
> |**00**|1|1|
> |**01**|0|0|
> |**11**|1|0|
> |**10**|1|1|
> 
> Quad (000, 001, 100, 101, wraps rows 00 and 10) → $\overline{B}$ · Pair (110, 100) → $A\overline{C}$ **$Y = \overline{B} + A\overline{C}$**

> [!example]- Example 2 (x y z → F): 1s at 000, 010, 100, 101, 110
> 
> |xy \ z|0|1|
> |:-:|:-:|:-:|
> |**00**|1|0|
> |**01**|1|0|
> |**11**|1|0|
> |**10**|1|1|
> 
> Quad (whole z=0 column) → $\overline{z}$ · Pair (100, 101) → $x\overline{y}$ **$F = \overline{z} + x\overline{y}$**

> [!example]- Example 3 (w x y z → F): 1s at m = 0, 1, 2, 4, 5, 6, 8, 9, 12, 13, 14
> 
> |wx \ yz|00|01|11|10|
> |:-:|:-:|:-:|:-:|:-:|
> |**00**|1|1|0|1|
> |**01**|1|1|0|1|
> |**11**|1|1|0|1|
> |**10**|1|1|0|0|
> 
> Octet (columns yz=00 and 01, all rows) → $\overline{y}$ Quad (rows 00, 01 × columns 00, 10 via wrap-around: m0, 2, 4, 6) → $\overline{w},\overline{z}$ Quad (rows 01, 11 × columns 00, 10: m4, 6, 12, 14) → $x\overline{z}$ **$F = \overline{y} + \overline{w},\overline{z} + x\overline{z}$**

> [!example]- Example 4 (w x y z → F): 1s at m = 0, 1, 2, 6, 8, 9, 10
> 
> |wx \ yz|00|01|11|10|
> |:-:|:-:|:-:|:-:|:-:|
> |**00**|1|1|0|1|
> |**01**|0|0|0|1|
> |**11**|0|0|0|0|
> |**10**|1|1|0|1|
> 
> Quad (m0, 1, 8, 9) → $\overline{x},\overline{y}$ Quad, four corners (m0, 2, 8, 10) → $\overline{x},\overline{z}$ Pair (m2, 6) → $\overline{w}y\overline{z}$ **$F = \overline{x},\overline{y} + \overline{x},\overline{z} + \overline{w}y\overline{z}$**

---

# Formula Sheet

| Topic             | Formula                                                                                                |
| :---------------- | :----------------------------------------------------------------------------------------------------- |
| K-map cells       | $2^n$                                                                                                  |
| De Morgan         | $\overline{A+B} = \overline{A}.\overline{B}$<br/>$\overline{AB} = \overline{A}+\overline{B}$           |
| Boolean basics    | $A+0=A$<br/>$A+1=1$<br/>$A+\overline{A}=1$<br/>$A\cdot0=0$ <br/>$A\cdot1=A$<br/>$A\cdot\overline{A}=0$ |
| Useful identities | $A+\overline{A}B=A+B$<br/>$A+AB=A$<br/>$A(\overline{A}+B)=AB$                                          |
| SOP / POS         | SOP = ORed products (minterms, output 1)<br/>POS = ANDed sums (maxterms, output 0)                     |

---

# Practice Questions

> [!question]- 1. Simplify $A + \overline{A}B$ and name the law. $A + B$ (Law 21).

> [!question]- 2. Simplify $A(\overline{A} + B)$. $A\overline{A} + AB = 0 + AB = AB$ (Law 24).

> [!question]- 3. Apply De Morgan's to $\overline{\overline{A} + BC}$. $= \overline{\overline{A}}\cdot\overline{BC} = A(\overline{B} + \overline{C})$.

> [!question]- 4. How many cells does a 4-variable K-map have, and what are the label sequences? $2^4 = 16$ cells. Labels in Gray code: **00, 01, 11, 10**.

> [!question]- 5. Which K-map groupings are legal? Rectangles of **1, 2, 4, 8, or 16** cells; wrap-around allowed (top↔bottom, left↔right); **no diagonals**; groups may overlap; every 1 must be covered.

> [!question]- 6. Simplify with a K-map: $F(A,B,C) = \sum m(0,1,4,5,6)$ $F = \overline{B} + A\overline{C}$ (same as K-map truth table Example 1).

---

> [!success] Study plan suggestion
> 
> 1. Memorize the **26 laws** (section 1) and be able to name the law used in each step
> 2. Redo the **Boolean proofs and K-map examples** on paper without looking
> 3. Finish with the **Practice Questions**, covering the answers first