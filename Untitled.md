	$$ \overline { \overline {\overline{AB}A}. \overline{ \overline {AB}B} }$$
> [!question]- Example 12: Simplify $\overline{\overline{\overline{AB}A}.\overline{\overline{AB}B}}$
>
> Apply **De Morgan's theorem** (Law 26) to both $\overline{AB}$ terms: $$ \overline{\overline{{\color{yellow}\overline{AB}}A}.\overline{{\color{yellow}\overline{AB}}B}} $$ $$ \overline{\overline{{\color{yellow}(\overline A+\overline B)}A}.\overline{{\color{yellow}(\overline A+\overline B)}B}} $$
>
> Apply the **distributive law** (Law 19): $$ \overline{\overline{{\color{yellow}(\overline A+\overline B)A}}.\overline{{\color{yellowgreen}(\overline A+\overline B)B}}} $$ $$ \overline{\overline{{\color{yellow}\overline AA+\overline BA}}.\overline{{\color{yellowgreen}\overline AB+\overline BB}}} $$
>
> Apply the **AND law** (Law 8): $$ \overline{\overline{{\color{red}\overline AA}+\overline BA}.\overline{\overline AB+{\color{red}\overline BB}}} $$ $$ \overline{\overline{{\color{red}0}+\overline BA}.\overline{\overline AB+{\color{red}0}}} $$
>
> Apply the **OR law** (Law 1): $$ \overline{\overline{{\color{yellow}0+\overline BA}}.\overline{{\color{yellowgreen}\overline AB+0}}} $$ $$ \overline{\overline{{\color{yellow}A\overline B}}.\overline{{\color{yellowgreen}\overline AB}}} $$
>
> Apply **De Morgan's theorem** (Law 26) to the long bar: $$ {\color{yellow}\overline{\overline{A\overline B}.\overline{\overline AB}}} $$ $$ {\color{yellow}\overline{\overline{A\overline B}}+\overline{\overline{\overline AB}}} $$
>
> Apply the **double complement** (Law 13): $$ {\color{yellow}\overline{\overline{A\overline B}}}+{\color{yellowgreen}\overline{\overline{\overline AB}}} $$ $$ {\color{yellow}A\overline B}+{\color{yellowgreen}\overline AB} $$
>
> Recognize the **sum of products** form of the XOR operator: $$ A\overline B+\overline AB = A\oplus B $$ $$ \boxed{A\oplus B} $$
>
> ##### Check: truth table
>
> |A|B|$\overline{A\overline B}$|$\overline{\overline AB}$|AND|X|$A\oplus B$|
> |:--|---|---|---|---|---|---|
> |0|0|1|1|1|0|0|
> |0|1|1|0|0|1|1|
> |1|0|0|1|0|1|1|
> |1|1|1|1|1|0|0|
>
> The output $X$ matches $A\oplus B$ in every row: $$ \boxed{\overline{\overline{\overline{AB}A}.\overline{\overline{AB}B}} = A\overline B+\overline AB = A\oplus B} $$

$$ \bar AB\bar C + ACD + A\bar BC\bar D + A\bar B\bar D + ABC\bar D$$
> [!question]- Example 13: Simplify $\overline AB\overline C + ACD + A\overline BC\overline D + A\overline B\,\overline D + ABC\overline D$
>
> Duplicate the term $A\overline BC\overline D$ using the **OR law** in reverse (Law 3), since it will be used twice: $$ \overline AB\overline C + ACD + {\color{yellow}A\overline BC\overline D} + A\overline B\,\overline D + ABC\overline D $$ $$ \overline AB\overline C + ACD + {\color{yellow}A\overline BC\overline D + A\overline BC\overline D} + A\overline B\,\overline D + ABC\overline D $$
>
> Rearrange using the **commutative law** (Law 14) and group the terms that share variables: $$ \overline AB\overline C + ACD + {\color{yellow}A\overline BC\overline D}+{\color{yellowgreen}A\overline BC\overline D} + {\color{yellowgreen}A\overline B\,\overline D} + {\color{yellow}ABC\overline D} $$ $$ \overline AB\overline C + ACD + ({\color{yellow}A\overline BC\overline D + ABC\overline D}) + ({\color{yellowgreen}A\overline BC\overline D + A\overline B\,\overline D}) $$
>
> Factor out the common terms: $$ \overline AB\overline C + ACD + ({\color{yellow}A\overline BC\overline D + ABC\overline D}) + ({\color{yellowgreen}A\overline BC\overline D + A\overline B\,\overline D}) $$ $$ \overline AB\overline C + ACD + {\color{yellow}AC\overline D}(\overline B + B) + {\color{yellowgreen}A\overline B\,\overline D}(C + 1) $$
>
> Apply the **OR laws** (Law 4 and Law 2): $$ \overline AB\overline C + ACD + AC\overline D({\color{yellow}\overline B + B}) + A\overline B\,\overline D({\color{yellowgreen}C + 1}) $$ $$ \overline AB\overline C + ACD + AC\overline D({\color{yellow}1}) + A\overline B\,\overline D({\color{yellowgreen}1}) $$
>
> Apply the **AND law** (Law 6): $$ \overline AB\overline C + ACD + {\color{yellow}AC\overline D(1)} + {\color{yellowgreen}A\overline B\,\overline D(1)} $$ $$ \overline AB\overline C + ACD + {\color{yellow}AC\overline D} + {\color{yellowgreen}A\overline B\,\overline D} $$
>
> Factor out the **AC** from the second and third terms: $$ \overline AB\overline C + {\color{yellow}AC}D + {\color{yellow}AC}\overline D + A\overline B\,\overline D $$ $$ \overline AB\overline C + {\color{yellow}AC}(D + \overline D) + A\overline B\,\overline D $$
>
> Apply the **OR law** (Law 4): $$ \overline AB\overline C + AC({\color{yellow}D + \overline D}) + A\overline B\,\overline D $$ $$ \overline AB\overline C + AC({\color{yellow}1}) + A\overline B\,\overline D $$
>
> Apply the **AND law** (Law 6): $$ \overline AB\overline C + {\color{yellow}AC(1)} + A\overline B\,\overline D $$ $$ \boxed{\overline AB\overline C + AC + A\overline B\,\overline D} $$
>
> ##### Check: truth table
>
> |A|B|C|D|Original X|$\overline AB\overline C$|$AC$|$A\overline B\,\overline D$|Simplified X|
> |:--|---|---|---|---|---|---|---|---|
> |0|0|0|0|0|0|0|0|0|
> |0|0|0|1|0|0|0|0|0|
> |0|0|1|0|0|0|0|0|0|
> |0|0|1|1|0|0|0|0|0|
> |0|1|0|0|1|1|0|0|1|
> |0|1|0|1|1|1|0|0|1|
> |0|1|1|0|0|0|0|0|0|
> |0|1|1|1|0|0|0|0|0|
> |1|0|0|0|1|0|0|1|1|
> |1|0|0|1|0|0|0|0|0|
> |1|0|1|0|1|0|1|1|1|
> |1|0|1|1|1|0|1|0|1|
> |1|1|0|0|0|0|0|0|0|
> |1|1|0|1|0|0|0|0|0|
> |1|1|1|0|1|0|1|0|1|
> |1|1|1|1|1|0|1|0|1|
>
> The original and simplified outputs match in all 16 rows (minterms 4, 5, 8, 10, 11, 14, 15): $$ \boxed{\overline AB\overline C + AC + A\overline B\,\overline D} $$

> [!example]- K-map Ex 4: $X = \overline AB\overline C + ACD + A\overline BC\overline D + A\overline B\,\overline D + ABC\overline D$
>
> ##### Step 1: Expand each term into minterms
>
> |Term|Binary (ABCD)|Minterms|
> |:--|---|---|
> |$\overline AB\overline C$|010-|4, 5|
> |$ACD$|1-11|11, 15|
> |$A\overline BC\overline D$|1010|10|
> |$A\overline B\,\overline D$|10-0|8, 10|
> |$ABC\overline D$|1110|14|
>
> Combined (no duplicates): $$ X = \sum m(4,5,8,10,11,14,15) $$
>
> ##### Step 2: Fill in the K-map (rows = AB, columns = CD, Gray code order)
>
> |AB \ CD|00|01|11|10|
> |:-:|:-:|:-:|:-:|:-:|
> |**00**|0|0|0|0|
> |**01**|1|1|0|0|
> |**11**|0|0|1|1|
> |**10**|1|0|1|1|
>
> ##### Step 3: Group the 1s
>
> |Group|Size|Cells|Type|
> |:--|---|---|---|
> |1|Quad|m10, 11, 14, 15|rows 11 and 10 × columns 11 and 10|
> |2|Pair|m4, 5|row 01 × columns 00 and 01|
> |3|Pair|m8, 10|row 10 × columns 00 and 10 (wrap-around, left ↔ right)|
>
> Every 1 is covered, and m10 is shared by Group 1 and Group 3 (overlap is allowed).
>
> ##### Step 4: Find the product term of each group
>
> **Group 1** (m10, 11, 14, 15): $A$ stays 1, $C$ stays 1; $B$ and $D$ change, so they are eliminated. $$ {\color{yellow}AC} $$
>
> **Group 2** (m4, 5): $A=0$, $B=1$, $C=0$ stay the same; $D$ changes, so it is eliminated. $$ {\color{yellowgreen}\overline AB\overline C} $$
>
> **Group 3** (m8, 10): $A=1$, $B=0$, $D=0$ stay the same; $C$ changes, so it is eliminated. $$ {\color{red}A\overline B\,\overline D} $$
>
> ##### Step 5: Sum (OR) the product terms
>
> $$ X = {\color{yellow}AC} + {\color{yellowgreen}\overline AB\overline C} + {\color{red}A\overline B\,\overline D} $$ $$ \boxed{X = AC + \overline AB\overline C + A\overline B\,\overline D} $$
>
> ##### Check: the K-map answer should equal the Boolean algebra answer (Example 13)
>
> $$ \text{K-map} = \text{Algebra} $$ $$ \boxed{\overline AB\overline C + AC + A\overline B\,\overline D = \overline AB\overline C + AC + A\overline B\,\overline D} $$