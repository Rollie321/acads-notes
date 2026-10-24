---
title: Component Testing
aliases: [Testing, Component Tests, Bench Tests]
tags: [project, testing, arduino, hc-sr04, pump, troubleshooting]
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
| 3     | HC-SR04 ultrasonic sensor      | Ruler check          | No (water later) |
| 4     | Battery pack                   | Multimeter           | No           |
| 5     | Flyback diode                  | Multimeter           | No           |
| 6     | Pump (alone)                   | Direct battery touch | Yes          |
| 7     | Isolated MOSFET module + pump  | Pump test sketch     | Yes          |
| 8     | Grounds and isolation          | Multimeter continuity| No           |
| 9     | Everything together            | Noise and dry run    | Yes          |

**Tools:** multimeter (continuity, DC volts, diode mode), ruler or tape measure, graduated cylinder, a small bowl of water, and a soldering iron for the MOSFET module's header and screw terminal.

> [!warning] Rules for every test
> - Unplug USB and disconnect the battery **before** changing any wire.
> - Keep water away from the breadboard and HC-SR04 at all times.
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

## 3. HC-SR04 ultrasonic sensor

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

### Test 3A: accuracy against a ruler

Point the sensor at a flat wall or book. Measure the real distance with a ruler from the **front of the sensor** to the surface.

| Real distance | Acceptable reading (`avg`) |
| ------------- | -------------------------- |
| 10 cm         | 9.5 to 10.5 cm             |
| 20 cm         | 19.5 to 20.5 cm            |
| 30 cm         | 29.5 to 30.5 cm            |

### Test 3B: stability

Hold the sensor still over a flat surface at about 15 to 20 cm.

- **Pass:** `spread` is **0.3 cm or less** and `no-echo=0`.
- This matches the resolution assumed in [[01-project-overview#Sensor resolution]] (about ±3 mm).

### Test 3C: sensitivity to the water level

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

## 4. Battery pack (4 × AA)

**Goal:** make sure the pump supply voltage and polarity are right **before** connecting anything.

Multimeter on **DC volts**, red probe on the pack **+** wire, black probe on the **−** wire.

| Reading            | Meaning                                                      |
| ------------------ | ------------------------------------------------------------ |
| About 6.0 to 6.6 V | **Pass** (fresh alkaline cells)                              |
| Below about 6 V    | **Marginal for the MOSFET module**: one retailer lists a 6 V minimum load supply for the LR7843 version. Replace the batteries |
| Negative number    | Probes are on the wrong wires (the pack itself is fine)      |
| 0 V                | Batteries inserted the wrong way, a dead cell, or a bad holder contact |

> [!tip] Record it, but do not use it as the disturbance
> Measure and write down the battery voltage at the start of every run, and once more while the pump is running (the voltage sags under load). Use a pinched tube or a height change as the disturbance instead, because a pack below the module's minimum can make the pump switch erratically rather than just run slower.

---

## 5. Flyback diode (1N4007 or 1N5819)

**Goal:** confirm the diode works and know which end is which.

Multimeter in **diode mode** (the symbol ▶|).

| Probe placement                                | Expected reading                         |
| ---------------------------------------------- | ---------------------------------------- |
| Red on the **non-striped** end (anode), black on the **striped** end (cathode) | About 0.5 to 0.7 V (1N4007), about 0.2 to 0.4 V (1N5819) |
| Probes swapped                                 | `OL` or no reading (blocks current)      |

- **Pass:** reads in one direction only.
- **Fail:** reads in both directions (shorted) or neither direction (open). Replace it.

When installed, the **striped end (cathode)** goes to the module's **+** terminal (the pump (+) side) and the other end to the **LOAD** terminal (the pump (−) side). See [[02-material-requirements#Pump driver (isolated MOSFET module)]].

---

## 6. Pump (alone, no electronics)

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
| Weak flow                    | Weak batteries (check section 4) or a kinked or too-long tube    |

### Optional: first flow estimate

Run the pump from the battery for exactly **10 s** into the graduated cylinder (use a stopwatch) and compute $\hat{K} = V_m / T$. This gives a first value for `FLOW_ML_PER_S`; see [[03-project-procedure#Measure the pump flow rate (open-loop estimate)]].

---

## 7. MOSFET driver module and pump (with Arduino PWM)

**Goal:** the Nano can switch and speed-control the pump through the isolated module.

**Before wiring:** solder the 2-pin header into the **PWM / GND** pads and the 3-pin screw terminal into the three load pads. The terminals are marked **+ / LOAD / −**, with LOAD in the middle.

Wiring from [[03-project-procedure#Connection table]]:

| Wire                          | Goes to                                                   |
| ----------------------------- | --------------------------------------------------------- |
| Nano **D9**                   | Module **PWM** pad                                        |
| Nano **GND**                  | Module signal **GND** pad                                 |
| Battery pack **+**            | Module **+** terminal                                     |
| Pump **(+)** wire             | Module **+** terminal (the same terminal as the battery +) |
| Pump **(−)** wire             | Module **LOAD** terminal                                  |
| Battery pack **−**            | Module **−** terminal                                     |
| Flyback diode                 | Across the pump: striped end to **+**, other end to **LOAD** |

> [!important] No common ground
> Do **not** connect the battery − to the Nano GND. The optocoupler isolates the two sides, and the board does not connect the signal ground and load ground together.

The pump goes in the water. Do not leave the battery connected while you move wires.

Upload the sketch below (also saved as `pump_driver_test.ino`), open the Serial Monitor at 115200 baud, and send `r` for the ramp test or `f` for full power.

```cpp
/*
  Pump and driver test (no sensor, button or switch needed)

  Wiring:
    Nano D9  -> module PWM pad
    Nano GND -> module signal GND pad
    6 V supply + -> module + terminal (and pump + wire in the same terminal)
    Pump (-) wire -> module LOAD terminal
    6 V supply - -> module - terminal
    Flyback diode across the pump (striped end on the + side)
  The pump must be in water. Do not run it dry.

  Open the Serial Monitor at 115200 baud and send one character:
    0 or s = stop            1 to 9 = about 11% to 99% power
    f      = full power      r      = ramp test (0, 20, 40, 60, 80, 100%)
    p      = pulse test (3 x 2 s ON, 2 s OFF)         h = show this help
  Safety: the pump stops by itself 10 s after the last command.
*/

const uint8_t PIN_PUMP = 9;
const unsigned long AUTO_STOP_MS = 10000UL;   // safety stop (avoids running dry)

int pwmNow = 0;
unsigned long lastCmd = 0;

void setPump(int pwm) {
  pwmNow = pwm;
  analogWrite(PIN_PUMP, pwm);
  lastCmd = millis();
  Serial.print("PWM = ");
  Serial.print(pwm);
  Serial.print("  (");
  Serial.print(pwm * 100L / 255);
  Serial.println("%)");
}

void rampTest() {
  Serial.println("Ramp test: 0, 20, 40, 60, 80, 100 percent, 2 s each");
  for (int pwm = 0; pwm <= 255; pwm += 51) {
    setPump(pwm);
    delay(2000);
  }
  setPump(0);
  Serial.println("Ramp done.");
}

void pulseTest() {
  Serial.println("Pulse test: 3 x (2 s ON at full power, 2 s OFF)");
  for (int i = 0; i < 3; i++) {
    setPump(255); delay(2000);
    setPump(0);   delay(2000);
  }
  Serial.println("Pulse test done.");
}

void printHelp() {
  Serial.println();
  Serial.println("Pump test. Send: 0/s stop, 1-9 speed, f full, r ramp, p pulse, h help");
  Serial.println("Pump auto-stops 10 s after the last command.");
}

void setup() {
  Serial.begin(115200);
  pinMode(PIN_PUMP, OUTPUT);
  analogWrite(PIN_PUMP, 0);                  // pump OFF at boot
  printHelp();
}

void loop() {
  if (Serial.available()) {
    char c = Serial.read();
    if (c >= '1' && c <= '9')      setPump((c - '0') * 28);   // 28 .. 252
    else if (c == '0' || c == 's') setPump(0);
    else if (c == 'f')             setPump(255);
    else if (c == 'r')             rampTest();
    else if (c == 'p')             pulseTest();
    else if (c == 'h')             printHelp();
  }
  if (pwmNow > 0 && millis() - lastCmd > AUTO_STOP_MS) {
    setPump(0);
    Serial.println("Auto-stop after 10 s. Send a key to run again.");
  }
}
```

**Keys to send in the Serial Monitor (115200 baud):**

| Key       | What it does                                                    |
| --------- | --------------------------------------------------------------- |
| `1` to `9`| Pump at about 11% to 99% power                                  |
| `f`       | Full power                                                      |
| `0` or `s`| Stop                                                            |
| `r`       | Ramp test: 0, 20, 40, 60, 80, 100%, two seconds each, then off  |
| `p`       | Pulse test: three times 2 s on, 2 s off, at full power          |
| `h`       | Show the key list                                               |

The sketch also stops the pump **10 s after the last command**, so a forgotten test cannot run the pump dry. The same sketch is saved as `pump_driver_test.ino`.

| What you observe                                    | Meaning                                                    |
| --------------------------------------------------- | ---------------------------------------------------------- |
| Pump speeds up as PWM rises and stops at 0          | **Pass**                                                   |
| Pump does not start until PWM is fairly high (around 100 or more) | Normal for small pumps (this is why `PWM_SLOW` must not be too low) |
| Speed does not rise smoothly with PWM               | Possible with an optocoupler input (D9's PWM is about 490 Hz). Test your `PWM_SLOW` zone before relying on it |
| Pump never turns on                                 | See the table below                                        |
| Pump runs all the time, even at PWM = 0, slightly weaker | Typically the pump (−) and battery (−) wires are swapped between **LOAD** and **−**. Disconnect the battery and swap them |
| Pump runs at boot or will not stop (rare)           | Add a **10 kΩ resistor from the PWM pad to the signal GND pad** |
| Nano resets or the Serial output stops when the pump starts | Keep the battery pack **separate from USB power**, check the wiring polarity, and make sure the battery − is not tied to the Nano GND |

| Pump never turns on: check this | How                                                        |
| ------------------------------- | ---------------------------------------------------------- |
| Supply voltage                  | Multimeter across the module **+** and **−** terminals: at least about 6 V (a retailer lists 6 V as the minimum), measured with the pump running |
| Module output                   | With PWM = 255, measure across the pump wires: it should read close to the battery voltage. 0 V means the MOSFET is not turning on |
| Signal side                     | D9 on the **PWM** pad and Nano GND on the module's own **GND** pad (not the battery −) |
| Terminals                       | Pump (−) on **LOAD**, battery − on **−**, battery + and pump (+) both on **+** |
| Joints                          | Solder joints on the header and screw terminal, and the screws actually clamping the wires |

> [!tip] Quick module check without the Arduino
> With the battery and pump connected as above, briefly touch the module's **PWM** pad to the Nano **5V** pin, with Nano GND on the module's **GND** pad. The pump should run while you hold it and stop when you release. If it does, the module and pump are fine and the problem is on D9 or in the sketch.

---

## 8. Grounds and isolation check

**Goal:** the Nano-side grounds are joined, the battery side stays separate, and nothing is shorted.

Unplug everything (USB and battery). Multimeter in **continuity mode** (beep):

| Probe A                      | Probe B                        | Expected |
| ---------------------------- | ------------------------------ | -------- |
| Nano GND                     | HC-SR04 GND                    | Beep     |
| Nano GND                     | Module signal **GND** pad      | Beep     |
| Nano GND                     | Battery pack − wire            | **No beep** (separate on purpose) |
| Module signal **GND** pad    | Module **−** terminal          | **No beep** (the optocoupler isolates them) |
| Nano 5V                      | Nano GND                       | **No beep** (a beep means a short circuit) |
| Battery pack +               | Battery pack −                 | **No beep** (a beep means a short circuit) |

> [!danger]
> If either of the last two rows beeps, **stop** and find the short before powering anything.

> [!note] If a separate-ground row beeps
> The circuit can still run, but the two sides are then connected, so the isolation is not working as described. Look for a stray wire first (for example the battery − touching the Nano ground rail).

---

## 9. Everything together

### Test 9A: dry run (no water)

Upload the main code from [[03-project-procedure#Phase 5 Main Arduino code]] with `SIMULATE 0`. Leave the pump **in the reservoir** with the tube outlet pointing at the sink or a bowl, away from the sensor.

1. Empty glass in place. Switch to **open-loop**, press **Start**.
2. Check that: Serial prints `MODE: OPEN-LOOP` and CSV lines; the pump runs; the run ends with a `RESULT` line.
3. Repeat with the switch on **closed-loop**.

### Test 9B: noise test

Run the pump at full power (the section 7 sketch or a real run) while watching the Serial Monitor.

| Observation                          | Meaning                                              |
| ------------------------------------ | ---------------------------------------------------- |
| Serial output stays clean, the distance stays stable and the Nano does not reset | **Pass** |
| Serial output freezes, shows garbage or the Nano resets when the pump starts | Electrical noise or a weak supply: keep the pump power wires away from the signal wires, make sure the pump supply is **separate from USB power** and the battery − is not tied to the Nano GND |
| Distance readings jump only while the pump runs | Water ripples or the tube is in the sensor beam: aim the tube at the inner glass wall and add the splash guard |

### Test 9C: sanity check against the real volume

Run one closed-loop run to 100 ml and pour the water into the graduated cylinder. Compare it with the Arduino reading. If they differ by more than about **±8 ml**, redo the area calibration.

---

## Summary checklist

Tick each row only when the test passes.

- [ ] **1.** Nano: LED blinks, Serial works
- [ ] **2.** Button reads PRESSED / released correctly
- [ ] **2.** Switch reads CLOSED-LOOP (GND side) and OPEN-LOOP correctly
- [ ] **3.** HC-SR04 within ±0.5 cm of a ruler, spread 0.3 cm or less
- [ ] **3.** HC-SR04 distance drops by about $100/A$ cm when 100 ml is added
- [ ] **4.** Battery pack reads at least about 6 V with correct polarity
- [ ] **5.** Flyback diode conducts in one direction only
- [ ] **6.** Pump pumps water when connected directly to the battery
- [ ] **7.** Pump speed follows PWM through the isolated module and stops at 0
- [ ] **8.** Nano-side grounds joined, battery − kept separate, no shorts
- [ ] **9.** Dry run completes in both modes
- [ ] **9.** No Serial or sensor glitches while the pump runs

> [!success] Report tip
> Photograph a few of these tests (the Serial output of the sensor test, a pump run, the multimeter reading) and add one short paragraph to the **Prototype development and procedure** section. It shows that each component was verified, which supports the *Functional prototype* part of the rubric.
