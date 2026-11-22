# Digispark ATtiny85 Digital Dice — 74HC595 Version (Plan, Wiring & Firmware)

## 1. Project Overview
A standalone **Digital Dice Simulator**. Pressing a push button plays a short rolling animation, then shows a random number from **1 to 6** on a single-digit **common-cathode 7-segment display**.

This version replaces the original design (bare ATtiny85 + 74HC4511 decoder + Arduino Nano programmer) with:

| Original plan | This plan |
| :--- | :--- |
| ATtiny85 chip + Arduino Nano as ISP | **Digispark ATtiny85** board (programs over USB) |
| 74HC4511 BCD decoder | **74HC595** 8-bit shift register |
| 3 BCD lines (P0–P2) | 3 serial lines: DATA, CLOCK, LATCH |
| 220Ω resistors | **330Ω** resistors |

Schematic image: `digispark-595-dice-schematic.svg`

### Key Design Highlights
* **No programmer needed:** the Digispark has a USB bootloader, so no Arduino Nano, ISP wiring or 10µF capacitor.
* **Powered by USB:** the USB plug on the board gives 5V, and the onboard regulator is not involved.
* **Direct segment control:** the 595 outputs map one-to-one to segments `a`–`g`, so the firmware sends a segment pattern instead of BCD bits.
* **Spare output:** `QH` (decimal point) is unused and could drive the DP segment later.

---

## 2. Bill of Materials

| Qty | Component | Notes |
| :--- | :--- | :--- |
| 1 | **Digispark ATtiny85** (USB-A plug type) | Solder the included header pins |
| 1 | **74HC595 / SN74HC595N** | DIP-16 shift register |
| 1 | **7-segment display, single digit, 0.5"** | Choose **common cathode** at purchase |
| 7 | **Resistors 330Ω** | 1/4W, one per segment `a`–`g` |
| 1 | **Ceramic capacitor 0.1µF** | Decoupling, next to the 595 |
| 1 | **Push button** | Tactile momentary switch |
| 1 | **DIP-16 socket** | Optional, recommended |
| 1 | **Breadboard + jumper wires** | For prototyping |
| 1 | **5V USB power source** | Phone charger, power bank or PC port |

---

## 3. Pin Reference

### Digispark pins used
| Digispark pin | ATtiny85 pin | Function in this project |
| :--- | :--- | :--- |
| `P0` | PB0 | DATA to 595 `SER` |
| `P1` | PB1 | CLOCK to 595 `SRCLK` (onboard LED blinks with it, harmless) |
| `P2` | PB2 | LATCH to 595 `RCLK` |
| `P3` | PB3 | Push button (to GND) |
| `5V` / `GND` | — | Power for the rest of the circuit |

### 74HC595 (DIP-16)
```text
            +------U------+
   QB   ----| 1       16 |---- VCC (+5V)
   QC   ----| 2       15 |---- QA
   QD   ----| 3       14 |---- SER (DATA)
   QE   ----| 4       13 |---- /OE (to GND)
   QF   ----| 5       12 |---- RCLK (LATCH)
   QG   ----| 6       11 |---- SRCLK (CLOCK)
   QH   ----| 7       10 |---- SRCLR (to +5V)
   GND  ----| 8        9 |---- QH' (not connected)
            +-------------+
```

### 7-segment display (5161AS-style layout)
| Display pin | Segment |
| :--- | :--- |
| 7 | a |
| 6 | b |
| 4 | c |
| 2 | d |
| 1 | e |
| 9 | f |
| 10 | g |
| 5 | DP (not connected) |
| 3, 8 | COM (to GND) |

Check the markings on the part you receive, since some displays use a different pin order.

---

## 4. Wiring Table

| From | To | Notes |
| :--- | :--- | :--- |
| Digispark `5V` | 595 pin 16 (`VCC`) and pin 10 (`SRCLR`) | Power, and never clear the register |
| Digispark `GND` | 595 pin 8 (`GND`) and pin 13 (`/OE`) | Ground, and outputs always enabled |
| Digispark `P0` | 595 pin 14 (`SER`) | DATA |
| Digispark `P1` | 595 pin 11 (`SRCLK`) | CLOCK |
| Digispark `P2` | 595 pin 12 (`RCLK`) | LATCH |
| Digispark `P3` | Push button leg 1 | Uses internal pull-up |
| Push button leg 2 | `GND` | Pressed = LOW |
| 595 pin 15 (`QA`) | R1 → display `a` (pin 7) | |
| 595 pin 1 (`QB`) | R2 → display `b` (pin 6) | |
| 595 pin 2 (`QC`) | R3 → display `c` (pin 4) | |
| 595 pin 3 (`QD`) | R4 → display `d` (pin 2) | |
| 595 pin 4 (`QE`) | R5 → display `e` (pin 1) | |
| 595 pin 5 (`QF`) | R6 → display `f` (pin 9) | |
| 595 pin 6 (`QG`) | R7 → display `g` (pin 10) | |
| 595 pin 7 (`QH`), pin 9 (`QH'`) | Not connected | Spare |
| Display `COM` (pins 3 and 8) | `GND` | Common cathode |
| 0.1µF capacitor | Across 595 pin 16 and pin 8 | Keep leads short |

### Why 330Ω instead of 220Ω
The 595 can handle about 70mA total through its VCC and GND pins. The digit `6` lights six segments. At 220Ω that is roughly 80mA, which is over the limit. At 330Ω it is roughly 55mA, which is safe.

---

## 5. Firmware (Arduino Sketch)

```cpp
// Digispark ATtiny85 Digital Dice (74HC595 + common-cathode 7-segment)
const byte dataPin   = 0;  // P0 -> 74HC595 pin 14 (SER)
const byte clockPin  = 1;  // P1 -> 74HC595 pin 11 (SRCLK)
const byte latchPin  = 2;  // P2 -> 74HC595 pin 12 (RCLK)
const byte buttonPin = 3;  // P3 -> push button to GND

// Segment patterns for common cathode: bit0 = a ... bit6 = g, bit7 = dp
// Index = digit shown (index 0 is unused by the dice)
const byte segments[7] = {
  0x3F, // 0
  0x06, // 1
  0x5B, // 2
  0x4F, // 3
  0x66, // 4
  0x6D, // 5
  0x7D  // 6
};

void setup() {
  pinMode(dataPin, OUTPUT);
  pinMode(clockPin, OUTPUT);
  pinMode(latchPin, OUTPUT);
  pinMode(buttonPin, INPUT_PULLUP);

  showNumber(1);  // Display '1' on power-up
}

void loop() {
  if (digitalRead(buttonPin) == LOW) {
    // Seed from press timing so every roll is different
    randomSeed(micros());

    // Rolling animation with gradual slowdown
    for (int i = 0; i < 12; i++) {
      showNumber(random(1, 7));
      delay(40 + (i * 15));
    }

    // Final roll (1 through 6)
    showNumber(random(1, 7));

    // Wait for button release before allowing the next roll
    while (digitalRead(buttonPin) == LOW) {
      delay(10);
    }
  }
}

void showNumber(int n) {
  digitalWrite(latchPin, LOW);
  shiftOut(dataPin, clockPin, MSBFIRST, segments[n]);
  digitalWrite(latchPin, HIGH);
}
```

**If your display is common anode:** connect COM to `+5V` instead of GND and change the shift line to `shiftOut(dataPin, clockPin, MSBFIRST, ~segments[n]);`

---

## 6. Step-by-Step Execution Plan

### Phase 1: Prepare Arduino IDE
1. Open **File → Preferences** and add this to *Additional Boards Manager URLs*:
   `https://raw.githubusercontent.com/digistump/arduino-boards-index/master/package_digistump_index.json`
2. Open **Tools → Board → Boards Manager**, search for **Digistump AVR Boards** and install it.
3. Select **Tools → Board → Digistump AVR Boards → Digispark (Default - 16.5mhz)**.
4. Leave the programmer setting alone. The Digispark uploads through its own bootloader.

### Phase 2: Linux USB permissions (Linux Mint and similar)
If the upload fails with a permission or "device not found" error, add a udev rule:

1. Create the file `/etc/udev/rules.d/49-micronucleus.rules` with:
   ```text
   SUBSYSTEMS=="usb", ATTRS{idVendor}=="16d0", ATTRS{idProduct}=="0753", MODE:="0666"
   KERNEL=="ttyACM*", ATTRS{idVendor}=="16d0", ATTRS{idProduct}=="0753", MODE:="0666", ENV{ID_MM_DEVICE_IGNORE}="1"
   ```
2. Reload the rules:
   ```bash
   sudo udevadm control --reload-rules && sudo udevadm trigger
   ```
3. Unplug and replug the Digispark.

### Phase 3: Upload the firmware
1. Paste the sketch from Section 5 into a new sketch.
2. Click **Upload**. **Do not plug the Digispark in yet.**
3. When the IDE shows *"Plug in device now..."*, plug the Digispark into USB (within about 60 seconds).
4. Wait for *"Running Digispark Uploader... Done"*.

### Phase 4: Breadboard build
1. Unplug the Digispark.
2. Place the 595, the display, the resistors, the button and the capacitor on the breadboard using the wiring table in Section 4.
3. Double-check the 595's pin 1 notch orientation and the display's COM connections.

### Phase 5: Test
1. Plug the Digispark into a 5V USB source.
2. Wait about 5 seconds for the bootloader to finish. The display may show random segments until the sketch starts.
3. The display should show `1`. Press the button and confirm the animation plays and the result is always between 1 and 6.

### Phase 6: Permanent build (optional)
1. Solder a DIP-16 socket for the 595, then the display, resistors, capacitor and button.
2. Check with a multimeter for shorts between `5V` and `GND` before inserting the chip.
3. Insert the 595 and test again.

---

## 7. Troubleshooting

| Symptom | Likely cause |
| :--- | :--- |
| Display blank | COM not connected to GND, or 595 pin 13 (`/OE`) not at GND |
| Random or garbled segments | CLOCK and LATCH swapped, or missing 0.1µF capacitor |
| All segments always on | Display is common anode, so use the `~segments[n]` change |
| Wrong segment lights | Resistor or display pin mapped to the wrong 595 output |
| Same roll order every time | Check that `randomSeed(micros())` is in the sketch |
| Upload fails on Linux | Add the udev rule in Phase 2 |
| Nothing for the first few seconds | Normal: the Digispark bootloader waits about 5 seconds |

---

## 8. Battery Option
For battery power, feed about 4.5V (3×AA) into the **`5V` pin**, **not `VIN`**. `VIN` needs about 7V or more because of the onboard regulator. Do not connect USB and a battery at the same time.
