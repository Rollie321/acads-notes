---
title: Component Testing
aliases: [Testing, Component Tests, Bench Tests]
tags: [project, testing, arduino, hc-sr04, lcd, troubleshooting]
status: planning
---

# Component Testing

**Notes in this project:** [[01-project-overview|Overview]] · [[02-material-requirements|Materials]] · [[03-project-procedure|Procedure]] · [[04-project-simulation|Simulation]] · [[05-component-testing|Component Testing]]

> [!abstract] Purpose
> Test every part **on its own** before running the main code. If you wire everything and the system fails, you will not know which part is at fault. With one part at a time, a failure always points to one thing.

> [!tip] Beginner rule
> Add **one** component, upload **one** small test sketch, confirm it passes, then move on. Do not skip ahead to the pump.

---

## 0. Test order and tools

| Order | Component                      | Test type            | Needs water? |
| ----- | ------------------------------ | -------------------- | ------------ |
| 1     | Arduino Nano and USB           | Blink + Serial       | No           |
| 2     | Start button and mode switch   | Serial print         | No           |
| 3     | I2C LCD                        | Scanner + text       | No           |
| 4     | HC-SR04 ultrasonic sensor      | Ruler check          | No (water later) |
| 5     | Battery pack                   | Multimeter           | No           |
| 6     | Flyback diode                  | Multimeter           | No           |
| 7     | Pump (alone)                   | Direct battery touch | Yes          |
| 8     | MOSFET driver module + pump    | PWM ramp sketch      | Yes          |
| 9     | Common ground                  | Multimeter continuity| No           |
| 10    | Everything together            | Noise and dry run    | Yes          |

**Tools:** multimeter (continuity, DC volts, diode mode), ruler or tape measure, graduated cylinder, a small bowl of water, a screwdriver for the LCD contrast trimmer.

> [!warning] Rules for every test
> - Unplug USB and disconnect the battery **before** changing any wire.
> - Keep water away from the breadboard, LCD and HC-SR04 at all times.
> - Never power the pump from an Arduino pin.
> - Serial Monitor baud rate: **115200** for every sketch below.

---

## 1. Arduino Nano

**Goal:** the board powers up, accepts uploads, and talks over Serial.

```cpp
void setup() {
  pinMode(LED_BUILTIN, OUTPUT);   // D13 on the Nano
  Serial.begin(115200);
  Serial.println("Nano OK");
}

void loop() {
  digitalWrite(LED_BUILTIN, HIGH); delay(500);
  digitalWrite(LED_BUILTIN, LOW);  delay(500);
  Serial.println("blink");
}
```

| Result                                   | Meaning                                         |
| ---------------------------------------- | ----------------------------------------------- |
| Onboard LED blinks, Serial shows `blink` | **Pass**                                        |
| Upload fails                             | Tools → Processor → **ATmega328P (Old Bootloader)**, then check the port and USB cable (some cables are charge-only) |
| No port listed                           | Try another cable or USB port. On Linux, check `ls /dev/ttyUSB*` and that your user is in the `dialout` group |
| Uploads but Serial is garbage            | Baud rate in the Serial Monitor is not 115200   |

> [!note] Linux tip
> If you get "permission denied" on `/dev/ttyUSB0`, run `sudo usermod -aG dialout $USER`, then log out and back in.

---

## 2. Start button (D2) and mode switch (D4)

**Goal:** confirm both inputs read correctly **before** relying on them.

Wiring: button between **D2** and **GND**. Slide switch middle pin to **D4**, one outer pin to **GND**, other outer pin unconnected. See [[03-project-procedure#Connection table]].

```cpp
void setup() {
  Serial.begin(115200);
  pinMode(2, INPUT_PULLUP);   // Start button
  pinMode(4, INPUT_PULLUP);   // Mode switch
}

void loop() {
  int btn = digitalRead(2);
  int sw  = digitalRead(4);
  Serial.print("Button: ");
  Serial.print(btn == LOW ? "PRESSED " : "released ");
  Serial.print(" | Switch: ");
  Serial.println(sw == LOW ? "CLOSED-LOOP (LOW)" : "OPEN-LOOP (HIGH)");
  delay(200);
}
```

| Action                       | Expected output              |
| ---------------------------- | ---------------------------- |
| Nothing touched              | `released`                   |
| Hold the button              | `PRESSED`                    |
| Switch toward the GND pin    | `CLOSED-LOOP (LOW)`          |
| Switch toward the empty pin  | `OPEN-LOOP (HIGH)`           |

> [!tip] If it fails
> - **Button always PRESSED:** one leg is permanently shorted to GND. Many 4-pin buttons connect the two pins on the *same side*, so rotate the button 90°.
> - **Button never changes:** the two wires are on the same internal pair of pins. Rotate the button 90°.
> - **Switch never changes:** you are probably using the two outer pins instead of the **middle** pin. Check with a multimeter in continuity mode.
> - **Output flickers on its own:** the pin is floating. Check that `INPUT_PULLUP` is in the sketch.

---

## 3. I2C LCD 16×2

**Goal:** find the address, show text, and draw the progress bar block used by the main code.

Wiring: GND → GND, VCC → **5V**, SDA → **A4**, SCL → **A5**. Library: **LiquidCrystal I2C** (Frank de Brabander).

### Test 3A: I2C scanner

Upload the scanner from [[03-project-procedure#Check the LCD]].

| Serial output                         | Meaning                                              |
| ------------------------------------- | ---------------------------------------------------- |
| `I2C device found at 0x27`            | **Pass.** Use `0x27` in the code                     |
| `I2C device found at 0x3F`            | **Pass.** Change the code to `LiquidCrystal_I2C lcd(0x3F, 16, 2);` |
| Only `Scan done.`                     | LCD not detected: check power, SDA/SCL swapped, loose jumper |

### Test 3B: text and progress bar

Replace `0x27` below if the scanner found something else.

```cpp
#include <Wire.h>
#include <LiquidCrystal_I2C.h>

LiquidCrystal_I2C lcd(0x27, 16, 2);

void setup() {
  lcd.init();
  lcd.backlight();
  lcd.setCursor(0, 0);
  lcd.print("LCD test OK");
  lcd.setCursor(0, 1);
  lcd.print("0123456789ABCDEF");   // fills all 16 columns
  delay(2000);
}

void loop() {
  // Progress bar test: same block character (255) the main code uses
  for (int cells = 0; cells <= 10; cells++) {
    lcd.setCursor(0, 0);
    lcd.print("Bar test        ");
    lcd.setCursor(0, 1);
    for (int i = 0; i < 10; i++) lcd.write(i < cells ? (uint8_t)255 : '.');
    lcd.print(" ");
    lcd.print(cells * 10);
    lcd.print("%  ");
    delay(400);
  }
  delay(1000);
}
```

| What you see                                | Meaning / fix                                                    |
| ------------------------------------------- | ---------------------------------------------------------------- |
| Text and a filling bar from 0% to 100%      | **Pass**                                                         |
| Backlight on, no text                       | Turn the small blue **contrast trimmer** on the backpack slowly  |
| Row of solid blocks only                    | Contrast too high (or `init()` not reached): turn the trimmer back |
| Completely blank                            | Wrong address (see 3A), SDA/SCL swapped, or VCC on 3.3 V         |
| Garbled or flickering                       | Loose SDA/SCL jumper. Re-seat the wires                          |

---

## 4. HC-SR04 ultrasonic sensor

**Goal:** readings are correct against a ruler and stable enough for the volume calculation.

Wiring: VCC → **5V**, Trig → **D5**, Echo → **D6**, GND → GND.

```cpp
const uint8_t PIN_TRIG = 5, PIN_ECHO = 6;

float pingCm() {
  digitalWrite(PIN_TRIG, LOW);  delayMicroseconds(2);
  digitalWrite(PIN_TRIG, HIGH); delayMicroseconds(10);
  digitalWrite(PIN_TRIG, LOW);
  long us = pulseIn(PIN_ECHO, HIGH, 30000UL);   // 30 ms timeout
  return us * 0.01715;                          // 0 = no echo
}

void setup() {
  Serial.begin(115200);
  pinMode(PIN_TRIG, OUTPUT);
  pinMode(PIN_ECHO, INPUT);
}

void loop() {
  // Take 20 readings and report average, min, max, spread
  float sum = 0, mn = 999, mx = 0;
  int bad = 0;
  for (int i = 0; i < 20; i++) {
    float d = pingCm();
    if (d == 0) { bad++; }
    else { sum += d; if (d < mn) mn = d; if (d > mx) mx = d; }
    delay(60);
  }
  int good = 20 - bad;
  if (good == 0) { Serial.println("NO ECHO on any reading"); }
  else {
    Serial.print("avg="); Serial.print(sum / good, 2);
    Serial.print(" cm  min="); Serial.print(mn, 2);
    Serial.print("  max="); Serial.print(mx, 2);
    Serial.print("  spread="); Serial.print(mx - mn, 2);
    Serial.print("  no-echo="); Serial.println(bad);
  }
  delay(500);
}
```

### Test 4A: accuracy against a ruler

Point the sensor at a flat wall or book. Measure the real distance with a ruler from the **front of the sensor** to the surface.

| Real distance | Acceptable reading (`avg`) |
| ------------- | -------------------------- |
| 10 cm         | 9.5 to 10.5 cm             |
| 20 cm         | 19.5 to 20.5 cm            |
| 30 cm         | 29.5 to 30.5 cm            |

### Test 4B: stability

Hold the sensor still over a flat surface at about 15 to 20 cm.

- **Pass:** `spread` is **0.3 cm or less** and `no-echo=0`.
- This matches the resolution assumed in [[01-project-overview#Sensor resolution]] (about ±3 mm).

### Test 4C: sensitivity to the water level

Point the sensor straight down into the empty glass, as it will be mounted. Record `avg`. Add **100 ml** and record again.

- The distance should **decrease** by about $100 / A$ cm (for a 28 cm² glass, about 3.6 cm).
- This is the same measurement as the area calibration in [[03-project-procedure#Calibrate the HC-SR04]]. If it works here, you can reuse the numbers.

| Problem                               | Likely cause / fix                                                      |
| ------------------------------------- | ----------------------------------------------------------------------- |
| Always `NO ECHO`                      | No 5 V on VCC, Trig/Echo swapped, or a loose wire                       |
| Reads the same value always           | Trig/Echo swapped, or the echo wire is not on D6                        |
| Large spread (more than 1 cm)         | Sensor not pointing straight, glass rim or tube inside the beam, or an object within about 5 cm |
| Reading too small / too large by a constant | Ruler measured from the wrong edge, or temperature differs (small effect) |
| Wrong only over water                 | Surface rippling. Wait a few seconds, or aim the stream at the inner glass wall |

> [!important] Minimum distance
> The HC-SR04 is unreliable below about 2 to 3 cm. Keep the sensor **5 to 10 cm above the full water level**.

---

## 5. Battery pack (4 × AA)

**Goal:** make sure the pump supply voltage and polarity are right **before** connecting anything.

Multimeter on **DC volts**, red probe on the pack **+** wire, black probe on the **−** wire.

| Reading            | Meaning                                                      |
| ------------------ | ------------------------------------------------------------ |
| About 5.8 to 6.6 V | **Pass** (fresh alkaline cells)                              |
| Below about 5 V    | Weak batteries: the pump will be weak and open-loop error will grow |
| Negative number    | Probes are on the wrong wires (the pack itself is fine)      |
| 0 V                | Batteries inserted the wrong way, a dead cell, or a bad holder contact |

> [!tip] Useful for the disturbance test
> Measure and write down the battery voltage at the start of every run. A drop in voltage is the explanation for a weaker pump in your report.

---

## 6. Flyback diode (1N4007 or 1N5819)

**Goal:** confirm the diode works and know which end is which.

Multimeter in **diode mode** (the symbol ▶|).

| Probe placement                                | Expected reading                         |
| ---------------------------------------------- | ---------------------------------------- |
| Red on the **non-striped** end (anode), black on the **striped** end (cathode) | About 0.5 to 0.7 V (1N4007), about 0.2 to 0.4 V (1N5819) |
| Probes swapped                                 | `OL` or no reading (blocks current)      |

- **Pass:** reads in one direction only.
- **Fail:** reads in both directions (shorted) or neither direction (open). Replace it.

When installed, the **striped end (cathode)** goes to the pump **(+)** wire. See [[02-material-requirements#Pump driver (MOSFET module)]].

---

## 7. Pump (alone, no electronics)

**Goal:** confirm the pump and tubing work before involving the driver module.

> [!danger] Do not run dry
> Fully submerge the pump in the reservoir first. A dry pump overheats and wears out quickly.

1. Put the pump in a bowl of water with the tube outlet over a second container or the sink.
2. Briefly touch the pump wires to the battery pack: pump **(+)** to battery **+**, pump **(−)** to battery **−**.
3. Water should flow out of the tube within about a second.

| Result                       | Meaning / fix                                                    |
| ---------------------------- | ---------------------------------------------------------------- |
| Water flows steadily         | **Pass**                                                         |
| Pump hums but no water       | Air in the pump or tube. Submerge fully and tilt to release air  |
| Pump spins the wrong way / no flow | Swap the two wires                                         |
| Nothing at all               | Dead pump, bad battery contact, or a broken wire                 |
| Weak flow                    | Weak batteries (check section 5) or a kinked or too-long tube    |

### Optional: first flow estimate

Run the pump from the battery for exactly **10 s** into the graduated cylinder (use a stopwatch) and compute $\hat{K} = V_m / T$. This gives a first value for `FLOW_ML_PER_S`; see [[03-project-procedure#Measure the pump flow rate (open-loop estimate)]].

---

## 8. MOSFET driver module and pump (with Arduino PWM)

**Goal:** the Nano can switch and speed-control the pump through the module.

Wiring from [[03-project-procedure#Connection table]]: D9 → module PWM, Nano GND → module signal GND, battery **+/−** → module power in, module OUT+/OUT− → pump, battery − also to Nano GND, flyback diode across the pump. The pump goes in the water.

```cpp
const uint8_t PIN_PUMP = 9;

void setup() {
  Serial.begin(115200);
  pinMode(PIN_PUMP, OUTPUT);
  analogWrite(PIN_PUMP, 0);          // pump OFF
  Serial.println("Pump test: starts in 3 s");
  delay(3000);
}

void loop() {
  // Ramp up
  for (int pwm = 0; pwm <= 255; pwm += 51) {      // 0, 51, 102, 153, 204, 255
    Serial.print("PWM = "); Serial.println(pwm);
    analogWrite(PIN_PUMP, pwm);
    delay(2000);
  }
  // Full power, then stop
  analogWrite(PIN_PUMP, 0);
  Serial.println("OFF for 4 s");
  delay(4000);
}
```

| What you observe                                    | Meaning                                                    |
| --------------------------------------------------- | ---------------------------------------------------------- |
| Pump speeds up as PWM rises and stops at 0          | **Pass**                                                   |
| Pump does not start until PWM is fairly high (around 100 or more) | Normal for small pumps (this is why `PWM_SLOW` must not be too low) |
| Pump never turns on                                 | See the table below                                        |
| Pump runs at boot or does not stop at PWM = 0       | Floating PWM input: add a **10 kΩ resistor from module PWM input to GND** |
| Nano resets or the Serial output stops when the pump starts | Power problem: keep the battery pack **separate from USB power**, check common ground and wiring polarity |

| Pump never turns on: check this | How                                                        |
| ------------------------------- | ---------------------------------------------------------- |
| Module power                    | Multimeter across module power in: should read about 6 V   |
| Module output                   | Multimeter across OUT+ / OUT− while PWM = 255: should read close to the battery voltage |
| Trigger voltage                 | Some modules need more than a 5 V signal. Check the datasheet |
| Terminals                       | Load wires on the power-in terminals, or the other way round |
| Common ground                   | Nano GND must connect to battery − (see section 9)         |

> [!tip] Quick module check without the Arduino
> With the battery connected to the module, briefly touch the module's PWM/signal input to the Nano **5V** pin (with Nano GND tied to the module signal GND). The pump should run while you hold it and stop when you release. If it does, the module and pump are fine and the problem is on D9 or in the sketch.

---

## 9. Common ground check

**Goal:** all grounds are joined. A missing common ground is the most common reason for an ignored PWM signal.

Unplug everything (USB and battery). Multimeter in **continuity mode** (beep):

| Probe A                      | Probe B                        | Expected |
| ---------------------------- | ------------------------------ | -------- |
| Nano GND                     | Battery pack − wire            | Beep     |
| Nano GND                     | HC-SR04 GND                    | Beep     |
| Nano GND                     | LCD GND                        | Beep     |
| Nano GND                     | Module signal GND              | Beep     |
| Nano 5V                      | Nano GND                       | **No beep** (a beep means a short circuit) |
| Battery pack +               | Battery pack −                 | **No beep** (a beep means a short circuit) |

> [!danger]
> If either of the last two rows beeps, **stop** and find the short before powering anything.

---

## 10. Everything together

### Test 10A: dry run (no water)

Upload the main code from [[03-project-procedure#Phase 5 Main Arduino code]] with `SIMULATE 0`, `USE_LCD 1`. Leave the pump **in the reservoir** with the tube outlet pointing at the sink or a bowl, away from the sensor.

1. Empty glass in place. Switch to **open-loop**, press **Start**.
2. Check that: the LCD shows `Measuring...`, then the progress screen; Serial prints `MODE: OPEN-LOOP` and CSV lines; the pump runs; the run ends with a `RESULT` line.
3. Repeat with the switch on **closed-loop**.

### Test 10B: noise test

Run the pump at full power (section 8 sketch or a real run) while watching the LCD and Serial.

| Observation                          | Meaning                                              |
| ------------------------------------ | ---------------------------------------------------- |
| LCD stays clean, distance stays stable | **Pass**                                           |
| LCD flickers or shows garbage when the pump starts | Electrical noise or a weak 5 V: re-seat SDA/SCL, keep the pump power wires away from the signal wires, make sure the pump is on the **separate battery pack** |
| Distance readings jump only while the pump runs | Water ripples or the tube is in the sensor beam: aim the tube at the inner glass wall and add the splash guard |

### Test 10C: sanity check against the real volume

Run one closed-loop run to 100 ml and pour the water into the graduated cylinder. Compare it with the Arduino reading. If they differ by more than about **±8 ml**, redo the area calibration.

---

## Summary checklist

Tick each row only when the test passes.

- [ ] **1.** Nano: LED blinks, Serial works
- [ ] **2.** Button reads PRESSED / released correctly
- [ ] **2.** Switch reads CLOSED-LOOP (GND side) and OPEN-LOOP correctly
- [ ] **3.** LCD address found, text visible, progress bar fills
- [ ] **4.** HC-SR04 within ±0.5 cm of a ruler, spread 0.3 cm or less
- [ ] **4.** HC-SR04 distance drops by about $100/A$ cm when 100 ml is added
- [ ] **5.** Battery pack reads about 6 V with correct polarity
- [ ] **6.** Flyback diode conducts in one direction only
- [ ] **7.** Pump pumps water when connected directly to the battery
- [ ] **8.** Pump speed follows PWM through the driver module and stops at 0
- [ ] **9.** Common ground confirmed, no shorts
- [ ] **10.** Dry run completes in both modes
- [ ] **10.** No LCD or sensor glitches while the pump runs

> [!success] Report tip
> Photograph a few of these tests (the LCD bar, the Serial output of the sensor test, the multimeter reading) and add one short paragraph to the **Prototype development and procedure** section. It shows that each component was verified, which supports the *Functional prototype* part of the rubric.
