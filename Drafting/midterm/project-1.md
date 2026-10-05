![[power-supply-schematics.png]]

# Component List — Variable Regulated Power Supply (1.2–30V @ 1.5A)

## Power Input Stage

| Component        | Value           | Purpose                                                    |
| ---------------- | --------------- | ---------------------------------------------------------- |
| **3-prong plug** | —               | Connects circuit to mains AC supply (Hot, Neutral, Ground) |
| **S1**           | Switch          | Turns the circuit on/off                                   |
| **Fuse**         | 0.5A            | Protects circuit from overcurrent/short-circuit damage     |
| **TR1**          | 24V transformer | Steps down mains voltage to a safe 24V AC (max)            |

## Rectification & Filtering

| Component | Value            | Purpose                                                                           |
| --------- | ---------------- | --------------------------------------------------------------------------------- |
| **BR1**   | Bridge rectifier | Converts AC from TR1 into pulsating DC using 4 diodes                             |
| **C1**    | 1000 µF, 63V     | Main smoothing (reservoir) capacitor — filters rectified DC into a stable voltage |
| **C2**    | 0.1 µF           | High-frequency bypass capacitor at IC1's input; prevents oscillation              |

The bridge rectifier's peak output voltage, given a 24V AC secondary, is approximately:

$$
V_{peak} = V_{AC(rms)} \times \sqrt{2} - 2V_{diode} \approx 24 \times 1.414 - 1.4 \approx 32.5\text{ V}
$$

## Regulation Stage

| Component | Value | Purpose |
|---|---|---|
| **IC1 (LM317)** | Adjustable regulator | Core 3-terminal regulator; holds a fixed 1.25V reference between OUT and ADJ pins |
| **D1** | 1N4001 | Protection diode — gives C3 a discharge path if the output is shorted, protecting IC1's ADJ pin |
| **R2** | 220 Ω | Fixed resistor between OUT and ADJ; sets the reference current/voltage |
| **P1** | 5 KΩ pot | Adjustable resistor from ADJ to ground; **this is the "Variable output" control** |
| **C3** | 1 µF, 40V | Bypasses ripple around the ADJ pin, improving ripple rejection |

The LM317's output voltage is set by the classic adjustable-regulator equation:

$$
V_{out} = V_{ref}\left(1 + \frac{P1}{R2}\right) + I_{ADJ} \cdot P1
$$

Since $I_{ADJ}$ (~50 µA) is negligible, and $V_{ref} \approx 1.25\text{ V}$:

$$
V_{out} \approx 1.25\left(1 + \frac{P1}{220}\right)
$$

- **P1 = 0 Ω:** $V_{out} \approx 1.25\text{ V}$ (matches the "1.2V" minimum spec)
- **P1 = 5000 Ω:** $V_{out} \approx 1.25(1 + 22.7) \approx 29.7\text{ V}$ (matches the "30V" max spec)

## Indicator LED

| Component | Value | Purpose |
|---|---|---|
| **LED1** | Standard LED | Power-on indicator light |
| **R1** | 1.8 KΩ | Current-limiting resistor for LED1, sized to keep LED current safe across the wide output voltage range |

## Optional Voltmeter Circuit

| Component | Value | Purpose |
|---|---|---|
| **R3** | 27 KΩ | Fixed series multiplier resistor for the meter |
| **P2** | 10 KΩ pot | Calibration trimmer — fine-tunes the meter to read accurately |
| **Meter** | 1 mA movement | Analog voltmeter displaying output voltage |

This forms a series-multiplier voltmeter. For a 1 mA full-scale movement to read up to 30V, total series resistance needed is:

$$
R_{total} = \frac{V_{FS}}{I_{FS}} = \frac{30\text{ V}}{1\text{ mA}} = 30{,}000\ \Omega = 30\text{ K}\Omega
$$

Since $R3 = 27\text{K}\Omega$, the remaining $\approx 3\text{K}\Omega$ is trimmed via P2 to precisely calibrate full-scale deflection.
kulang: $C_1$