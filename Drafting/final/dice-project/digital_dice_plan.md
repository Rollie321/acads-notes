# Digital Dice: 555 + 74LS161 + 74LS48 + 7-Segment

A push-button electronic die built from discrete TTL logic, with no microcontroller. Hold the button and the display spins. Release it and the display stops on a number from 1 to 6.

Schematic image: `digital_dice_schematic.png` (and `.svg`).

---

## 1. Plan

### How it works

| Block | Part | Job |
|---|---|---|
| Clock | NE555 (astable) | Makes about 150 Hz pulses, but only while SW1 is held (SW1 drives the 555 RESET pin). |
| Counter | 74LS161 (4-bit synchronous counter) | Counts the pulses. Outputs QA to QD give a binary number. |
| Wrap logic | 74LS00 (one NAND gate) | When the count reaches 6 (QB and QC both high), it pulls /LOAD low. On the next clock the counter loads 0001 instead of counting to 7. The sequence is 1, 2, 3, 4, 5, 6, 1, ... |
| Decoder | 74LS48 | Converts the 4-bit count to the 7 segment signals. Outputs are active high with internal 2 kΩ pull-ups, so it drives a common-cathode display directly. |
| Display | 1-digit common-cathode 7-segment | Shows the number. The 74LS48 pull-ups limit each segment to about 1.5 mA, so no external resistors are used. |
| Power | 9 V battery + 7805 | Regulated 5 V for the TTL parts (TTL needs 5 V ±5%). |

### Why the button works as a roll control

- Pin 4 (RESET) of the 555 is held low by R3 (10k), so the 555 is stopped.
- Pressing SW1 connects pin 4 to +5V, so the 555 oscillates and the counter spins.
- Releasing SW1 stops the 555 with its output low, and the counter holds whatever number it was on.
- The roll is random because nobody can time a 150 Hz spin.

### Clock frequency

```
f = 1.44 / ((R1 + 2·R2) · C1)
  = 1.44 / ((1k + 2·47k) · 100nF)
  ≈ 150 Hz
```

- Want a slower, visible flicker? Raise C1 (1 µF gives about 15 Hz).
- Want a faster blur? Lower C1 (10 nF gives about 1.5 kHz).

### Power-up behaviour

On power-up the 74LS161 can start on 0 or on 8 to 15. Every one of those states returns to the 1 to 6 loop within a few clocks, so the first button press always ends on a valid number.

---

## 2. Connections

All pin numbers are for standard DIP packages. Every IC gets a 100 nF capacitor across its Vcc and GND pins, placed close to the chip.

### U1: NE555 (DIP-8)

| Pin | Name | Connects to |
|---|---|---|
| 1 | GND | GND |
| 2 | TRIG | Pin 6, and C1 (+ side) |
| 3 | OUT | U2 pin 2 (CLK) |
| 4 | RESET | SW1 to +5V, and R3 (10k) to GND |
| 5 | CTRL | C2 (10 nF) to GND |
| 6 | THRES | Pin 2, and R2 |
| 7 | DISCH | Junction of R1 and R2 |
| 8 | Vcc | +5V |

Timing network:
- R1 (1k): +5V to pin 7
- R2 (47k): pin 7 to pins 2 and 6
- C1 (100 nF): pins 2 and 6 to GND

### U2: 74LS161 (DIP-16)

| Pin | Name | Connects to |
|---|---|---|
| 1 | /CLR | +5V |
| 2 | CLK | U1 pin 3 (555 output) |
| 3 | A | +5V (this is the "1" that gets loaded) |
| 4 | B | GND |
| 5 | C | GND |
| 6 | D | GND |
| 7 | ENP | +5V |
| 8 | GND | GND |
| 9 | /LOAD | U3 pin 3 (NAND output) |
| 10 | ENT | +5V |
| 11 | QD | U4 pin 6 (D) |
| 12 | QC | U4 pin 2 (C), and U3 pin 2 |
| 13 | QB | U4 pin 1 (B), and U3 pin 1 |
| 14 | QA | U4 pin 7 (A) |
| 15 | RCO | Not connected |
| 16 | Vcc | +5V |

### U3: 74LS00 (DIP-14)

| Pin | Name | Connects to |
|---|---|---|
| 1 | 1A | U2 pin 13 (QB) |
| 2 | 1B | U2 pin 12 (QC) |
| 3 | 1Y | U2 pin 9 (/LOAD) |
| 4, 5, 9, 10, 12, 13 | Unused inputs | GND |
| 6, 8, 11 | Unused outputs | Not connected |
| 7 | GND | GND |
| 14 | Vcc | +5V |

### U4: 74LS48 (DIP-16)

| Pin | Name | Connects to |
|---|---|---|
| 1 | B | U2 pin 13 (QB) |
| 2 | C | U2 pin 12 (QC) |
| 3 | /LT | +5V |
| 4 | /BI /RBO | +5V |
| 5 | /RBI | +5V |
| 6 | D | U2 pin 11 (QD) |
| 7 | A | U2 pin 14 (QA) |
| 8 | GND | GND |
| 9 | e | Display segment e (direct) |
| 10 | d | Display segment d (direct) |
| 11 | c | Display segment c (direct) |
| 12 | b | Display segment b (direct) |
| 13 | a | Display segment a (direct) |
| 14 | g | Display segment g (direct) |
| 15 | f | Display segment f (direct) |
| 16 | Vcc | +5V |

### DS1: common-cathode 7-segment display

- Common cathode pin(s) to GND.
- Each segment pin a to g goes straight to the matching 74LS48 output (table above). No series resistors: the 74LS48's internal 2 kΩ pull-ups limit the current to about 1.5 mA per lit segment.
- Leave the decimal point unconnected.
- Pinouts vary between display models. Check your display's datasheet. A common 10-pin layout has the commons on pins 3 and 8, but do not assume it.
- Do not use a common-anode display with the 74LS48. It needs the 74LS47 instead.

### Power: 9 V battery to 5 V

| From | To |
|---|---|
| BT1 (+) | SW2, then U5 IN |
| C7 (330 nF) | U5 IN to GND |
| U5 GND | GND (battery −) |
| U5 OUT | +5V rail |
| C8 (100 nF) and C9 (100 µF) | +5V rail to GND (C9 polarised: + on the +5V side) |

The 7805 (TO-220, front view, label facing you) has pins in the order IN, GND, OUT from left to right.

---

## 3. Component list

| Ref | Part | Value / type | Qty |
|---|---|---|---|
| U1 | NE555 timer | DIP-8 (or NE555P) | 1 |
| U2 | 74LS161 | Synchronous 4-bit binary counter, DIP-16 | 1 |
| U3 | 74LS00 | Quad 2-input NAND, DIP-14 | 1 |
| U4 | 74LS48 | BCD to 7-segment decoder/driver (active-high outputs, common cathode), DIP-16 | 1 |
| U5 | 7805 | 5 V regulator, TO-220 | 1 |
| DS1 | 7-segment display | 1 digit, common cathode | 1 |
| R1 | Resistor | 1 kΩ, 1/4 W | 1 |
| R2 | Resistor | 47 kΩ, 1/4 W | 1 |
| R3 | Resistor | 10 kΩ, 1/4 W | 1 |
| C1 | Capacitor | 100 nF ceramic (timing) | 1 |
| C2 | Capacitor | 10 nF ceramic (555 CTRL) | 1 |
| C3 to C6 | Capacitor | 100 nF ceramic (decoupling, one per IC) | 4 |
| C7 | Capacitor | 330 nF ceramic (regulator input) | 1 |
| C8 | Capacitor | 100 nF ceramic (regulator output) | 1 |
| C9 | Capacitor | 100 µF electrolytic, 16 V or higher | 1 |
| SW1 | Push button | Normally open (the ROLL button) | 1 |
| SW2 | Switch | SPST slide or toggle (power) | 1 |
| BT1 | Battery | 9 V (PP3) with snap connector | 1 |
| — | Breadboard or perfboard | Full-size breadboard, plus jumper wires | 1 |
| — | IC sockets (optional) | DIP-8, DIP-14, 2× DIP-16 | 4 |

Totals: 5 ICs, 1 display, 3 resistors, 9 capacitors (C1 to C9), 2 switches, 1 battery.

Alternative supply: a USB 5 V source (for example a phone charger with a cable cut-off or a breakout board) replaces BT1, SW2, U5 and C7 to C9. Keep the 100 nF caps on each IC.

---

## 4. Build order

1. **Power first.** Wire BT1, SW2, U5, C7, C8, C9. Check the +5V rail with a multimeter before connecting any IC. It should read 4.9 to 5.1 V.
2. **Clock.** Place U1 with R1, R2, C1, C2, R3 and SW1. Put an LED with a 330 Ω resistor from pin 3 to GND. It should flicker while SW1 is held and go dark when released.
3. **Counter and wrap logic.** Add U2 and U3. Check that each press ends on a different value, and that the Q outputs only ever show 1 to 6 (binary 0001 to 0110).
4. **Decoder and display.** Add U4 and DS1 (no segment resistors needed). Hold SW1, release it, and read the digit.
5. **Decoupling.** Add the 100 nF capacitors on each IC if you have not already.

---

## 5. Troubleshooting

| Symptom | Likely cause |
|---|---|
| Display is blank | Display is common anode instead of common cathode, or the common pin is not at GND. |
| Display shows odd shapes, not digits | Check the QA to QD wires into U4 against the table. A swapped pair gives scrambled digits. |
| Display lights segments at 7 to 15 | Counter is not wrapping. Check U3 pins 1, 2, 3 and the U2 pin 9 wire. |
| Shows 0 after a roll | The counter is not reloading to 1. Check U2 pin 3 is at +5V and pins 4, 5, 6 are at GND. |
| Never stops spinning | SW1 stuck, or R3 missing so pin 4 floats high. |
| Never spins | 555 not oscillating: check C1, R1, R2, and that pins 2 and 6 are joined. |
| Random resets or flicker | Missing or distant decoupling capacitors. Add 100 nF directly across each IC's Vcc and GND. |
| Display is dim | Expected to a degree: the 74LS48 gives only about 1.5 mA per segment. Use a bright (high-efficiency) display, or see the 74HC4511 variant below. |
| 7805 gets warm | Normal. The display and TTL draw roughly 40 to 70 mA total. A 9 V battery will run down in a few hours, so USB power is better for long use. |

---

## 6. Variants

- **Brighter display (74HC4511):** Replace U4 with a 74HC4511 (or CD4511). The pin numbers for A to D (pins 7, 1, 2, 6), the segments a to g (pins 13, 12, 11, 10, 9, 15, 14), GND (8) and Vcc (16) are the same as the 74LS48. Tie pin 3 (/LT) and pin 4 (/BI) to +5V and pin 5 (LE) to GND. Its outputs can source about 25 mA, so put 330 Ω resistors (R4 to R10, about 8 mA per segment) between each output and the display. Also add 4.7 kΩ pull-ups from U2 pins 11 to 14 (QD to QA) to +5V, because a 74LS output may not reach the 4511's input-high level (about 3.5 V).
- **Full CMOS version:** Use a 74HC161, a 74HC00 and a 74HC4511 (as above, without the pull-ups). The NE555's high output is only about 3.3 V at 5 V, which is too low for HC inputs, so also use a CMOS 555 (TLC555 or LMC555). The wrap logic stays the same.
- **Two dice:** Duplicate U2, U3 gate, U4 and DS1, and share the 555 clock. Use a slightly different wrap or a second 555 so the two dice are not locked together.
