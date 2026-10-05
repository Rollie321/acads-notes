# Seatwork — Binary Number Systems

## 1. Convert \(45_{10}\) into 8-bit straight binary

Break \(45\) into powers of \(2\):

$$
45 = 32 + 8 + 4 + 1
$$

Using the 8-bit place values:

$$
\begin{array}{cccccccc}
128 & 64 & 32 & 16 & 8 & 4 & 2 & 1 \\
0 & 0 & 1 & 0 & 1 & 1 & 0 & 1
\end{array}
$$

Therefore,

$$
\boxed{45_{10} = 00101101_2}
$$

---

## 2. Convert \(110101_2\) into decimal

Expand the binary number using powers of \(2\):

$$
110101_2
=
(1)(2^5)+(1)(2^4)+(0)(2^3)+(1)(2^2)+(0)(2^1)+(1)(2^0)
$$

$$
=32+16+0+4+0+1
$$

$$
=53
$$

Therefore,

$$
\boxed{110101_2 = 53_{10}}
$$

---

## 3. Perform the binary addition

$$
\begin{array}{r}
1011_2\\
+0111_2\\
\hline
10010_2
\end{array}
$$

Therefore,

$$
\boxed{1011_2+0111_2=10010_2}
$$

### Check

$$
1011_2=11_{10}
$$

$$
0111_2=7_{10}
$$

$$
11+7=18
$$

Since

$$
18_{10}=10010_2
$$

the answer is verified.

---

## 4. Convert $$3C_{16}$$ directly to 8-bit straight binary

Convert each hexadecimal digit into its 4-bit binary equivalent:

$$
3_{16}=0011_2
$$

$$
C_{16}=1100_2
$$

Combine the two groups:

$$
3C_{16}=0011\ 1100_2
$$

Therefore,

$$
\boxed{3C_{16}=00111100_2}
$$

---

## 5. Convert \(89_{10}\) into 8421 BCD

In 8421 BCD, each decimal digit is converted separately using 4 bits.

For digit \(8\):

$$
8=1000
$$

For digit \(9\):

$$
9=1001
$$

Therefore,

$$
89_{10}=1000\ 1001_{\mathrm{BCD}}
$$

$$
\boxed{89_{10}=1000\ 1001_{\mathrm{BCD}}}
$$

---

## 6. Convert \(0101\ 0011\ 1001_{\mathrm{BCD}}\) into decimal

Separate the BCD number into groups of four bits:

$$
0101=5
$$

$$
0011=3
$$

$$
1001=9
$$

Therefore,

$$
0101\ 0011\ 1001_{\mathrm{BCD}}=539_{10}
$$

$$
\boxed{539_{10}}
$$

---

## 7. Subtract \(1000_2-0001_2\)

Perform the binary subtraction:

$$
\begin{array}{r}
1000_2\\
-0001_2\\
\hline
0111_2
\end{array}
$$

Therefore,

$$
\boxed{1000_2-0001_2=0111_2}
$$

### Check

$$
8-1=7
$$

and

$$
7_{10}=0111_2
$$

---

## 8. Subtract \(11010_2-01111_2\)

Perform the binary subtraction:

$$
\begin{array}{r}
11010_2\\
-01111_2\\
\hline
01011_2
\end{array}
$$

Therefore,

$$
\boxed{11010_2-01111_2=01011_2}
$$

### Check using decimal conversion

$$
11010_2=26_{10}
$$

$$
01111_2=15_{10}
$$

Thus,

$$
26-15=11
$$

Since

$$
11_{10}=01011_2
$$

the answer is verified:

$$
\boxed{01011_2}
$$

---

# Final Answers

| No. | Answer                                |
| --: | ------------------------------------- |
|   1 | $${00101101_2}$$                      |
|   2 | $$ 53_{10} $$                         |
|   3 | \(\boxed{10010_2}\)                   |
|   4 | \(\boxed{00111100_2}\)                |
|   5 | \(\boxed{1000\ 1001_{\mathrm{BCD}}}\) |
|   6 | \(\boxed{539_{10}}\)                  |
|   7 | \(\boxed{0111_2}\)                    |
|   8 | \(\boxed{01011_2}\)                   |
