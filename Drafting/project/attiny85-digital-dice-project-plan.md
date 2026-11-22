# ATtiny85 Digital Dice Simulator — Project Plan, Schematics & Execution Guide

## 1. Project Overview
This project builds a standalone, battery- or 5V-powered **Digital Dice Simulator** using an **ATtiny85** microcontroller, a **BCD-to-7-Segment Decoder** (`74HC4511` or `CD4511`), and a **Common-Cathode 7-Segment Display**. Pressing a tactile push button triggers a brief rolling animation followed by a random number between **1 and 6**.

### Key Design Highlights
* **Pin Efficiency:** Because dice values (1–6) only require 3 binary bits (`001` to `110`), the 4th BCD input (`D`) is tied to `GND`, freeing an ATtiny85 pin (`PB4`) for optional future expansion (such as a piezo buzzer).
* **Standalone Operation:** An **Arduino Nano** is used temporarily as an ISP programmer to flash the firmware onto the ATtiny85. Once programmed, the Nano is disconnected and the ATtiny85 runs independently on a breadboard or soldered PCB.

---

## 2. Bill of Materials (Components List)

### Core Circuit Components (Final PCB / Standalone Build)
| Qty | Component | Specification / Notes |
| :--- | :--- | :--- |
| 1 | **Microcontroller** | ATtiny85 (DIP-8 package) |
| 1 | **BCD-to-7-Segment Decoder** | 74HC4511 or CD4511 (DIP-16 package, for Common-Cathode displays) |
| 1 | **7-Segment Display** | Single-digit, **Common-Cathode** (e.g., 5161AS) |
| 1 | **Push Button** | 2-pin or 4-pin tactile momentary switch |
| 7 | **Resistors** | 220Ω to 330Ω (1/4W) for current limiting on display segments `a`–`g` |
| 1 | **Decoupling Capacitor** | 0.1µF (100nF) ceramic capacitor (placed across ATtiny85 VCC and GND) |
| 1 | **IC Socket (8-pin)** | DIP-8 socket (recommended for PCB so the ATtiny85 can be removed/reprogrammed) |
| 1 | **IC Socket (16-pin)** | DIP-16 socket (optional, protects the decoder during PCB soldering) |

### Temporary Programming Tools (Breadboard Setup Only)
| Qty | Component | Specification / Notes |
| :--- | :--- | :--- |
| 1 | **Programmer Board** | Arduino Nano (with Mini-USB/USB-C data cable) |
| 1 | **Electrolytic Capacitor** | 10µF capacitor (connected between Arduino Nano `RST` and `GND` during flashing) |
| 1 | **Breadboard & Jumpers** | Standard solderless breadboard and male-to-male jumper wires |

---

## 3. Schematics & Pinout Diagrams

### A. IC Pinout Reference
```text
        ATtiny85 (DIP-8)                     74HC4511 / CD4511 (DIP-16)
         +-----U-----+                            +------U------+
 (RST) --| 1 PB5 VCC 8|-- +5V           BCD B  ---| 1 B   VDD 16|--- +5V
 (BTN) --| 2 PB3 PB2 7|-- BCD C         BCD C  ---| 2 C     f 15|--- Seg f (via 220Ω)
(FREE) --| 3 PB4 PB1 6|-- BCD B         /LT    ---| 3 /LT   g 14|--- Seg g (via 220Ω)
  GND  --| 4 GND PB0 5|-- BCD A         /BI    ---| 4 /BI   a 13|--- Seg a (via 220Ω)
         +-----------+                  LE     ---| 5 LE    b 12|--- Seg b (via 220Ω)
                                        BCD D  ---| 6 D     c 11|--- Seg c (via 220Ω)
                                        BCD A  ---| 7 A     d 10|--- Seg d (via 220Ω)
                                        GND    ---| 8 VSS   e  9|--- Seg e (via 220Ω)
                                                  +-------------+
```

### B. Stage 1 Schematic: Programming ATtiny85 via Arduino Nano (ISP)
Use this temporary wiring on a breadboard to upload the code to the ATtiny85.

```text
+-------------------+                       +-------------------+
|   Arduino Nano    |                       |     ATtiny85      |
|                   |                       |                   |
|               5V  |---------------------->| Pin 8 (VCC)       |
|              GND  |---------------------->| Pin 4 (GND)       |
|              D10  |---------------------->| Pin 1 (PB5 / RST) |
|              D11  |---------------------->| Pin 5 (PB0 / MOSI)|
|              D12  |---------------------->| Pin 6 (PB1 / MISO)|
|              D13  |---------------------->| Pin 7 (PB2 / SCK) |
|                   |                       +-------------------+
|  RST ---[10µF]--- GND  (Note: Stripe/Negative leg of 10µF cap goes to GND)
+-------------------+
```

### C. Stage 2 Schematic: Standalone Dice Circuit (Breadboard & PCB)
Once programmed, disconnect the Arduino Nano and wire the standalone circuit as shown below.

```text
+5V Power ----+-----------------------------------+-----------------------+
              |                                   |                       |
          [0.1µF Cap]                             |                       |
              |                                   |                       |
GND Power ----+----+                              |                       |
                   |                              |                       |
            +------+------+                +------+------+                |
            |  ATtiny85   |                |  74HC4511   |                |
            |             |                |             |                |
            | 8 VCC   GND 4|--- GND        | 16 VDD  VSS 8|--- GND        |
            |             |                |             |                |
            | 5 PB0 (A)   |--------------->| 7 A         |                |
            | 6 PB1 (B)   |--------------->| 1 B     /LT 3|--- +5V        |
            | 7 PB2 (C)   |--------------->| 2 C     /BI 4|--- +5V        |
            |             |       GND ---->| 6 D      LE 5|--- GND        |
            | 2 PB3 (BTN) |---+            |             |                |
            +-------------+   |            |  Outputs:   |                |
                              |            | a,b,c,d,e,f,g                |
                         [Push Button]     +------+------+                |
                              |                   |                       |
                             GND            [7x 220Ω Resistors]           |
                                                  |                       |
                                                  v                       |
                                        +-------------------+             |
                                        | 7-Segment Display |             |
                                        | (Common Cathode)  |             |
                                        |                   |             |
                                        | COM Pins (3 & 8)  |--- GND      |
                                        +-------------------+
```

---

## 4. Complete Wiring Table (Standalone Circuit)

| From Component & Pin | To Component & Pin | Purpose / Notes |
| :--- | :--- | :--- |
| **Power Supply (+5V)** | ATtiny85 Pin 8 (`VCC`) | Main 5V power |
| **Power Supply (+5V)** | 74HC4511 Pin 16 (`VDD`), Pin 3 (`/LT`), Pin 4 (`/BI`) | Decoder power + disable Lamp Test & Blanking |
| **Power Supply (GND)** | ATtiny85 Pin 4 (`GND`) | Common ground |
| **Power Supply (GND)** | 74HC4511 Pin 8 (`VSS`), Pin 6 (`D`), Pin 5 (`LE`) | Decoder ground + tie MSB (`D`) to 0 + transparent latch |
| **Power Supply (GND)** | 7-Segment Display Common Cathode Pins | Ground return for all 7 LED segments |
| **ATtiny85 Pin 5 (`PB0`)** | 74HC4511 Pin 7 (`A`) | Binary Bit 0 (1s place) |
| **ATtiny85 Pin 6 (`PB1`)** | 74HC4511 Pin 1 (`B`) | Binary Bit 1 (2s place) |
| **ATtiny85 Pin 7 (`PB2`)** | 74HC4511 Pin 2 (`C`) | Binary Bit 2 (4s place) |
| **ATtiny85 Pin 2 (`PB3`)** | Push Button Leg 1 | Dice roll trigger input (uses internal pull-up) |
| **Push Button Leg 2** | Power Supply (`GND`) | Pulls `PB3` LOW when button is pressed |
| **74HC4511 Pins 9–15** | 7-Segment Display Segments `a`–`g` | Connect each segment through a **220Ω–330Ω resistor** |

---

## 5. Firmware Code (Arduino Sketch)

```cpp
// ATtiny85 Digital Dice Simulator (BCD Output for 74HC4511 / CD4511)
const int bitA = 0;      // PB0 (Pin 5) -> Decoder Input A (1s place)
const int bitB = 1;      // PB1 (Pin 6) -> Decoder Input B (2s place)
const int bitC = 2;      // PB2 (Pin 7) -> Decoder Input C (4s place)
const int buttonPin = 3; // PB3 (Pin 2) -> Tactile Push Button

void setup() {
  pinMode(bitA, OUTPUT);
  pinMode(bitB, OUTPUT);
  pinMode(bitC, OUTPUT);
  pinMode(buttonPin, INPUT_PULLUP);

  // Display '1' on initial power-up
  displayNumber(1);
}

void loop() {
  // Continuously advance PRNG while idle so button press timing creates true randomness
  random(1, 7);

  // Check if button is pressed (active LOW)
  if (digitalRead(buttonPin) == LOW) {
    // Rolling animation: cycle random numbers with gradual slowdown
    for (int i = 0; i < 12; i++) {
      displayNumber(random(1, 7));
      delay(40 + (i * 15));
    }

    // Generate and display final roll (1 through 6)
    int roll = random(1, 7);
    displayNumber(roll);

    // Debounce: wait until user releases the button before allowing next roll
    while (digitalRead(buttonPin) == LOW) {
      delay(10);
    }
  }
}

void displayNumber(int num) {
  digitalWrite(bitA, bitRead(num, 0));
  digitalWrite(bitB, bitRead(num, 1));
  digitalWrite(bitC, bitRead(num, 2));
}
```

---

## 6. Step-by-Step Execution Plan

### Phase 1: Prepare Arduino IDE & Arduino Nano Programmer
1. **Install ATtinyCore in Arduino IDE:**
   * Open Arduino IDE → **File** → **Preferences**.
   * In *Additional Boards Manager URLs*, add: `http://drazzy.com/package_drazzy.com_index.json`
   * Go to **Tools** → **Board** → **Boards Manager**, search for **ATtinyCore**, and install it.
2. **Turn Arduino Nano into an ISP Programmer:**
   * Connect the Arduino Nano to your PC via USB (do not connect the ATtiny85 yet).
   * Open **File** → **Examples** → **11.ArduinoISP** → **ArduinoISP**.
   * Select **Tools** → **Board** → **Arduino Nano** (and choose the correct COM port).
   * Click **Upload**.

### Phase 2: Flash Firmware onto the ATtiny85
1. **Wire the ISP Circuit:**
   * Place the ATtiny85 on a breadboard and connect it to the Arduino Nano using the **Stage 1 Schematic** (`D10`–`D13`, `5V`, `GND`).
   * Place a **10µF capacitor** between the Nano's `RST` pin (+) and `GND` (-) to prevent the Nano from resetting during upload.
2. **Configure ATtiny85 Board Settings:**
   * **Tools** → **Board** → **ATtinyCore** → **ATtiny25/45/85 (No bootloader)**
   * **Tools** → **Chip** → **ATtiny85**
   * **Tools** → **Clock Source** → **8 MHz (internal)**
   * **Tools** → **Programmer** → **Arduino as ISP**
3. **Burn Bootloader (Sets Internal Clock Fuses):**
   * Click **Tools** → **Burn Bootloader** (only needed once on a brand-new chip to configure the 8 MHz clock).
4. **Upload Dice Code:**
   * Paste the **Firmware Code** from Section 5 into a new sketch.
   * Click **Sketch** → **Upload Using Programmer** (`Ctrl+Shift+U`).
   * Once you see *"Done uploading"*, unplug the USB cable and remove the Arduino Nano completely.

### Phase 3: Breadboard Prototype Verification
1. **Assemble Standalone Circuit:**
   * Wire the ATtiny85, `74HC4511` decoder, 7-segment display, seven 220Ω resistors, and push button on the breadboard following the **Stage 2 Schematic**.
2. **Power & Test:**
   * Connect a 5V power supply. Verify that the display lights up showing `1`.
   * Press the button several times to confirm the rolling animation plays smoothly and displays numbers strictly between `1` and `6`.

### Phase 4: Permanent PCB Assembly
1. **Component Placement:**
   * Plan the layout on your perfboard or custom PCB, keeping the 7 resistors neatly aligned between the decoder and the 7-segment display.
2. **Solder Sockets First:**
   * Solder the **8-pin DIP socket** (for ATtiny85) and **16-pin DIP socket** (for 74HC4511) onto the PCB without the ICs inserted.
3. **Solder Supporting Components & Traces:**
   * Solder the 7-segment display, 220Ω resistors, 0.1µF decoupling capacitor, push button, and power connector/battery leads.
4. **Continuity Check:**
   * Use a multimeter in continuity mode to verify there are no solder bridges between `5V` and `GND` or adjacent pins before inserting the chips.
5. **Final Assembly & Testing:**
   * Insert the programmed ATtiny85 and the 74HC4511 into their sockets (double-checking the Pin 1 notch orientation).
   * Apply power and test the finished digital dice!
