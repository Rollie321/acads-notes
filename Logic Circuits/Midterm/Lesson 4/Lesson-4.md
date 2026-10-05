
# Boolean Algebra:
- George Boole (1815-1864)
- algebra of logic applied to computer systems
- 1983, Claude E. Shannon used to solve relay logic problems

# Laws of Boolean Algebra

* OR laws
	1. $A + 0 = A$
	2. $A + 1 = 1$
	3. $A + A = A$
	4. $A + \bar A = 1$
* AND laws
	5. $A.0 =0$
	6. $A . 1 =A$ 
	7. $A.A =A$ 
	8. $A.\bar{A} = 0$
	
* Laws of complementation
	9. $\bar 0=1$
	10. $\bar 1=0$ 
	11. $if\ A=0,\ then \bar A = 1$
	12. $if\ A=1,\ then \bar A = 0$
	13. $A=A$
	
* Commutative Laws
	14. $A+B=B+A$
	15. $A.B=B.A$
	
* Associative Laws
	16. $A+(B+C)=(A+B)+C$
	17. $(A+B)+(C+D) = A + B + C + D$
	18. $A.(B.C)=(A.B).C$
	
* Distributive Laws
	19. $A(B+C) =AB+AC$
	20. $A + B.C=(A+B)(A+C)$
	21. $A + \bar A.B=A+B$
	
* Absorptive Laws
	22.  $A+AB=A$
	23. $A.(A+B)=A$
	24. $A.(\bar A + B)=AB$
	
* De morgan's Theorem
	25. $\overline {AB} = \overline A + \overline B$
	26. $\overline{(A+B)} = \overline A.\overline B$

# Problems
#### Example 1: Prove $A+\bar A.B=A+B$

$$
	\begin{aligned}
		\text{distributive law}\\
		{\color{yellow}A + \bar A.B} = A + B \\
		{\color{yellow}(A+\bar A) . (A+B)} = A + B\\\\

		 \text{OR law}\\
		{\color{yellow}(A+\bar A)} . (A+B) = A + B\\
		{\color{yellow}1}. (A+B) = A + B\\\\

		 \text{AND law}\\
		{\color{yellow}1. (A+B)} = A + B\\
		{\color{yellow}(A+B)} = A + B\\\\
		 A+B = A + B
	\end{aligned}
$$

#### Example 2: Prove $A+AB=A$
$$
	\begin{aligned}
		\text{factor out the \color{yellow}A}\\
		{\color{yellow}A}+{\color{yellow}A}B = A\\
		{\color{yellow}A}(1+B)=A\\\\
		
		\text{OR law}\\
		A({\color{yellow}1+B})=A\\
		A.{\color{yellow}1} = A\\\\
		\text{AND law}\\
		A.{\color{yellow}1} = A\\
		A = A
	\end{aligned}
$$

#### Example 3: Demorganize $\overline {A\overline{(B+\overline C)D}}$
$$
\begin{aligned}
	\overline {A\overline{(B+\bar C)D}}\\\\
	\bar A+ \overline{\overline {({B+\bar C})D}}\\
	\bar A+ \overline{\overline{(B+\bar C)}} + \overline D\\
	\bar A+(B+\overline C) + \overline D\\
	\bar A + B +\overline C + \overline D\\\\
	\boxed{\bar A + B +\overline C + \overline D}
\end{aligned}
$$

#### Example 4: Determine the output x
![[Pasted image 20260910101802.png]]
$$ (A\oplus B)(A+\overline B) $$

| A   | B   | B'  | A^B | B'+A | X   |
| :-- | --- | --- | --- | ---- | --- |
| 0   | 0   | 1   | 0   | 1    | 0   |
| 0   | 1   | 0   | 1   | 0    | 0   |
| 1   | 0   | 1   | 1   | 1    | 1   |
| 1   | 1   | 0   | 0   | 1    | 0   |

#### Example 5: Determine the boolean expression then simplify:

![[Pasted image 20260910102950.png]]

Expression:
$$\overline {((AB) +\overline C)}(BC)$$

Simplify:
$$
	\begin{aligned}
	\overline {((AB) +\overline C)}(BC)\\\\

	Demorganize\\
	\overline {\color{yellow}{(AB +\overline C)}}(BC)\\
	{\color{yellow} \overline{AB}. \overline {\overline C}}.BC\\
	\overline{AB}{\color{yellow}.}C.BC\\\\

	Demorganize\\
	{\color{yellow}\overline{AB}}.C.BC\\
	{\color{yellow}(\overline A + \overline B)}.C.B.C\\\\

	\text{AND law}\\
	(\overline A + \overline B).C.B.C\\
	(\overline A + \overline B).B.{\color{yellow}C.C}\\
	(\overline A + \overline B).B.\color{yellow}C\\\\

	\text{Disitributive law}\\
	{\color{yellow}(\overline A + \overline B).B}.C\\
	{\color{yellow}(\overline AB + \overline BB)}.C\\\\

	\text{AND law}\\
	(\overline AB + {\color{yellow}\overline BB}).C\\
	(\overline AB +{\color{yellow}0}).C\\\\
	
	\text{OR law}\\
	{\color{yellow}(\overline AB +0)}.C\\
	{\color{yellow}(\overline AB)}.C\\\\
	\overline ABC \\\\
	
	\boxed{\overline {((AB) +\overline C)}(BC) = \overline ABC}
	\end{aligned}
$$

#### Example 6: Determine the output, then simplify
![[logic-diagram-2.png]]

This circuit can be written as:
$$ (A \oplus B)(A+\overline B) $$

However, the $\oplus$ operator can also be rewritten into sum of NOT products
 $$A\oplus B = \overline AB + A\overline B$$

 Hence, we can rewrite the circuit as:
$$ ({\color{yellow}A \oplus B})(A+\overline B) = ({\color{yellow}\overline AB + A\overline B})(A+\overline B)$$
$$\boxed{(\overline AB + A\overline B)(A+\overline B)} $$

Then you can simplify it by multiplying the terms with foil method:
$$ A\overline AB + \overline AB\overline B + AA\overline B+A\overline {BB}$$
Apply the AND law:

| Law 1 (ata)        | Law 2 (di ko sure) |
| :----------------- | ------------------ |
| $A\overline A = 0$ | $AA=A$             |
$$ {\color{red}A\overline A}B + \overline A{\color{red} B\overline B} + {\color{yellow}AA}\overline B+A\overline {\color{yellow}BB}$$
$$ {\color{red}0}B + \overline A{\color{red} 0} + {\color{yellow}A}\overline B+A\overline {\color{yellow}B}$$

Variables multiplied to zero are cancelled out, then apply the OR law:
$$ {\color{red}0B} + {\color{red}\overline A 0} + {\color{yellow}A\overline B}+ {\color{yellow}A\overline B}$$
$$A\overline B + A \overline B = A\overline B$$
Now we got a simplified circuit:
$$\boxed{A\overline B}$$

#### Example 7: Find the equation from the given table

write the **minterm** and **maxterms**:

| A     | B   | C   | X   | Minterm                   | Maxterm                                   |
| :---- | --- | --- | --- | ------------------------- | ----------------------------------------- |
| **0** | 0   | 0   | 0   | $\overline {ABC}$         | $A + B + C$                               |
| 0     | 0   | 1   | 1   | $\overline{AB} C$         | $A + B + \overline C$                     |
| 0     | 1   | 0   | 0   | $\overline AB\overline C$ | $A + \overline B + C$                     |
| 0     | 1   | 1   | 0   | $\overline A BC$          | $A + \overline B + \overline C$           |
| 1     | 0   | 0   | 1   | $A\overline {BC}$         | $\overline A + B + C$                     |
| 1     | 0   | 1   | 0   | $A\overline B C$          | $\overline A + B + \overline C$           |
| 1     | 1   | 0   | 0   | $AB\overline C$           | $\overline A + \overline B + C$           |
| 1     | 1   | 1   | 1   | $ABC$                     | $\overline A + \overline B + \overline C$ |

* **Minterm:**
	- Write it as products
	- apply  **NOT operation** to variables with **0 Input**
- **Maxterm**:
	- write it as sum
	- variables with **input 1** applies **NOT operation**

Then you can write the **SOP** (sum of products), where the output $X = 1$
$$ \overline{AB}C + A\overline{BC} + ABC $$

Which should be equal also to the **POS** (product of sums), where the output $X=0$:
$$(A+B+C)(A+\overline B+C)(A+\overline B+\overline C)(\overline A+B+C)(\overline A+\overline B+C)$$

Next step is to simplify, kaso ang hirap boi tangina.


#### Example 8: Obtain the simplified boolean expression

| A   | B   | C   | X   |
| :-- | --- | --- | --- |
| 0   | 0   | 0   | 0   |
| 0   | 0   | 1   | 1   |
| 0   | 1   | 0   | 0   |
| 0   | 1   | 1   | 1   |
| 1   | 0   | 0   | 0   |
| 1   | 0   | 1   | 0   |
| 1   | 1   | 0   | 1   |
| 1   | 1   | 1   | 1   |

##### Step 1: Find the minterm and maxterm:

| A   | B   | C   | X   | Minterm           | Maxterm                     |
| :-- | --- | --- | --- | ----------------- | --------------------------- |
| 0   | 0   | 0   | 0   |                   | $A+B+C$                     |
| 0   | 0   | 1   | 1   | $\overline {AB}C$ |                             |
| 0   | 1   | 0   | 0   |                   | $A+\overline B+C$           |
| 0   | 1   | 1   | 1   | $\overline ABC$   |                             |
| 1   | 0   | 0   | 0   |                   | $\overline A+B+C$           |
| 1   | 0   | 1   | 0   |                   | $\overline A+B+\overline C$ |
| 1   | 1   | 0   | 1   | $AB\overline C$   |                             |
| 1   | 1   | 1   | 1   | $ABC$             |                             |

##### Step 2A: Find the SOP
$$(\overline {AB}C)+(\overline ABC)+(AB\overline C)+(ABC)$$

##### Step 2B: Find the POS:
$$ (A+B+C)(A+\overline B+C)(\overline A+B+C)(\overline A+B+\overline C)() $$

##### Step 3A: Simplify the SOP
$$(\overline {AB}C)+(\overline ABC)+(AB\overline C)+(ABC)$$
$$\overline AC(\overline B + B) + AB(\overline C+C)$$
$$\color{yellow} \boxed{\color{white} \overline AC + AB}$$

##### Step 3B: Simplify the POS:
$$ {\color{yellow}(A+B+C)(A+\overline B+C)}{\color{yellowgreen}(\overline A+B+C)(\overline A+B+\overline C)}$$
$$ {\color{yellow}(A+C)(B+\overline B)}{\color{yellowgreen}(\overline A + B)(\overline C+C)}$$
$$ (A+C)\cancel{(B+\overline B)}(\overline A + B)\cancel{(\overline C+C)}$$
$$ (A+C)(\overline A + B)$$
$$ A \overline A + AB + \overline AC + BC$$
Apply $\underline{OR}$ law
$$ {\color{red}A \overline A} + AB + \overline AC + BC$$
$$ AB + \overline AC + BC$$
Multiply imaginary 1:
$$ AB + \overline AC + BC{\color{yellow}(1)}$$
Use reverse $OR$ Law
$$ AB + \overline AC + BC{\color{yellow}(A+\overline A)}$$
Distribute and multiply:
$$AB + \overline AC + ABC + \overline ABC$$ 
Rearrange, then factor out:
$$ AB + ABC + \overline AC+\overline ABC$$
$$ {\color{yellow}AB} + {\color{yellow}AB}C + {\color{yellowgreen}\overline AC}+\overline {\color{yellowgreen}A}B{\color{yellowgreen}C}$$
$$ {\color{yellow}AB}(1+C)+{\color{yellowgreen}\overline AC}(1 + B)$$
Apply $OR$ law:
$$ AB({\cancel{1+C}})+\overline AC(\cancel{1 + B})$$
$$ \color{yellow}{\boxed{\color{white}AB + \overline AC}} $$
##### After that, the answer from POS and SOP should be equal:
$$POS =SOP$$
$$\boxed{AB+\overline AC = AB +\overline AC}$$

