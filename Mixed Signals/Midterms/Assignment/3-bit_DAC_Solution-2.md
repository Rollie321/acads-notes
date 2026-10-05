![[Midterm-ass-1.png]]


# Seatwork 3: 3-Bit Binary-Weighted Resistor DAC

**Given:**
$$R_f = 2\ \text{k}\Omega, \qquad \text{Logic levels} = 0\ \text{V or } +10\ \text{V}$$

---

## Circuit

A binary-weighted resistor DAC uses a summing (inverting) op-amp, with each bit's input resistor scaled by a power of two. Bit 2 (MSB) uses the smallest resistor, and each lower-order bit uses a resistor twice the previous one, so its contribution is half as strong.

$$R_2 = 2R_f, \qquad R_1 = 4R_f, \qquad R_0 = 8R_f$$


```tikz
\begin{document}
\begin{tikzpicture}[scale=1.0, every node/.style={font=\small}]
% Op-Amp Body
\draw (6,0) -- (6,-2.4) -- (7.6,-1.2) -- cycle;
\node at (6.55,-0.55) {$-$};
\node at (6.55,-1.85) {$+$};
\node[right] at (7.7,-1.2) {$V_{out}$};

% Feedback Loop (Rf)
\draw (6.9,-1.2) -- (6.9,0.9) -- (4.8,0.9) -- (4.8,-1.2);
\draw (5.4,0.6) rectangle (6.3,1.2);
\node at (5.85,1.5) {$R_f = 2k\Omega$};

% Inverting Node Connection
\draw (4.8,-1.2) -- (6,-1.2);
\fill (4.8,-1.2) circle (1.5pt);

% Ground Connection (Fixed wire gap)
\draw (6,-1.2) -- (6.8,-1.2); 
\draw (6.8,-1.95) -- (6.8,-2.3);
\draw (6.6,-2.3) -- (7.0,-2.3);
\draw (6.65,-2.45) -- (6.95,-2.45);
\draw (6.7,-2.55) -- (6.9,-2.55);

% Branch 2 (b2)
\draw (0.5,0) -- (2.0,0);
\draw (2.0,-0.3) rectangle (3.4,0.3);
\node at (2.7,0.6) {$R_2=4k\Omega$};
\draw (3.4,0) -- (4.8,0) -- (4.8,-1.2);
\node[left] at (0.5,0) {$b_2$};

% Branch 1 (b1)
\draw (0.5,-0.8) -- (2.0,-0.8);
\draw (2.0,-1.1) rectangle (3.4,-0.5);
\node at (2.7,-0.2) {$R_1=8k\Omega$};
\draw (3.4,-0.8) -- (4.4,-0.8) -- (4.4,-1.2);
\node[left] at (0.5,-0.8) {$b_1$};

% Branch 0 (b0)
\draw (0.5,-1.6) -- (2.0,-1.6);
\draw (2.0,-1.9) rectangle (3.4,-1.3);
\node at (2.7,-1.0) {$R_0=16k\Omega$};
\draw (3.4,-1.6) -- (4.6,-1.6) -- (4.6,-1.2);
\node[left] at (0.5,-1.6) {$b_0$};
\end{tikzpicture}
\end{document}

```

Each input is switched between $0\text{ V}$ and $+10\text{ V}$ by the logic bit. Since the summing junction is a virtual ground, each branch acts like an independent current source into the summing node:

$$V_{out} = -R_f\left(\frac{V_2}{R_2} + \frac{V_1}{R_1} + \frac{V_0}{R_0}\right)$$

---

## a. Voltage Weight of Each Bit

The **voltage weight** of a bit is the contribution to $V_{out}$ when that bit alone is HIGH (+10 V) and all others are LOW.

$$W_i = -\frac{R_f}{R_i}\,V_{in}$$

**Bit 2 (MSB), $R_2 = 2R_f = 4\text{ k}\Omega$:**
$$W_2 = -\frac{2\text{ k}\Omega}{4\text{ k}\Omega}(10\text{ V}) = -5.00\text{ V}$$

**Bit 1, $R_1 = 4R_f = 8\text{ k}\Omega$:**
$$W_1 = -\frac{2\text{ k}\Omega}{8\text{ k}\Omega}(10\text{ V}) = -2.50\text{ V}$$

**Bit 0 (LSB), $R_0 = 8R_f = 16\text{ k}\Omega$:**
$$W_0 = -\frac{2\text{ k}\Omega}{16\text{ k}\Omega}(10\text{ V}) = -1.25\text{ V}$$

Notice each weight is exactly half of the one before it, matching the binary place value ($5.00, 2.50, 1.25$).

---

## b. Full-Scale Output

Full scale occurs when **all bits = 1** (code $111$), so all weights add together:

$$V_{FS} = W_2 + W_1 + W_0 = -(5.00 + 2.50 + 1.25)\ \text{V}$$

$$\boxed{V_{FS} = -8.75\ \text{V}}$$

---

## c. Truth Table (Codes 000 to 111)

$$V_{out} = -(b_2 \cdot 5.00 + b_1 \cdot 2.50 + b_0 \cdot 1.25)\ \text{V}$$

| $b_2$ | $b_1$ | $b_0$ | Code | $V_{out}$ (V) |
| :---: | :---: | :---: | :--: | :-----------: |
|   0   |   0   |   0   | 000  |    $0.00$     |
|   0   |   0   |   1   | 001  |    $-1.25$    |
|   0   |   1   |   0   | 010  |    $-2.50$    |
|   0   |   1   |   1   | 011  |    $-3.75$    |
|   1   |   0   |   0   | 100  |    $-5.00$    |
|   1   |   0   |   1   | 101  |    $-6.25$    |
|   1   |   1   |   0   | 110  |    $-7.50$    |
|   1   |   1   |   1   | 111  |    $-8.75$    |

**Resolution check:** the step size between consecutive codes is constant at $1.25\text{ V}$, equal to the LSB weight — confirming the DAC is linear, as expected for a properly scaled binary-weighted resistor network.
