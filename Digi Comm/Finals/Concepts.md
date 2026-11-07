
---

## title: Error Detection and Correction - Concepts Explained tags: [computer-engineering, data-communications, study-notes, glossary] source: Advanced Electronic Communications Systems (Wayne Tomasi), Ch. "Fundamental Concepts of Data Communications", Sec. 4-6, pp. 160-168

# Error Detection and Correction: Concepts Explained

> [!abstract] How to use this file This is the "study guide" behind your slides. Each section explains the terms from a slide in plain language, starting from the basics.
> 
> - Content from the Tomasi book is marked **(book)**.
> - My own added explanations, analogies, and examples are marked **(extra)**.
> - Read **Part 1** first if the terms are new to you. Everything else builds on it.

**Contents**

1. [[#Part 1 Foundations you need first]]
2. [[#Part 2 Errors What goes wrong and how we measure it]]
3. [[#Part 3 Error detection]]
4. [[#Part 4 Error correction]]
5. [[#Part 5 Comparing the methods]]
6. [[#Part 6 Computer Engineering connections]]
7. [[#Part 7 Likely questions and short answers]]
8. [[#Glossary quick reference]]

---

## Part 1 Foundations you need first

### 1.1 Data communications

Sending digital data (1s and 0s) from one place to another: between two computers, from a computer to a printer, from a sensor to a microcontroller, and so on. The link can be a few feet long or thousands of miles (book).

### 1.2 Bit, byte, and binary

- **Bit**: one binary digit, either `0` or `1`.
- **Byte**: 8 bits grouped together. **Character** codes like ASCII use 7 or 8 bits per character.
- **Binary number**: a number written with only 0s and 1s. `1010010` is binary for 82.

### 1.3 Hexadecimal (hex)

A shorthand for binary. Each hex digit stands for 4 bits.

|Hex|Binary|Hex|Binary|
|---|---|---|---|
|0|0000|8|1000|
|1|0001|9|1001|
|2|0010|A|1010|
|3|0011|B|1011|
|4|0100|C|1100|
|5|0101|D|1101|
|6|0110|E|1110|
|7|0111|F|1111|

So hex `52` = `0101 0010` = binary `1010010` (the leading 0 is dropped for a 7-bit code).

### 1.4 ASCII

A standard table that maps characters to numbers. Examples used in your slides:

|Character|Hex|7-bit binary|
|---|---|---|
|R|52|1010010|
|T|54|1010100|
|H|48|1001000|
|E|45|1000101|
|space|20|0100000|
|C|43|1000011|
|A|41|1000001|
|/|2F|0101111|

### 1.5 Noise and interference

Unwanted electrical signals that get mixed into the data signal. Noise is the reason a transmitted `1` can arrive as a `0`. Sources include lightning, motors, generators, power lines, and fluorescent lights (book).

### 1.6 Redundancy

**Extra information added on purpose** so mistakes can be spotted or fixed. Everything in this topic is built on it.

> [!example] Analogy (extra) You tell a friend your phone number and then add "the digits add up to 40." If their sum comes out different, they know they misheard. The "add up to 40" is redundancy. It carries no new phone-number information, but it lets them check.

The cost of redundancy is **overhead**: extra bits to send, store, and process.

### 1.7 XOR (exclusive OR)

A logic operation that is the heart of parity, CRC, and Hamming codes.

|A|B|A XOR B|
|---|---|---|
|0|0|0|
|0|1|1|
|1|0|1|
|1|1|0|

Memory trick: **XOR gives 1 when the inputs are different.** Another way to see it: XOR is addition of bits where you ignore the carry (1 + 1 = 0).

Useful facts:

- XORing many bits together gives `1` if the number of 1s is **odd**, and `0` if it is **even**. This is exactly what parity needs.
- XOR is its own undo: `(A XOR B) XOR B = A`. This is why a receiver can "cancel out" what the sender added.

### 1.8 Modulo-2 arithmetic

Arithmetic where you only keep the remainder after dividing by 2, so there are no carries or borrows. In modulo-2:

- Addition = XOR.
- Subtraction = XOR (the same thing).

CRC uses modulo-2 division, which is long division where each "subtract" step is an XOR.

### 1.9 Block, frame, and "check" bits

Data is usually sent in groups called **blocks** or **frames**. Error-checking bits are added to each block. The book calls them:

- **BCS** (block check sequence), or
- **FCS** (frame check sequence).

Both mean the same thing here: the extra check bits appended after the data.

### 1.10 Simplex, half-duplex, and full-duplex (extra)

- **Simplex**: data flows one way only (like broadcast radio).
- **Half-duplex**: both ways, but taking turns (walkie-talkie).
- **Full-duplex**: both ways at once (phone call).

This matters because some correction methods (ARQ) need a way for the receiver to talk back. On a simplex link there is no return path.

---

## Part 2 Errors What goes wrong and how we measure it

### 2.1 Why errors are unavoidable

Because data circuits can be very long and run through electrically noisy places, some bits will arrive wrong. The design goal is not "no errors ever." It is **no undetected errors reaching the user** (book).

### 2.2 Types of errors (book)

|Type|Meaning|Example (sent → received)|
|---|---|---|
|**Single-bit**|Exactly one bit is wrong|`10110` → `10010`|
|**Multiple-bit**|Two or more bits are wrong, **not** next to each other|`10110` → `00111`|
|**Burst**|Two or more **adjacent** bits are wrong|`10110` → `11000`|

Burst errors are common in real systems because a short noise spike (like a lightning pulse) damages several bits in a row. A burst error is harder to detect and correct than a single-bit error.

### 2.3 P(e) and BER

Both describe "how often are bits wrong?"

- **P(e), probability of error**: the **expected, theoretical** rate. You calculate it from the system design and noise level.
- **BER, bit error rate**: the **measured, historical** rate. You count real errors over a period.

$$\text{BER} = \frac{\text{bits received in error}}{\text{total bits sent}}$$

> [!example] Example A P(e) of 10⁻⁵ means about 1 wrong bit in every 100,000 bits sent. Engineers measure BER in a real system and compare it with the P(e) they expected, to judge how well the system performs.

### 2.4 The two branches of error control

```
Error control
├── Error detection   → "Did an error happen?"
└── Error correction  → "Fix it" (resend, or repair in place)
```

---

## Part 3 Error detection

### 3.1 The basic idea

Error detection answers **only** "is something wrong?" It does **not** tell you which bit is wrong, and it does not fix anything. It works by adding redundant bits at the sender; the receiver recomputes them and compares.

The methods below go from simplest to most reliable: **VRC (parity) → Checksum → LRC → CRC**

### 3.2 Parity and VRC (vertical redundancy check)

**What it is:** one extra bit added to each character so that the total number of 1s follows a rule.

- **Odd parity**: the total number of 1s (including the parity bit) must be **odd**.
- **Even parity**: the total number of 1s must be **even**.

**Worked example (book):** the letter **R** is hex `52` = `1010010`. It has three 1s.

|Scheme|What's needed|Parity bit|Sent|
|---|---|---|---|
|Odd parity|Total 1s must be odd. Already 3 (odd) → add `0`|0|`0 1010010`|
|Even parity|Total 1s must be even. Have 3 → add `1` to make 4|1|`1 1010010`|

**Receiver:** counts the 1s. If the rule is broken, an error occurred.

**Why "vertical"?** If you stack characters as rows, each parity bit sits in a column at the end of its row. It checks one character at a time.

**Big weakness:** if **two** bits flip (or any even number), the count of 1s keeps the same odd/even nature, so the error is **not detected**. Over the long run, parity catches about **50%** of errors (book).

**Other parity names (book):** marking parity (parity bit always 1), no parity, and ignored parity (parity bit always 0 or ignored).

> [!note] Computer Engineering link (extra) A **UART** (the serial port hardware in microcontrollers like Arduino) can add this parity bit automatically. Hardware generates it with a chain of XOR gates.

### 3.3 Checksum

**What it is:** treat each character as a number, add them up, and append the total (or part of it) as a check value.

1. Sender: add all the character values → checksum. Append it.
2. Receiver: add the received characters again and compare with the received checksum.
3. Different → an error definitely happened.

**Example (extra, simplified):** message values `3, 7, 5` → checksum `15`. If the receiver gets `3, 9, 5` it computes `17`, which does not match `15`, so it knows something is wrong.

> [!warning] Limitation (extra) A plain sum can miss errors that cancel out (for example, one value goes up by 2 while another goes down by 2). Real systems use smarter checksums (e.g., IP/TCP headers use a ones-complement style sum).

### 3.4 LRC (longitudinal redundancy check)

**What it is:** parity calculated **across the message**, one bit position at a time. Think of lining up all characters in rows and computing a parity bit for each **column**. The resulting "column of parities" forms one extra character, appended to the message.

- **VRC** protects inside each character (row-wise).
- **LRC** protects across the whole block (column-wise).

**Worked example (book): message `THE CAT`**

|Char|Hex|7-bit binary|
|---|---|---|
|T|54|1010100|
|H|48|1001000|
|E|45|1000101|
|space|20|0100000|
|C|43|1000011|
|A|41|1000001|
|T|54|1010100|
|**LRC**|**2F**|**0101111**|

Each bit of the LRC is the even parity (XOR) of that column. XOR of all the hex values: `54 ⊕ 48 ⊕ 45 ⊕ 20 ⊕ 43 ⊕ 41 ⊕ 54 = 2F`. `2F` is the ASCII character `/`, so the transmitted message is **`THE CAT/`**.

**Strengths:**

- Detects about **95-98%** of errors (book).
- Overhead is one extra character no matter how long the message is.

**Weakness:** it can miss an error if an **even number of characters** have an error in the **same bit position**, because those parities cancel.

In practice, VRC and LRC are used **together** (a grid of parity checks) so errors have fewer places to hide.

### 3.5 CRC (cyclic redundancy check)

CRC is the most reliable detection method in this chapter and the most widely used in real systems.

#### 3.5.1 The idea in one sentence

Treat the whole message as one very long binary number, **divide it by a fixed agreed number** (the generator), and send the **remainder** along with the data. The receiver does the same division. If the remainder is 0, the data is almost certainly fine.

> [!example] Analogy (extra) Sender and receiver agree on a divisor, say 7. The sender has the number 100. 100 ÷ 7 leaves remainder 2. The sender arranges the message so it is **exactly divisible by 7** (by adding the right check value). The receiver divides what arrives by 7. Any remainder means the number changed on the way. CRC does exactly this, but with binary and XOR instead of ordinary arithmetic.

#### 3.5.2 Generator polynomial

The agreed divisor is written as a **polynomial** because the 1s and 0s are treated as coefficients of powers of x. Example: `1011` means

$$1\cdot x^3 + 0\cdot x^2 + 1\cdot x^1 + 1\cdot x^0 = x^3 + x + 1$$

The book's standard **CRC-16** generator is:

$$P(x) = x^{16} + x^{15} + x^2 + 1$$

You do not need to do algebra with polynomials. Just read them as a pattern of 1s and 0s: a term present = a `1` at that position.

#### 3.5.3 Terms to know

- **Dividend**: the message bits (with zeros added at the end, see below).
- **Divisor / generator**: the agreed polynomial pattern.
- **Remainder**: the leftover after division. This becomes the **BCS / CRC** bits.
- Number of check bits = degree of the generator (CRC-16 → 16 check bits).

#### 3.5.4 Worked example (extra, verified by calculation)

Data: `11010011101100`. Generator: `1011` (so 3 check bits).

**Step 1: append 3 zeros** to the data (as many zeros as check bits): `11010011101100 000` → `11010011101100000`

**Step 2: modulo-2 divide** by `1011`. Whenever the leading bit is 1, XOR the divisor in at that position:

| Step  | Divisor lined up at bit | Result after XOR    |
| ----- | ----------------------- | ------------------- |
| start |                         | `11010011101100000` |
| 1     | 0                       | `01100011101100000` |
| 2     | 1                       | `00111011101100000` |
| 3     | 2                       | `00010111101100000` |
| 4     | 3                       | `00000001101100000` |
| 5     | 7                       | `00000000110100000` |
| 6     | 8                       | `00000000011000000` |
| 7     | 9                       | `00000000001110000` |
| 8     | 10                      | `00000000000101000` |
| 9     | 11                      | `00000000000000100` |

(Positions with a leading 0 are skipped.) The leftover in the last 3 bits is **`100`**. That is the CRC.

**Step 3: send** the original data followed by the remainder: `11010011101100` + `100`.

**Step 4: receiver divides** the whole received block by `1011`:

- No error → remainder **`000`** → accept.
- If the 4th bit from the left flips in transit → remainder **`101`** (not zero) → **error detected**.

#### 3.5.5 How good is CRC-16? (book)

It detects:

- all single-bit errors,
- all double-bit errors (under the book's stated conditions),
- all odd numbers of bit errors,
- **all burst errors of 16 bits or less**,
- about **99.9%** of longer bursts.

Overall, about **99.999%** of errors are detected.

#### 3.5.6 How CRC is built in hardware (book)

A CRC generator is a **shift register** (a chain of flip-flops) with **XOR gates** placed at the positions where the polynomial has a term. Bits are shifted in one at a time, and at the end the register holds the remainder. This is very cheap and fast in hardware, which is why Ethernet and storage devices use it.

> [!note] Name check (extra) A shift register with XOR feedback is called an **LFSR** (linear feedback shift register).

---

## Part 4 Error correction

### 4.1 Lost vs. damaged messages (book)

- **Lost message**: never arrives, or arrives unrecognizable.
- **Damaged message**: arrives, but contains bit errors.

### 4.2 Detecting code vs. correcting code

- An **error-detecting** code has enough redundancy to know **that** an error happened.
- An **error-correcting** code has enough redundancy to know **which bit** is wrong, so it can be repaired.

Correction needs more redundant bits than detection.

### 4.3 Two practical strategies

1. **Retransmission (ARQ)**: ask the sender to send it again.
2. **Forward error correction (FEC)**: repair it at the receiver.

### 4.4 ARQ (automatic repeat request)

The receiver checks each message (using parity/CRC). Then:

|Term|Meaning|
|---|---|
|**ACK** (positive acknowledgment)|"Received correctly, send the next one."|
|**NAK** (negative acknowledgment)|"It had errors, send it again."|
|**Time-out**|Sender waited too long without getting any reply, so it assumes the message (or the reply) was lost and resends.|

**Two styles (book):**

- **Discrete (stop-and-wait) ARQ**: send one message, **wait** for an ACK, then send the next. Simple but wastes time waiting.
- **Continuous ARQ (selective repeat)**: keep sending numbered messages without waiting for each reply. The receiver reports which numbered ones were bad, and **only those** are resent.

**Overhead and sizing (book):**

- Costs extra time for acknowledgments and for the line to change direction.
- Very short messages waste time on overhead; very long messages are more likely to contain errors. The book gives an optimal message size of about **256-512 characters**.

**When it fits:** two-way links where a short delay is fine.

### 4.5 FEC (forward error correction)

The sender adds enough redundant bits that the receiver can **locate** the bad bit and **fix it by flipping it** (complementing it). No retransmission is needed.

**Advantages (book):** works on one-way (simplex) links, broadcast to many receivers, and very long-delay links like deep-space communication, where waiting for a resend is impractical.

**Cost:** many more redundant bits are needed than for simple detection, so FEC is generally practical only for correcting a small number of errors (about 1-3 bits).

### 4.6 Hamming code

The most popular error-correcting code (book), invented by **Richard W. Hamming** at Bell Labs.

#### 4.6.1 What it can and cannot do

- **Can:** find and correct a **single-bit** error in the data.
- **Cannot:** correct multiple-bit or burst errors, and it cannot reliably identify errors that hit the Hamming bits themselves.

#### 4.6.2 How many Hamming bits do you need?

$$2^n \ge m + n + 1$$

- **m** = number of data bits
- **n** = number of Hamming (check) bits

**Why this formula? (extra)** The n Hamming bits can form 2ⁿ different patterns. Those patterns have to say either "no error" (1 pattern) or "the error is at position 1, 2, ..., (m + n)" (m + n patterns). So you need at least m + n + 1 patterns.

**Example (book):** 7-bit ASCII (m = 7)

- n = 3: 2³ = 8, but m + n + 1 = 11 → too small.
- n = 4: 2⁴ = 16 ≥ 12 → works.
- So you send 7 + 4 = **11 bits**, an increase of about **57%**.

#### 4.6.3 Worked example (book, Example 5, checked by calculation)

**Data (m = 12):** `101100010010`

**Step 1: how many Hamming bits?**

- n = 4: 16 vs 12 + 4 + 1 = 17 → fails.
- n = 5: 32 ≥ 18 → works. So **5 Hamming bits**, **17 bits** in total.

**Step 2: layout.** Number the bit positions 17 down to 1. The book puts the Hamming bits (H) at positions **17, 13, 9, 8, 4**. The data bits fill the rest.

|Pos|17|16|15|14|13|12|11|10|9|8|7|6|5|4|3|2|1|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|Bit|H|1|0|1|H|1|0|0|H|H|0|1|0|H|0|1|0|

**Step 3: compute the Hamming bits.** Write the positions that hold a `1` in 5-bit binary and XOR them all:

|Position with a 1|5-bit binary|
|---|---|
|2|00010|
|6|00110|
|12|01100|
|14|01110|
|16|10000|
|**XOR**|**10110**|

Those five result bits become the Hamming bits: b17 = 1, b13 = 0, b9 = 1, b8 = 1, b4 = 0.

**Step 4: transmitted code (17 bits):** `11010100110100010`

**Step 5: an error happens.** Suppose bit **14** flips from 1 to 0 on the way.

**Step 6: receiver checks.** It XORs the **received** Hamming bits (`10110`) with the positions of all the remaining 1s among the non-Hamming bits (16, 12, 6, 2):

|Item|Binary|
|---|---|
|Position 16|10000|
|Position 12|01100|
|Position 6|00110|
|Position 2|00010|
|Received Hamming bits|10110|
|**XOR result**|**01110 = 14**|

The result `01110` is **14**, the position of the bad bit. Flip bit 14 back to 1 and the data is repaired.

> [!tip] Key insight to say out loud The XOR result **is** the address of the bad bit. If the result were `00000`, there would be no error.

---

## Part 5 Comparing the methods

|Method|Detects|Corrects|Overhead|Main weakness|
|---|---|---|---|---|
|**VRC / Parity**|about 50% (long-run)|No|1 bit per character|Misses errors with an even number of flipped bits|
|**Checksum**|Varies|No|1 value per message|A plain sum can miss errors that cancel|
|**LRC**|95-98%|No|1 character per message|Misses even counts in the same bit column|
|**CRC-16**|about 99.999%|No|16 bits per message|Needs shift-register or division logic|
|**ARQ**|Uses a detector (e.g. CRC)|Yes, by resending|ACKs, waiting, line turnaround|Needs a return path; adds delay|
|**FEC / Hamming**|Yes|Yes (single-bit)|e.g. +57% for 7-bit ASCII|Cannot fix burst or multi-bit errors|

**The core trade-off:** more reliability costs more **overhead** (extra bits), more **delay** (waiting or resending), or more **complexity** (extra hardware or computation).

---

## Part 6 Computer Engineering connections

All of these are **(extra)** explanations that tie the topic to your program.

|Area|Where it appears|Why it matters|
|---|---|---|
|**Digital logic design**|Parity generators and checkers are trees of XOR gates. CRC circuits are shift registers plus XOR gates.|These are circuits you can draw and build from the gates you already know.|
|**Computer memory**|**ECC RAM** uses Hamming-style codes (commonly single-error-correct, double-error-detect) to fix flipped bits in memory.|Cosmic rays and electrical noise can flip a stored bit.|
|**Storage**|Hard drives, SSDs, CDs, and QR codes use stronger correction codes (e.g., Reed-Solomon, a relative of the ideas here).|Lets scratched or worn media still be read.|
|**Networking**|Every Ethernet frame ends with a **CRC-32** (the FCS). IP and TCP headers use checksums. TCP uses ARQ-style acknowledgments and retransmission.|Why web pages arrive intact over a noisy network.|
|**Embedded systems**|UART serial can include a parity bit. Firmware images and sensor protocols often include a CRC.|Directly relevant if you use Arduino or other microcontrollers.|
|**Modems / modulation (book)**|**Trellis code modulation** adds controlled redundancy to the signal to lower the bit error rate (used in modem standards such as V.32 and V.33).|Shows detection and correction ideas applied at the signal level.|

---

## Part 7 Likely questions and short answers

> [!question] What's the difference between error detection and error correction? Detection only says **that** an error occurred. Correction also finds **which bit** is wrong (or gets a fresh copy) and fixes it.

> [!question] Why is parity not enough on its own? It misses any error that flips an even number of bits, so it catches only about half of all errors.

> [!question] What is the difference between VRC and LRC? VRC adds a parity bit to each **character** (row-wise). LRC computes parity for each **bit column** across the whole message and sends it as one extra character.

> [!question] Why is CRC so much better than a simple checksum? Its division-based remainder is sensitive to the **position** of every bit, not just their sum, so it catches all burst errors up to the CRC length and about 99.999% overall.

> [!question] How does a receiver know a CRC passed? It runs the same division on the data **plus** the CRC. A remainder of **zero** means no detected error.

> [!question] ARQ or FEC, which is better? Neither always. ARQ is cheap in bits but needs a return path and time. FEC sends more bits every time but needs no return path, so it suits one-way or very long-delay links.

> [!question] Why can Hamming code only fix one bit? The check bits give just enough patterns to name one bad position (or "no error"). Two errors would produce a pattern that points to the wrong place.

> [!question] What does P(e) = 10⁻⁵ mean? On average, about 1 bit in 100,000 is expected to be wrong.

> [!question] Why add redundancy if it wastes bandwidth? Because without it you cannot tell good data from bad. The extra bits buy reliability.

---

## Glossary quick reference

|Term|Plain-language meaning|
|---|---|
|**ACK / NAK**|"Got it correctly" / "Got it with errors, resend."|
|**ARQ**|Automatic repeat request: error correction by asking for a resend.|
|**ASCII**|Table mapping characters to numbers.|
|**BCS / FCS**|Block / frame check sequence: the extra check bits appended to a block.|
|**BER**|Bit error rate: the measured fraction of bits received wrong.|
|**Bit**|A single 0 or 1.|
|**Burst error**|Two or more adjacent bits wrong.|
|**Checksum**|A check value made by adding up the data.|
|**CRC**|Cyclic redundancy check: remainder of polynomial division, appended to data.|
|**Detection**|Finding out that an error happened.|
|**Correction**|Fixing the error, by resend or by repair in place.|
|**Even / odd parity**|Rule that the total count of 1s must be even / odd.|
|**FEC**|Forward error correction: receiver repairs errors without a resend.|
|**Generator polynomial**|The agreed divisor used in CRC.|
|**Hamming bits**|Extra check bits that let Hamming code locate a bad bit.|
|**Hamming code**|Popular single-bit error-correcting code.|
|**Hex**|Base-16 shorthand for binary (1 digit = 4 bits).|
|**LFSR**|Linear feedback shift register: shift register with XOR feedback; builds CRC hardware.|
|**LRC**|Longitudinal redundancy check: column-wise parity across a message.|
|**Modulo-2**|Arithmetic with no carries; addition and subtraction are both XOR.|
|**Noise**|Unwanted signals that corrupt data.|
|**Overhead**|The cost (extra bits or time) of adding reliability.|
|**P(e)**|Probability of error: the expected error rate.|
|**Parity bit**|One extra bit that forces an even or odd count of 1s.|
|**Redundancy**|Extra information added on purpose to enable checking.|
|**Remainder**|What is left after a division; the CRC value.|
|**Simplex**|One-way communication link.|
|**Time-out**|Waiting limit after which the sender resends.|
|**UART**|Serial-port hardware in microcontrollers; can add a parity bit.|
|**VRC**|Vertical redundancy check: per-character parity.|
|**XOR**|Output is 1 when the inputs differ.|

---

## References

- Tomasi, W. _Advanced Electronic Communications Systems_, Ch. "Fundamental Concepts of Data Communications", Sec. 4-6, pp. 160-168 (error control, detection, correction).
- Tomasi, trellis code modulation and probability-of-error / BER sections (digital modulation chapter).
- Video explanations: see the list I gave you earlier (Ben Eater's error detection series, 3Blue1Brown on Hamming codes).