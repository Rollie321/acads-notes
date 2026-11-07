

---

## title: Error Detection and Error Correction tags: [computer-engineering, data-communications, presentation] source: Advanced Electronic Communications Systems (Wayne Tomasi), Ch. "Fundamental Concepts of Data Communications", Sec. 4-6, pp. 160-168

# Error Detection and Error Correction

> [!abstract] Presentation plan About 12 slides. Each section below is one slide, with talking points under it. Items marked **(CpE)** are extra material for the Computer Engineering context and are not from the book.

---

## Slide 1: Title

- **Error Detection and Error Correction**
- Subject / Program: Computer Engineering
- Presenter, date
- Source: Tomasi, _Advanced Electronic Communications Systems_

---

## Slide 2: Why Errors Occur

Data circuits can be a few feet or thousands of miles long, so **errors are inevitable**. That is why error control is needed.

**Causes of transmission errors**

- Natural: lightning
- Man-made: motors, generators, power lines, fluorescent lights

> [!tip] Say this The goal is not to stop errors from happening. It is to stop _undetected_ errors from reaching the user.

---

## Slide 3: Error Types and BER

|Type|Definition|Example|
|---|---|---|
|**Single-bit**|Only one bit in the data string is wrong|`10110` → `10010`|
|**Multiple-bit**|Two or more _non-consecutive_ bits wrong|`10110` → `00111`|
|**Burst**|Two or more _consecutive_ bits wrong|`10110` → `11000`|

**Measuring error performance**

- **P(e)** (probability of error): the _theoretical_ expectation.
- **BER** (bit error rate): the _measured_ historical record.
- Example: P(e) = 10⁻⁵ means 1 bit error per 100,000 bits.
- BER is measured and then compared with P(e) to judge system performance.

**Error control splits into two categories**

```
Error Control
├── Error Detection   (is there an error?)
└── Error Correction  (fix it)
```

---

## Slide 4: Error Detection Overview

- Detection only tells you **that** an error occurred.
- It does **not** correct the error or say **which** bit is wrong.
- Basic idea: add **redundant bits** so the receiver can check the data.
- Duplicating every data unit works but is costly. Adding check bits is far more efficient.

**Redundancy checking methods**

```
VRC (parity)  →  Checksum  →  LRC  →  CRC
simplest ───────────────────────► most reliable
```

---

## Slide 5: Parity / VRC (Vertical Redundancy Check)

One parity bit per character forces the total number of 1s to be **odd** (odd parity) or **even** (even parity).

**Example: ASCII "R" = 52 hex = `1010010`**

|Scheme|Count of 1s|Parity bit|Transmitted|
|---|---|---|---|
|Odd parity|3|0|`01010010`|
|Even parity|3|1|`11010010`|

**Pros and cons**

- ✅ Very simple.
- ❌ An **even number of bit errors cancels out** and goes undetected. Long-run detection is only about **50%**.

Other forms: marking parity (always 1), no parity, ignored parity (always 0).

> [!note] (CpE) UART serial frames (including Arduino `Serial`) can include an optional parity bit. This is exactly VRC at the hardware level.

---

## Slide 6: Checksum and LRC

### Checksum

1. Assign each character a numeric value.
2. Combine them (e.g. arithmetic sum) into one check character.
3. Append it to the message.
4. The receiver recomputes it and compares. A mismatch means an error **definitely** occurred.

### LRC (Longitudinal Redundancy Check)

- Parity is computed **per bit position** across all characters (XOR of the characters), so it is also called _message parity_.
- VRC works **within** a character. LRC works **across** the message.
- LRC bits are appended as an extra character, the **BCS / FCS** (block / frame check sequence).

**Example: message `THE CAT` (VRC odd parity, LRC even parity)**

||T|H|E|sp|C|A|T|**LRC**|
|---|---|---|---|---|---|---|---|---|
|Hex|54|48|45|20|43|41|54|**2F**|
|VRC bit (b7)|0|1|0|0|0|1|0|0|

LRC = `00101111` (2F), which is ASCII `/`, so the transmitted message is **`THE CAT/`**.

- Detects about **95-98%** of errors.
- Overhead is one character regardless of message length, so it suits long messages.
- Weakness: misses errors when an **even number of characters** have an error in the **same bit position**.

---

## Slide 7: CRC (Cyclic Redundancy Check)

The most reliable detection method in the chapter.

**Idea:** treat the whole message as one long binary number, divide it by a **generator polynomial** using **modulo-2 (XOR) division**, and append the **remainder** as the BCS.

- Receiver divides message + CRC by the same generator. **Remainder = 0 → no error.**
- Systematic code: the check bits are separate from the data but sent in the same transmission.
- BCS length = n − k, for an (n, k) cyclic code.

**CRC-16**

- Generator: **P(x) = x¹⁶ + x¹⁵ + x² + 1**
- Detects:
    - all single-bit errors
    - all double-bit errors (given the divisor has at least three 1s)
    - all odd numbers of bit errors (given a factor of x + 1)
    - all burst errors ≤ 16 bits
    - 99.9% of bursts > 16 bits
- Overall: about **99.999%** of errors detected.

**Mini example (modulo-2 division)**

- Data: `11010011101100`
- Generator: `1011` (x³ + x + 1), so append 3 zeros before dividing
- Remainder: **`100`**
- Transmitted: `11010011101100` + `100`
- Receiver divides the whole thing by `1011` and gets remainder `000`, so no error.

> [!note] Hardware A CRC generator needs one shift register per BCS bit, with XOR gates at the polynomial's taps (book Fig. 5).

---

## Slide 8: Error Correction Overview

**Two kinds of problem messages**

- **Lost message:** never arrives, or is unrecognizable.
- **Damaged message:** recognized, but contains errors.

**Two strategies**

|Strategy|What it does|
|---|---|
|**Error-detecting codes**|Enough redundancy to know an error occurred (parity, BCS, CRC)|
|**Error-correcting codes**|Enough redundancy to know **which bit** is wrong|

**Burst errors:** an advantage for block transmission (fewer blocks affected) but harder to detect and correct.

Two practical methods: **Retransmission (ARQ)** and **Forward Error Correction (FEC)**.

---

## Slide 9: ARQ vs. FEC

### ARQ (Automatic Repeat Request)

Receiver asks the sender to **resend** the message or part of it.

- **Positive ACK:** error-free message received.
- **Negative ACK:** errors found, so retransmit.
- **Time-out:** no ACK received, so the sender retransmits on its own.
- **Discrete ARQ:** waits for an ACK after each message.
- **Continuous ARQ (selective repeat):** numbered frames sent without waiting, and only the bad frames are resent.
- Reliable but adds overhead (acknowledgments and line turnarounds).
- Optimal message size: **256-512 characters**.

### FEC (Forward Error Correction)

Redundant bits let the receiver **find and fix** the error with no retransmission.

- The error bit is corrected simply by **complementing** it.
- Needs many more redundant bits than detection alone, so it is generally limited to **1-3 bit errors**.
- Best when ACKs are impractical: simplex links, broadcast to many receivers, deep-space communication.

||ARQ|FEC|
|---|---|---|
|Needs a return channel|Yes|No|
|Extra delay|Retransmission time|Extra bits on every message|
|Best for|Two-way links, bursty errors|One-way or long-delay links|

---

## Slide 10: Hamming Code

Developed by **Richard W. Hamming** at Bell Labs. It is the most popular error-correcting code.

- Corrects **single-bit errors only**.
- Cannot fix multiple-bit or burst errors, and cannot identify errors in the Hamming bits themselves.
- Hamming bits can be placed anywhere, as long as sender and receiver agree.

**How many Hamming bits?**

$$2^n \ge m + n + 1$$

- m = data bits, n = Hamming bits
- 7-bit ASCII → n = 4 → **11 bits sent** (≈ **57% increase**)

### Worked example (book Example 5)

Data: `101100010010` (m = 12)

1. n = 4: 16 < 17 ✗. n = 5: 32 ≥ 18 ✓, so **5 Hamming bits**, 17 bits total.
2. Place Hamming bits (H) at positions **4, 8, 9, 13, 17**.
3. Write the positions of all 1s as 5-bit binary and **XOR** them:
    - Positions with 1: 2, 6, 12, 14, 16
    - `00010 ⊕ 00110 ⊕ 01100 ⊕ 01110 ⊕ 10000` = **`10110`**
    - So b17 = 1, b13 = 0, b9 = 1, b8 = 1, b4 = 0
4. Transmitted 17-bit code: `11010100110100010`
5. Suppose bit **14** flips in transit.
6. Receiver XORs the received Hamming bits (`10110`) with the positions of the remaining 1s (2, 6, 12, 16):
    - Result = `01110` = **14** → the error position. Flip that bit to fix it.

> [!tip] Say this The XOR result _is_ the address of the bad bit. That is the whole trick.

---

## Slide 11: Comparison Table

|Method|Detects|Corrects|Overhead|Weakness|
|---|---|---|---|---|
|**VRC / Parity**|✅ (≈50% long-run)|❌|1 bit / char|Misses even-count errors|
|**Checksum**|✅|❌|1 char / message|Simple sum can miss errors|
|**LRC**|✅ (95-98%)|❌|1 char / message|Same bit position in an even number of characters|
|**CRC-16**|✅ (≈99.999%)|❌|16 bits / message|Needs division hardware or software|
|**ARQ**|uses a detector|✅ (by resend)|ACKs + turnarounds|Delay, needs a return path|
|**FEC / Hamming**|✅|✅ (single-bit)|e.g. +57% for 7-bit ASCII|Cannot fix bursts or multi-bit errors|

---

## Slide 12: Computer Engineering Applications (CpE)

|Area|Where it shows up|
|---|---|
|**Digital logic**|Parity generators are XOR trees. CRC hardware is an XOR plus shift-register (LFSR) circuit.|
|**Memory and storage**|ECC RAM uses Hamming-style codes. SSDs, HDDs and QR codes use stronger codes.|
|**Networking**|Ethernet frames end with a CRC-32 FCS. IP, TCP and UDP headers use checksums.|
|**Embedded systems**|UART parity bit, CRC on flash/firmware images, sensor bus checks.|
|**Modems** (book)|Trellis code modulation adds controlled redundancy to improve BER (V.32, V.33).|

---

## Slide 13: Summary

- Errors are unavoidable. Error control makes them manageable.
- **Detection** (VRC, checksum, LRC, CRC) says _that_ something is wrong. More reliable methods cost more bits.
- **Correction** is either **ARQ** (resend) or **FEC** (fix at the receiver, e.g. Hamming).
- The choice is a trade-off between **overhead, delay, and reliability**.

---

## Speaker Cheat Sheet

- **P(e)** = expected error rate, **BER** = measured error rate
- **VRC** = per character, **LRC** = per message, both use parity
- **CRC remainder = 0** at the receiver means the data is good
- **Hamming:** 2ⁿ ≥ m + n + 1, and the XOR of the check bits gives the error position
- **ARQ** = resend, **FEC** = fix in place

## References

- Tomasi, W. _Advanced Electronic Communications Systems_, Ch. "Fundamental Concepts of Data Communications", Sec. 4-6, pp. 160-168.
- Tomasi, trellis code modulation and probability of error / BER sections (digital modulation chapter).