**Discussion** of Results

![[lab-1.png]]


![[lab-2.png]]

![[lab-3.png]]

The half-adder gave SUM = A ⊕ B (ON only when the inputs differ) and CARRY = AB (ON only when A = B = 1). The full-adder gave SUM = A ⊕ B ⊕ C, which was ON for 001, 010, 100 and 111, meaning an odd number of 1s at the inputs. Its CARRY = AB + C(A ⊕ B) was ON for 011, 101, 110 and 111, meaning two or more inputs were 1.

The half-subtractor gave DIFFERENCE = A ⊕ B and BORROW = A'B. The borrow was ON only for A = 0, B = 1, because 0 − 1 needs a borrow. The full-subtractor  gave DIFFERENCE = A ⊕ B ⊕ C and BORROW = A'B + C(A'B' + AB), which is the same as A'B + A'C + BC. The borrow was ON for 001, 010, 011 and 111, the cases where A is less than B + C. In the network, the XOR output A ⊕ B is reused for both the difference and the borrow, so the circuit needs only two AND gates.

The measured LED states agreed with the derived Boolean expressions for every input combination. This confirms that the circuits were wired correctly and that the simplified functions are valid. The difference and sum outputs are identical, and the borrow differs from the carry only by the complement on A.

Questions

1. Under what conditions does the LED indicator for the carry signal turn on in a full-adder circuit?
The carry LED turns on when at least two of the three inputs (A, B, C) are 1: ABC = 011, 101, 110 and 111. In these cases the sum of the inputs is 2 or 3, which cannot fit in one bit, so a carry goes to the next stage.

2. When does the borrow signal light up in a full-subtractor circuit?
The borrow LED turns on when A is less than B + C (the subtrahend plus the incoming borrow): ABC = 001, 010, 011 and 111. It is ON when A = 0 and B or C is 1, or when B and C are both 1.

3. Describe a practical method for adapting an adder network so that it carries out full-subtractor logic.
The DIFFERENCE is the same XOR of three inputs as the adder's SUM, so that part is used unchanged. For the borrow, put an inverter on input A before the carry part of the adder. This gives Borrow = A'B + A'C + BC, which is the carry logic with A complemented. Alternatively, invert B with a NOT gate and set the first carry-in to 1  The final carry-out is then the complement of the borrow.

4. How can a parallel adder be constructed using multiple full adders?

To add numbers with more than one bit, connect several full adders side by side, one for each bit. The first adder adds the rightmost bits, the next adder adds the next bits, and so on. Each adder passes its carry to the adder on its left, just like carrying a 1 to the next column when adding by hand. The first adder has no carry coming in, so its carry input is set to 0. Each adder gives one sum bit, and together the sum bits form the answer. If the last adder still has a carry, it becomes an extra bit at the front of the answer.

5. State the purpose of the carry-out bit and explain how it is used when several adders are connected in parallel.

The carry-out bit is the "extra 1" that is passed on when the sum of a column is too big to fit in one bit. It works like carrying a 1 to the next column when adding by hand. When several adders are connected, each adder sends its carry-out to the adder next to it on the left, where it is added along with the next pair of bits. In this way the carry moves step by step from the rightmost bit to the leftmost bit. If the last adder still produces a carry-out, the answer is too big for the number of bits used, so that carry becomes an extra bit at the front of the answer.

Conclusion

The half-adder, full-adder, half-subtractor and full-subtractor were designed, simplified and assembled, and the LED outputs matched the truth tables for all input combinations. The half-subtractor was built from one XOR gate and one NOT-AND combination. The full-subtractor was built from two XOR gates, two NOT gates, two AND gates and one OR gate . Reusing the first XOR output kept the full-subtractor at two AND gates instead of three, which shows how simplifying the functions reduces the number of devices. The adder sum and the subtractor difference are both A ⊕ B ⊕ C. The carry is AB + C(A ⊕ B), and the borrow is A'B + C(A ⊕ B)', which equals A'B + A'C + BC. The two circuits differ only in the complemented A term, so an adder can be converted into a subtractor with little change. Cascading full adders and subtractors extends these circuits to multi-bit arithmetic, which is the basis of the arithmetic logic in digital systems.
Compose