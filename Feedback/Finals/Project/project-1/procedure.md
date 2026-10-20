
---

## title: Project Procedure aliases: [Procedure, Build Guide] tags: [project, arduino, procedure, code] status: planning

# Project Procedure (with Arduino Code)

**Notes in this project:** [[01-project-overview|Overview]] · [[02-material-requirements|Materials]] · [[03-project-procedure|Procedure]] · [[04-project-simulation|Simulation]]

## Roadmap

```mermaid
flowchart LR
    P1["1 Design"] --> P2["2 Math model"] --> P3["3 Simulation"] --> P4["4 Build and calibrate"] --> P5["5 Code"] --> P6["6 Testing"] --> P7["7 Report and demo"]
```

---

## Phase 1 Design

_(PDF Step 1)_

- [ ] Get the topic approved by your instructor
- [ ] Draw the block diagram (see [[01-project-overview#3. Block diagrams]])
- [ ] Finalize the component list ([[02-material-requirements]])
- [ ] Choose targets: **100 ml and 200 ml** minimum (PDF requires at least two reference settings)

**Output:** block diagram + component list.

---

## Phase 2 Mathematical model

_(PDF Step 2)_

### Assumptions

1. Constant supply voltage during a run.
2. $1\ \text{ml of water} \approx 1\ \text{g}$.
3. Tubing delay is negligible.
4. Pump flow responds as a first-order system to the on/off drive.

### Derivation

**Pump** (drive to flow rate), first-order:

$$ \tau,\dot{Q}(t) + Q(t) = K,u(t) \quad\Longrightarrow\quad \frac{Q(s)}{U(s)} = \frac{K}{\tau s + 1} $$

**Glass** (flow to accumulated volume), integrator:

$$ \frac{dV}{dt} = Q(t) \quad\Longrightarrow\quad \frac{V(s)}{Q(s)} = \frac{1}{s} $$

**Plant transfer function:**

$$ \boxed{G(s) = \frac{V(s)}{U(s)} = \frac{K}{s,(\tau s + 1)}} $$

|Symbol|Meaning|Unit|
|---|---|---|
|$u$|Pump drive (0 = off, 1 = full)|–|
|$Q$|Flow rate|ml/s|
|$K$|Steady flow at full drive|ml/s|
|$\tau$|Pump time constant|s|
|$V$|Dispensed volume|ml|

### Time-domain step response

For a unit step in $u$:

$$ V(t) = K\left[,t - \tau\left(1 - e^{-t/\tau}\right)\right] $$

After the short transient, $V(t)$ is a ramp with slope $K$.

### Closed-loop view (on-off controller)

$$ e(t) = r - y(t), \qquad u(t) = \begin{cases} 1 & e > 0 \ 0 & e \le 0 \end{cases} $$

Because the plant contains an integrator, the volume holds its value once the pump stops, so the steady-state error is set only by the overshoot after shutoff.

### Estimating $K$ and $\tau$

- **$K$:** run the pump at full drive for a known time $T$ and measure the volume $V_m$: $$K = \frac{V_m}{T}$$
- **$\tau$:** from the logged data, find the time for the flow to reach about $63\%$ of its steady value after the pump starts. If this is hard to measure, estimate it and **state it as an estimate** (the PDF allows this).

**Output:** equations + transfer function + stated assumptions.

---

## Phase 3 Simulation

_(PDF Step 3)_ Make **one open-loop step response plot** from $G(s)$ using your measured $K$ and $\tau$. See [[04-project-simulation#B. Open-loop step response plot]].

---

## Phase 4 Build and calibrate

### Assemble

- [ ] Mount the load cell (fixed end on the base, free end carrying the platform)
- [ ] Wire load cell → HX711 → Nano (see [[02-material-requirements#6. Wiring]])
- [ ] Wire the MOSFET pump driver with the **separate battery pack**
- [ ] Wire the two buttons and the slide switch
- [ ] Wire the I2C LCD: GND → GND, VCC → 5V, SDA → A4, SCL → A5
- [ ] Place the pump in the reservoir and aim the tube over the glass

### Install the library

Arduino IDE → Library Manager → search **HX711** → install _HX711 Arduino Library_ by Bogdan Necula.

Then search **LiquidCrystal I2C** → install the one by Frank de Brabander (for the LCD).

### Check the LCD

Upload this I2C scanner once to find the LCD address (usually `0x27` or `0x3F`), then put that value in `LiquidCrystal_I2C lcd(0x27, 16, 2);` in the main code:

```cpp
#include <Wire.h>

void setup() {
  Serial.begin(115200);
  Wire.begin();
  for (uint8_t addr = 1; addr < 127; addr++) {
    Wire.beginTransmission(addr);
    if (Wire.endTransmission() == 0) {
      Serial.print("I2C device found at 0x");
      Serial.println(addr, HEX);
    }
  }
  Serial.println("Scan done.");
}

void loop() {}
```

If the address is found but the screen stays blank, turn the contrast trimmer on the back of the LCD backpack.

### Calibrate the load cell

Upload this sketch, place a **known weight** (e.g., 100 g), and read the raw value:

```cpp
#include "HX711.h"
HX711 scale;

void setup() {
  Serial.begin(115200);
  scale.begin(5, 6);          // DT = D5, SCK = D6
  scale.set_scale();          // no scaling yet
  scale.tare(10);
  Serial.println("Place known weight, then read the value below.");
}

void loop() {
  Serial.println(scale.get_units(10));   // raw reading
  delay(500);
}
```

$$ \text{CAL\_FACTOR} = \frac{\text{raw reading}}{m_{known}\ (\text{g})} $$

> [!example] Raw $= 42000$ with a $100 \text{g}$ weight gives $\text{CAL\_FACTOR} = 420$. Put this in `CAL_FACTOR` in the main code and verify with a second known weight.

### Measure the pump flow rate (open-loop estimate)

1. Upload the main code with the placeholder `FLOW_ML_PER_S = 5.0`.
2. Run the pump at full power for about 10 s and measure the volume with a graduated cylinder.
3. Compute $\hat{K} = V_m / T$. Repeat 3 times and average.
4. Update `FLOW_ML_PER_S`. The open-loop run time is then:

$$ t_{run} = \frac{r}{\hat{K}} $$

---

## Phase 5 Main Arduino code

> [!note] Set `#define SIMULATE 0` for real hardware and `1` for the Wokwi simulation ([[04-project-simulation]]). Set `#define USE_LCD 1` when the I2C LCD is connected and `0` to build without it.

> [!INFO]- Controller flow
> 
> ```mermaid
> flowchart TD
>     A(["Start pressed"]) --> B["Read mode switch (D4)"]
>     B --> C["Tare scale"]
>     C --> D{"Closed-loop?"}
>     D -- "No" --> E["Pump ON for t = target / flow<br/>(LCD shows measured %)"]
>     E --> F["Pump OFF"]
>     D -- "Yes" --> G["Read weight + update LCD %"]
>     G --> H{"weight >= target?"}
>     H -- "No" --> I["Pump ON"]
>     I --> G
>     H -- "Yes" --> F
>     F --> J["Wait 3 s for dripping"]
>     J --> K["Print RESULT line + show final % on LCD"]
> ```
> 
### Code

```cpp
/*
  Water Dispenser: Open-Loop vs Closed-Loop (On-Off) Control
  Board: Arduino Nano
  Libraries: HX711 (bogde), only needed when SIMULATE = 0
             LiquidCrystal I2C (Frank de Brabander), only needed when USE_LCD = 1

  Mode switch (D4):  HIGH = OPEN-LOOP,  LOW = CLOSED-LOOP
  Target button (D3): cycles 100 / 200 / 300 ml
  Start button (D2):  runs one dispensing cycle
  I2C LCD 16x2:       SDA = A4, SCL = A5. Shows progress = measured volume / target (%)
  Output: CSV over Serial at 115200 baud (time_ms, weight_g)

  The LCD is DISPLAY ONLY. It never affects the pump or the controller.
*/

#define SIMULATE 0        // 0 = real hardware, 1 = software plant model (Wokwi)
#define USE_LCD  1        // 1 = I2C LCD connected, 0 = no LCD

#if !SIMULATE
  #include "HX711.h"
  HX711 scale;
#endif

#if USE_LCD
  #include <Wire.h>
  #include <LiquidCrystal_I2C.h>
  LiquidCrystal_I2C lcd(0x27, 16, 2);   // I2C address (try 0x3F if blank), 16 columns, 2 rows
#endif

// ---------- Pins ----------
const uint8_t PIN_HX_DT   = 5;
const uint8_t PIN_HX_SCK  = 6;
const uint8_t PIN_PUMP    = 9;   // PWM -> MOSFET gate
const uint8_t PIN_START   = 2;
const uint8_t PIN_TARGET  = 3;
const uint8_t PIN_MODE    = 4;
// LCD uses the Nano's hardware I2C pins: SDA = A4, SCL = A5

// ---------- Calibration and tuning (EDIT THESE) ----------
float CAL_FACTOR = 420.0;                  // from calibration
const float FLOW_ML_PER_S = 5.0;           // measured flow (open-loop estimate)
const int   PWM_FULL = 255;                // full pump power
const int   PWM_SLOW = 150;                // slow speed near target (bonus; pump may stall if too low)
const float STOP_EARLY_ML = 0.0;           // closed-loop: stop this many ml early (try 2-5 later)
const float SLOW_ZONE_ML  = 0.0;           // closed-loop: slow down in last N ml (0 = pure on-off; try 20)
const unsigned long TIMEOUT_MS = 60000UL;  // safety: max pump time
const unsigned long SETTLE_MS  = 3000UL;   // wait for dripping before final reading
const unsigned long LCD_REFRESH_MS = 200;  // LCD update period (keeps I2C traffic from slowing the loop)

// ---------- Simulation plant model (only used if SIMULATE = 1) ----------
const float SIM_TAU_S = 0.5;               // pump time constant
const float SIM_GAIN  = 0.8;               // real pump is 80% as strong as the estimate (a disturbance)
const unsigned long SIM_SAMPLE_MS = 50;

// ---------- Targets ----------
const float   TARGETS_ML[] = {100.0, 200.0, 300.0};
const uint8_t N_TARGETS    = 3;
uint8_t tIdx = 0;

// ---------- State ----------
int   pumpPwm = 0;
float simFlow = 0.0, simVol = 0.0;
unsigned long simLast = 0;
float runTarget = 0.0;          // target of the run in progress (for the LCD)
bool  runClosed = false;        // mode of the run in progress (for the LCD)
bool  resultShown = false;      // true while the final result is held on the LCD
unsigned long lcdLast = 0;

// ---------- LCD helpers (display only, never used for control) ----------
// Progress in percent = measured volume / target * 100. Can exceed 100 (overshoot).
int percentOf(float volumeMl, float targetMl) {
  int p = (int)(volumeMl / targetMl * 100.0 + 0.5);
  return constrain(p, 0, 999);
}

void lcdLine(uint8_t row, const char *text) {
#if USE_LCD
  char buf[17];
  snprintf(buf, sizeof(buf), "%-16s", text);   // pad to 16 chars so old text is erased
  lcd.setCursor(0, row);
  lcd.print(buf);
#endif
}

// Idle screen: mode and target. Redrawn only when one of them changes.
void lcdShowIdle(bool closedLoop) {
#if USE_LCD
  static int8_t lastMode = -1, lastIdx = -1;
  if (lastMode == (int8_t)closedLoop && lastIdx == (int8_t)tIdx) return;
  lastMode = closedLoop;
  lastIdx  = tIdx;
  char l0[17], l1[17];
  snprintf(l0, sizeof(l0), "Mode: %s", closedLoop ? "CLOSED" : "OPEN");
  snprintf(l1, sizeof(l1), "Target: %3d ml", (int)TARGETS_ML[tIdx]);
  lcdLine(0, l0);
  lcdLine(1, l1);
#endif
}

// Running screen: progress bar and percentage of the target.
void lcdShowProgress(float volumeMl, bool force) {
#if USE_LCD
  if (!force && millis() - lcdLast < LCD_REFRESH_MS) return;
  lcdLast = millis();
  int pct = percentOf(volumeMl, runTarget);
  char l0[17], l1[17];
  if (force) {
    snprintf(l0, sizeof(l0), "%-6s  T=%3dml", runClosed ? "CLOSED" : "OPEN", (int)runTarget);
    lcdLine(0, l0);
  }
  int cells = constrain(pct, 0, 100) / 10;          // 0..10 filled cells
  for (uint8_t i = 0; i < 10; i++) l1[i] = (i < cells) ? (char)255 : '.';
  snprintf(l1 + 10, sizeof(l1) - 10, " %3d%%", pct);
  lcdLine(1, l1);
#endif
}

// Result screen: final volume and final percentage. Held until the next button press.
void lcdShowResult(float finalMl) {
#if USE_LCD
  char l0[17], l1[17];
  int ml = (int)(finalMl + 0.5);
  if (ml < 0) ml = 0;
  if (ml > 999) ml = 999;
  snprintf(l0, sizeof(l0), "DONE    T=%3dml", (int)runTarget);
  snprintf(l1, sizeof(l1), "Final %3dml %3d%%", ml, percentOf(finalMl, runTarget));
  lcdLine(0, l0);
  lcdLine(1, l1);
#endif
}

// ---------- Helpers ----------
void setPump(int pwm) {
  pumpPwm = pwm;
  analogWrite(PIN_PUMP, pwm);
}

float readWeight() {
#if SIMULATE
  unsigned long now = millis();
  float dt = (now - simLast) / 1000.0;
  simLast = now;
  float u = pumpPwm / 255.0;
  float flowTarget = FLOW_ML_PER_S * SIM_GAIN * u;
  simFlow += (flowTarget - simFlow) * dt / SIM_TAU_S;   // first-order pump
  simVol  += simFlow * dt;                              // integrator (glass)
  return simVol;
#else
  return scale.get_units(1);
#endif
}

void tareScale() {
#if SIMULATE
  simFlow = 0.0; simVol = 0.0; simLast = millis();
#else
  scale.tare(10);
#endif
}

float sampleAndLog(unsigned long t0) {
  float w = readWeight();
  Serial.print(millis() - t0);
  Serial.print(',');
  Serial.println(w, 1);
  lcdShowProgress(w, false);                 // display only
#if SIMULATE
  delay(SIM_SAMPLE_MS);
#endif
  return w;
}

bool pressed(uint8_t pin) {
  if (digitalRead(pin) == LOW) {
    delay(30);
    if (digitalRead(pin) == LOW) {
      while (digitalRead(pin) == LOW) { }   // wait for release
      return true;
    }
  }
  return false;
}

// ---------- One dispensing cycle ----------
void runDispense(bool closedLoop) {
  float target = TARGETS_ML[tIdx];
  runTarget = target;
  runClosed = closedLoop;

  Serial.println();
  Serial.print("MODE: ");   Serial.println(closedLoop ? "CLOSED-LOOP" : "OPEN-LOOP");
  Serial.print("TARGET_ML: "); Serial.println(target, 0);
  Serial.println("Taring... keep the empty glass on the platform.");
  lcdLine(0, "Taring...");
  lcdLine(1, "Keep glass on");
  tareScale();
  lcdShowProgress(0.0, true);                // draw the progress screen at 0%
  Serial.println("time_ms,weight_g");

  unsigned long t0 = millis();

  if (!closedLoop) {
    // OPEN-LOOP: fixed, pre-calculated time. The sensor is read ONLY for logging and the LCD.
    unsigned long runMs = (unsigned long)(target / FLOW_ML_PER_S * 1000.0);
    setPump(PWM_FULL);
    while (millis() - t0 < runMs && millis() - t0 < TIMEOUT_MS) {
      sampleAndLog(t0);
    }
    setPump(0);
  } else {
    // CLOSED-LOOP: compare measured weight to the target and stop when reached.
    while (millis() - t0 < TIMEOUT_MS) {
      float w = sampleAndLog(t0);
      float remaining = target - STOP_EARLY_ML - w;
      if (remaining <= 0) break;                                  // on-off decision
      if (SLOW_ZONE_ML > 0 && remaining < SLOW_ZONE_ML) setPump(PWM_SLOW);
      else setPump(PWM_FULL);
    }
    setPump(0);
  }

  // Let residual water drip, then take the final reading.
  unsigned long ts = millis();
  float w = 0;
  while (millis() - ts < SETTLE_MS) { w = sampleAndLog(t0); }

  lcdShowResult(w);                          // hold the final result on the LCD
  resultShown = true;

  Serial.print("RESULT,");
  Serial.print(closedLoop ? "closed" : "open");
  Serial.print(",target=");  Serial.print(target, 0);
  Serial.print(",final=");   Serial.print(w, 1);
  Serial.print(",error=");   Serial.println(w - target, 1);
  Serial.println("Record the REAL volume with a graduated cylinder too.");
}

// ---------- Arduino entry points ----------
void setup() {
  Serial.begin(115200);
  pinMode(PIN_PUMP, OUTPUT);
  setPump(0);                                // pump OFF at boot
  pinMode(PIN_START,  INPUT_PULLUP);
  pinMode(PIN_TARGET, INPUT_PULLUP);
  pinMode(PIN_MODE,   INPUT_PULLUP);

#if USE_LCD
  lcd.init();
  lcd.backlight();
#endif
  lcdLine(0, "Water Dispenser");
  lcdLine(1, "Starting...");

#if !SIMULATE
  scale.begin(PIN_HX_DT, PIN_HX_SCK);
  scale.set_scale(CAL_FACTOR);
  scale.tare(10);
#endif

  Serial.println("Water Dispenser ready.");
  Serial.println("D3 = change target, D2 = start, D4 switch = mode (HIGH open / LOW closed)");
  Serial.print("Current target: "); Serial.print(TARGETS_ML[tIdx], 0); Serial.println(" ml");
}

void loop() {
  if (pressed(PIN_TARGET)) {
    tIdx = (tIdx + 1) % N_TARGETS;
    resultShown = false;                     // leave the result screen
    Serial.print("Target set to: "); Serial.print(TARGETS_ML[tIdx], 0); Serial.println(" ml");
  }
  if (pressed(PIN_START)) {
    bool closedLoop = (digitalRead(PIN_MODE) == LOW);
    runDispense(closedLoop);
  }
  if (!resultShown) lcdShowIdle(digitalRead(PIN_MODE) == LOW);
}
```

### Code to control-theory map

|Code|Control concept|
|---|---|
|`TARGETS_ML[tIdx]`|Reference input $r$|
|`readWeight()`|Sensor output $y$ (feedback)|
|`remaining = target - w`|Error $e = r - y$|
|`if (remaining <= 0) break;`|On-off controller|
|`setPump(...)`|Actuator command $u$|
|Fixed `runMs` in open-loop|No feedback, time from the model only|
|`percentOf()`, `lcdShowProgress()`|Display of $p = y/r \times 100\%$. Reads $y$ but **never feeds the controller**|

---

## Phase 6 Testing and data collection

_(PDF Steps 4 and 5)_

### Test procedure (for each target: 100 ml and 200 ml)

1. Place the **empty glass** on the platform. Check that the pump has water and the tube is aimed correctly.
2. Choose the target with the D3 button.
3. Switch to **open-loop**, press **Start**, then record the Arduino reading, the final **% shown on the LCD**, and the real volume (graduated cylinder).
4. Repeat **3 times**.
5. Switch to **closed-loop** and repeat steps 3–4.
6. Compute the error for each trial.

**Absolute error and percent error:**

$$ e_{abs} = \lvert V_{real} - r \rvert, \qquad e_{\%} = \frac{e_{abs}}{r} \times 100\% $$

### Data table template

|Mode|Target (ml)|Trial|Arduino reading (g)|Real volume (ml)|Abs. error (ml)|
|---|---|---|---|---|---|
|Open|100|1||||
|Open|100|2||||
|Open|100|3||||
|Closed|100|1||||
|Closed|100|2||||
|Closed|100|3||||
|Open|200|1||||
|Open|200|2||||
|Open|200|3||||
|Closed|200|1||||
|Closed|200|2||||
|Closed|200|3||||

Add an **average error** row for each mode and target.

### Required comparison (PDF Section 4)

|Test condition|Open-loop|Closed-loop|
|---|---|---|
|Reference / target setting|Record|Record|
|Measured output|Record|Record|
|Absolute steady-state error|Calculate|Calculate|
|Response to a change in target (100 → 200 ml)|Observe|Observe|
|Response to a disturbance|Observe|Observe|

### Disturbance test

Pick one and apply it to **both** modes:

- Use **weaker (partly used) batteries** instead of fresh ones
- Raise or lower the **reservoir or glass height** by a few centimeters
- **Slightly pinch** the tube with a clip

> [!success] Expected result If the true flow is a fraction $\alpha$ of the estimate ($K_{true} = \alpha\hat{K}$), open-loop gives a steady-state error of $$e_{ss,open} = r,(1 - \alpha)$$ Closed-loop does not depend on $\alpha$, because it measures the real output. Its error is only the small post-shutoff overshoot. This is your clearest proof that feedback helps.

### Optional bonus: reduce overshoot

Try one at a time and record the effect:

1. `STOP_EARLY_ML = 3.0` (tune from test runs)
2. `SLOW_ZONE_ML = 20.0` with `PWM_SLOW = 150` (a simple P-like slowdown)

---

## Phase 7 Report and demonstration

_(PDF Step 6)_

### Report outline (about 4–6 pages, excluding code and appendices)

1. **Introduction and objectives:** the application, control problem, objectives.
2. **System design and mathematical model:** block diagram, components, assumptions, $G(s) = \dfrac{K}{s(\tau s + 1)}$.
3. **Prototype development and procedure:** circuit, controller logic, testing procedure.
4. **Results and discussion:** simulation plot, data table, open vs closed-loop comparison, consistency with the model.
5. **Conclusion:** were the objectives achieved? State **one limitation** (dripping overshoot) and **one improvement** (drain pump or PWM slowdown).

**Appendices:** wiring diagram and source code only.

### Live demo checklist

- [ ] Dry run beforehand (spill tray ready)
- [ ] Demo at least two targets
- [ ] Point out the LCD: progress bar rises to the target % (open-loop may stop short of 100%, closed-loop stops at about 100%)
- [ ] Show open-loop, then closed-loop, then a disturbance in both
- [ ] Graduated cylinder ready to confirm the real volume
- [ ] Be ready to explain the sensor, controller, and feedback path
- [ ] Record a backup video (the PDF's recommended arrangement mentions a recorded video demonstration)

> [!TLDR]- Timeline (3 weeks)
> 
> ```mermaid
> flowchart LR
>     W1["Week 1<br/>Topic approval<br/>Block diagram<br/>Model and transfer function<br/>Initial simulation"] --> W2["Week 2<br/>Build and calibrate<br/>Implement controller<br/>Test open and closed loop<br/>Record data"]
>     W2 --> W3["Week 3<br/>Disturbance tests<br/>Final comparison<br/>Report<br/>Demo and video"]
> ```
> 
---

## Troubleshooting

|Problem|Likely cause / fix|
|---|---|
|Weight reading drifts|Load cell not rigidly mounted, or tubing touching the platform; let it warm up for a minute|
|Weight is negative when adding water|Swap load cell A+ and A− wires|
|Pump never turns on|MOSFET wired backwards, dead battery, or missing common ground|
|Pump runs at boot|Missing 10 kΩ pull-down on the gate|
|Pump stalls at `PWM_SLOW`|Raise `PWM_SLOW` (try 180)|
|Upload fails on Nano|Tools → Processor → "ATmega328P (Old Bootloader)"|
|Large open-loop errors|Re-measure `FLOW_ML_PER_S` with the same battery and tube setup|
|LCD backlight on, no text|Turn the contrast trimmer on the I2C backpack|
|LCD completely blank|Wrong I2C address (try `0x3F`, or run the scanner), or SDA/SCL swapped|
|LCD shows garbled characters or flickers|Loose jumper on SDA/SCL, or pump noise on the 5V rail: re-seat wires and keep the battery pack separate from USB power|
|LCD % is wrong but Serial weight is right|Check the target value shown on line 1 and that the glass was tared empty|