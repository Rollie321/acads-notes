---
title: Project Procedure
aliases: [Procedure, Build Guide]
tags: [project, arduino, procedure, code, hc-sr04]
status: planning
---

# Project Procedure (with Arduino Code)

**Notes in this project:** [[01-project-overview|Overview]] · [[02-material-requirements|Materials]] · [[03-project-procedure|Procedure]] · [[04-project-simulation|Simulation]] · [[05-component-testing|Component Testing]]

## Roadmap

```mermaid
flowchart LR
    P1["1 Design"] --> P2["2 Math model"] --> P3["3 Simulation"] --> P4["4 Build and calibrate"] --> P5["5 Code"] --> P6["6 Testing"] --> P7["7 Report and demo"]
```

---

## Phase 1 Design

*(PDF Step 1)*

- [ ] Get the topic approved by your instructor (PDF topic D, automatic water-level control, with an on-off controller)
- [ ] Draw the block diagram (see [[01-project-overview#3. Block diagrams]])
- [ ] Choose a straight-sided glass and sketch the sensor mounting (sensor 5 to 10 cm above the highest water level you will use)
- [ ] Tape a ruler vertically on the outside of the glass, 0 mark level with the inside floor (ground truth for the real level)
- [ ] Finalize the component list ([[02-material-requirements]])
- [ ] Choose two target level rises: **3 cm and 6 cm** minimum (PDF requires at least two reference settings). The target is set by `TARGET_CM` in the code, so each target is one upload

**Output:** block diagram + component list.

---

## Phase 2 Mathematical model

*(PDF Step 2)*

### Assumptions

1. Constant supply voltage during a run.
2. Tubing delay is negligible.
3. Pump flow responds as a first-order system to the on/off drive.
4. The glass has **straight sides**, so the cross-section is constant and a given flow always raises the level at the same rate. The glass area is absorbed into $K_h$ and is never measured.
5. The ultrasonic beam hits the water surface directly, and the surface is nearly still when read.
6. The sensor adds only a small sampling and filtering delay $T_f$ (about 0.1 to 0.2 s), neglected in $G(s)$.

### Derivation

**Pump and tubing** (drive to level rise rate), first-order. $Q_h$ is how fast the water level rises:

$$\tau\,\dot{Q}_h(t) + Q_h(t) = K_h\,u(t) \quad\Longrightarrow\quad \frac{Q_h(s)}{U(s)} = \frac{K_h}{\tau s + 1}$$

**Glass** (level rise rate to accumulated level rise), integrator:

$$\frac{dh}{dt} = Q_h(t) \quad\Longrightarrow\quad \frac{H(s)}{Q_h(s)} = \frac{1}{s}$$

**Plant transfer function:**

$$\boxed{G(s) = \frac{H(s)}{U(s)} = \frac{K_h}{s\,(\tau s + 1)}}$$

**Sensor** (distance to level): the HC-SR04 measures the distance $d$ to the surface, and the Arduino computes

$$y = d_0 - d$$

where $d_0$ is the empty-glass distance. The sensor is therefore a unity gain on the level change, plus the small delay $T_f$.

| Symbol | Meaning                                           | Unit |
| ------ | ------------------------------------------------- | ---- |
| $u$    | Pump drive (0 = off, 1 = full)                    | –    |
| $Q_h$  | Level rise rate                                   | cm/s |
| $K_h$  | Steady level rise rate at full drive              | cm/s |
| $\tau$ | Pump time constant                                | s    |
| $h$    | Level rise (the output $y$)                       | cm   |
| $d_0$  | Empty-glass distance from sensor to the glass floor | cm |
| $d$    | Current distance from sensor to the water surface | cm   |

### Time-domain step response

For a unit step in $u$:

$$h(t) = K_h\left[\,t - \tau\left(1 - e^{-t/\tau}\right)\right]$$

After the short transient, $h(t)$ is a ramp with slope $K_h$.

### Closed-loop view (on-off controller)

$$
e(t) = r - y(t), \qquad
u(t) =
\begin{cases}
1 & e > 0 \\
0 & e \le 0
\end{cases}
$$

Because the plant contains an integrator, the level holds its value once the pump stops, so the steady-state error is set only by the overshoot after shutoff and by the sensor resolution (about $\pm 0.3$ cm).

### Estimating $K_h$ and $\tau$

- **$K_h$:** run the pump at full drive for a known time $T$ and read the real level rise $h_m$ on the ruler:

  $$K_h = \frac{h_m}{T}$$

  The procedure is in "Measure the level rise rate" below.

- **$\tau$:** from the logged data, find the time for the level rise rate to reach about $63\%$ of its steady value after the pump starts. If this is hard to measure, estimate it and **state it as an estimate** (the PDF allows this).

**Output:** equations + transfer function + stated assumptions.

---

## Phase 3 Simulation

*(PDF Step 3)* Make **one open-loop step response plot** from $G(s)$ using your measured $K_h$ and $\tau$. See [[04-project-simulation#B. Open-loop step response plot]].

---

## Phase 4 Build and calibrate

### Assemble

- [ ] Solder the 2-pin header and the 3-pin screw terminal onto the MOSFET module, then build the pump driver **first** and test it with a fixed PWM before adding anything else (see [[05-component-testing]])
- [ ] Tape the ruler on the glass (0 mark at the inside floor)
- [ ] Mount the HC-SR04 on its bracket, pointing straight down, centered over the glass, 5 to 10 cm above the highest water level you will use
- [ ] Wire HC-SR04 → Nano (see [[02-material-requirements#6. Wiring]])
- [ ] Wire the isolated MOSFET module: signal side (PWM, GND) to the Nano, load side (+, LOAD, −) to the **separate battery pack** and the pump
- [ ] Wire the Start button and the mode slide switch
- [ ] Place the pump in the reservoir and fix the tube so the water runs down the **inner glass wall**, outside the sensor beam

### Connection table

Every wire needed for the final build. Tick each row as you connect it.

| #   | Group         | From                            | To                                       | Notes                                                                              |
| --- | ------------- | ------------------------------- | ---------------------------------------- | ---------------------------------------------------------------------------------- |
| 1   | HC-SR04 → Nano | HC-SR04 **VCC**                | Nano **5V**                              |                                                                                    |
| 2   | HC-SR04 → Nano | HC-SR04 **GND**                | Nano **GND**                             |                                                                                    |
| 3   | HC-SR04 → Nano | HC-SR04 **Trig**               | Nano **D5**                              | Output from the Nano                                                               |
| 4   | HC-SR04 → Nano | HC-SR04 **Echo**               | Nano **D6**                              | Input to the Nano (5 V logic, no level shifter)                                    |
| 5   | Start button  | Button, one side                | Nano **D2**                              |                                                                                    |
| 6   | Start button  | Button, other side              | Nano **GND**                             | Uses the internal pull-up, no resistor needed                                      |
| 7   | Mode switch   | Slide switch **middle pin**     | Nano **D4**                              |                                                                                    |
| 8   | Mode switch   | Slide switch **one outer pin**  | Nano **GND**                             | Other outer pin stays unconnected. GND position = closed-loop, otherwise open-loop |
| 9   | Pump driver   | Nano **D9**                     | Module **PWM** pad                       | PWM output. No external resistor needed (module takes a 3 V or 5 V signal) |
| 10  | Pump driver   | Nano **GND**                    | Module signal **GND** pad                | Signal reference only (isolated from the load side)                        |
| 11  | Pump driver   | Battery pack **+** (about 6 V)  | Module **+** terminal                    | Separate from USB power                                                    |
| 12  | Pump driver   | Battery pack **−**              | Module **−** terminal                    | Do **not** connect this to the Nano GND                                    |
| 13  | Pump          | Pump **(+)** wire               | Module **+** terminal                    | Same terminal as the battery +: twist the two wires together first         |
| 14  | Pump          | Pump **(−)** wire               | Module **LOAD** terminal                 | The middle terminal. The MOSFET switches this side                         |
| 15  | Flyback diode | Diode **cathode** (striped end) | Module **+** terminal (pump **(+)** side)| Across the pump                                                            |
| 16  | Flyback diode | Diode **anode**                 | Module **LOAD** terminal (pump **(−)** side) |                                                                        |

> [!important] Two separate grounds
> **Nano side:** Nano GND, HC-SR04 GND, the button and switch GND and the module's signal GND pad share one ground rail.
> **Load side:** the battery −, the module's **−** terminal and nothing else. The optocoupler isolates the two sides, so do **not** join them.

> [!tip] Check the terminal order on your board
> Retailer pinouts for this module family list the load terminals as **+ / LOAD / −**, with LOAD as the negative lead of the load. Confirm with the silkscreen on your own board, and run the test in [[05-component-testing#7. MOSFET driver module and pump (with Arduino PWM)]] before using water.

> [!warning] Battery voltage
> One retailer lists a 6 to 28 V load supply for the LR7843 version of this module. A 4 × AA pack is right at that edge, so check the pack voltage under load and replace weak batteries.

> [!note] Optional status LED
> The parts list has an optional LED, but the code does not use one, so it is not in this table.

### Calibrate the HC-SR04

**Step 1: check the readings.** Upload this sketch with the empty glass in place. The distance should be stable to within about $\pm 0.3$ cm:

```cpp
const uint8_t PIN_TRIG = 5, PIN_ECHO = 6;

void setup() {
  Serial.begin(9600);
  pinMode(PIN_TRIG, OUTPUT);
  pinMode(PIN_ECHO, INPUT);
}

void loop() {
  digitalWrite(PIN_TRIG, LOW);  delayMicroseconds(2);
  digitalWrite(PIN_TRIG, HIGH); delayMicroseconds(10);
  digitalWrite(PIN_TRIG, LOW);
  long us = pulseIn(PIN_ECHO, HIGH, 30000UL);   // 30 ms timeout
  Serial.print("Distance (cm): ");
  Serial.println(us * 0.01715, 2);              // 0 means no echo
  delay(300);
}
```

The conversion uses the speed of sound ($343\ \text{m/s}$) and the fact that the pulse travels to the water and back:

$$d = \frac{t_{echo} \times 0.0343\ \text{cm/}\mu\text{s}}{2} = t_{echo} \times 0.01715\ \text{cm/}\mu\text{s}$$

**Step 2: check the level reading against the ruler.** Note the empty distance $d_0$. Pour water slowly until the ruler on the glass reads **3.0 cm** (read at eye level) and note the new distance $d_3$. The computed level rise is

$$y = d_0 - d_3$$

and should be within about $\pm 0.3$ cm of 3.0 cm.

**Step 3: verify at the second target.** Continue pouring until the ruler reads **6.0 cm**. Again $d_0 - d_6$ should be within about $\pm 0.3$ cm of 6.0 cm. There is no constant to enter in the code. If the reading is consistently off, the sensor is probably not pointing straight down, or the glass is not straight-sided, so fix the mounting and repeat.

> [!example] Example
> $d_0 = 14.0\ \text{cm}$ and $d_3 = 11.1\ \text{cm}$ give a rise of $2.9\ \text{cm}$, which is within $\pm 0.3$ cm of the ruler's $3.0\ \text{cm}$.

### Measure the level rise rate (open-loop estimate)

1. Upload the main code with the placeholder `RISE_CM_PER_S = 0.20` and `TARGET_CM = 3.0`. The open-loop run time is then $3.0 / 0.20 = 15\ \text{s}$.
2. With the empty glass in place, switch to **open-loop** and press **Start**. After the 3 s settle, read the real level rise $h_m$ on the ruler.
3. Compute $\hat{K}_h = h_m / T$ with $T = 15\ \text{s}$. Repeat 3 times (empty and dry the glass between runs) and average.
4. Update `RISE_CM_PER_S`. The open-loop run time is then:

$$t_{run} = \frac{r}{\hat{K}_h}$$

---

## Phase 5 Main Arduino code

> [!note]
> Set `#define SIMULATE 0` for real hardware and `1` for the Wokwi simulation ([[04-project-simulation]]).

> [!INFO]- Controller flow
> ```mermaid
> flowchart TD
>     A(["Start pressed"]) --> B["Read mode switch (D4)"]
>     B --> C["Tare: measure empty distance d0"]
>     C --> D{"Closed-loop?"}
>     D -- "No" --> E["Pump ON for t = target / rise rate<br/>(sensor logged only)"]
>     E --> F["Pump OFF"]
>     D -- "Yes" --> G["Read level rise (log to Serial)"]
>     G --> H{"level rise >= target?"}
>     H -- "No" --> I["Pump ON"]
>     I --> G
>     H -- "Yes" --> F
>     F --> J["Wait 3 s for dripping"]
>     J --> K["Print RESULT line (final, error, %)"]
> ```

### Code

```cpp
/*
  Water Level Control: Open-Loop vs Closed-Loop (On-Off) Control
  Board: Arduino Nano
  Sensor: HC-SR04 ultrasonic (Trig = D5, Echo = D6), mounted above the glass
  Pump:   isolated MOSFET module, PWM pad on D9

  Mode switch (D4):  HIGH = OPEN-LOOP,  LOW = CLOSED-LOOP
  Start button (D2):  runs one filling cycle
  Target:             set TARGET_CM below (level rise in cm; edit the code and re-upload to change it)
  Output: CSV over Serial at 9600 baud (time_ms, level_cm), then a RESULT line

  Level rise = emptyCm - distanceCm   (no glass area or volume is needed)
  No libraries are needed. The sensor is read with pulseIn().
*/

#define SIMULATE 0        // 0 = real hardware, 1 = software plant model (Wokwi)

// ---------- Pins ----------
const uint8_t PIN_TRIG    = 5;   // HC-SR04 Trig
const uint8_t PIN_ECHO    = 6;   // HC-SR04 Echo
const uint8_t PIN_PUMP    = 9;   // PWM -> module PWM pad
const uint8_t PIN_START   = 2;
const uint8_t PIN_MODE    = 4;

// ---------- Calibration and tuning (EDIT THESE) ----------
const float RISE_CM_PER_S = 0.20;          // measured level rise rate at full drive (open-loop estimate)
const int   PWM_FULL = 255;                // full pump power
const int   PWM_SLOW = 150;                // slow speed near target (bonus; pump may stall if too low)
const float STOP_EARLY_CM = 0.0;           // closed-loop: stop this many cm early (try 0.2 to 0.5 later)
const float SLOW_ZONE_CM  = 0.0;           // closed-loop: slow down in last N cm (0 = pure on-off; try 1.5)
const unsigned long TIMEOUT_MS = 60000UL;  // safety: max pump time
const unsigned long SETTLE_MS  = 3000UL;   // wait for dripping and ripples before final reading

// ---------- Simulation plant model (only used if SIMULATE = 1) ----------
const float SIM_TAU_S = 0.5;               // pump time constant
const float SIM_GAIN  = 0.8;               // real pump is 80% as strong as the estimate (a disturbance)
const unsigned long SIM_SAMPLE_MS = 50;

// ---------- Target (EDIT THIS) ----------
const float TARGET_CM = 6.0;               // desired level rise in cm (use 3 for the first reference, 6 for the second)

// ---------- State ----------
int   pumpPwm = 0;
float simRate = 0.0, simLevel = 0.0;
unsigned long simLast = 0;
float emptyCm = 0.0;            // empty-glass distance, set by tareLevel()
float lastGoodCm = 0.0;         // last valid distance (used if a ping times out)

// ---------- Pump helper ----------
void setPump(int pwm) {
  pumpPwm = pwm;
  analogWrite(PIN_PUMP, pwm);
}

// ---------- HC-SR04 ultrasonic sensor (real hardware only) ----------
#if !SIMULATE
// One ping, returns the distance in cm. On timeout, returns the last good value.
float pingCm() {
  digitalWrite(PIN_TRIG, LOW);  delayMicroseconds(2);
  digitalWrite(PIN_TRIG, HIGH); delayMicroseconds(10);
  digitalWrite(PIN_TRIG, LOW);
  long us = pulseIn(PIN_ECHO, HIGH, 30000UL);   // 30 ms timeout
  if (us == 0) return lastGoodCm;               // no echo
  return us * 0.01715;                          // round trip: 0.0343 / 2 cm per us
}

// Median of 3 pings: rejects single bad readings from splashes and ripples.
float medianDistance() {
  float a[3];
  for (int i = 0; i < 3; i++) { a[i] = pingCm(); delay(20); }
  if (a[0] > a[1]) { float t = a[0]; a[0] = a[1]; a[1] = t; }
  if (a[1] > a[2]) { float t = a[1]; a[1] = a[2]; a[2] = t; }
  if (a[0] > a[1]) { float t = a[0]; a[0] = a[1]; a[1] = t; }
  lastGoodCm = a[1];
  return a[1];
}
#endif

// ---------- Sensor interface (level rise in cm) ----------
float readLevel() {
#if SIMULATE
  unsigned long now = millis();
  float dt = (now - simLast) / 1000.0;
  simLast = now;
  float u = pumpPwm / 255.0;
  float rateTarget = RISE_CM_PER_S * SIM_GAIN * u;
  simRate  += (rateTarget - simRate) * dt / SIM_TAU_S;  // first-order pump
  simLevel += simRate * dt;                             // integrator (glass)
  return simLevel;
#else
  return emptyCm - medianDistance();                    // y = d0 - d
#endif
}

// Empty-glass reference: average of 10 median readings, taken BEFORE the pump starts.
void tareLevel() {
#if SIMULATE
  simRate = 0.0; simLevel = 0.0; simLast = millis();
#else
  float s = 0;
  for (int i = 0; i < 10; i++) s += medianDistance();
  emptyCm = s / 10.0;
#endif
}

float sampleAndLog(unsigned long t0) {
  float v = readLevel();
  Serial.print(millis() - t0);
  Serial.print(',');
  Serial.println(v, 2);
#if SIMULATE
  delay(SIM_SAMPLE_MS);
#endif
  return v;
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

// ---------- One filling cycle ----------
void runCycle(bool closedLoop) {
  float target = TARGET_CM;

  Serial.println();
  Serial.print("MODE: ");   Serial.println(closedLoop ? "CLOSED-LOOP" : "OPEN-LOOP");
  Serial.print("TARGET_CM: "); Serial.println(target, 1);
  Serial.println("Measuring empty level... keep the empty glass in place.");
  tareLevel();
  Serial.println("time_ms,level_cm");

  unsigned long t0 = millis();

  if (!closedLoop) {
    // OPEN-LOOP: fixed, pre-calculated time. The sensor is read ONLY for logging.
    unsigned long runMs = (unsigned long)(target / RISE_CM_PER_S * 1000.0);
    setPump(PWM_FULL);
    while (millis() - t0 < runMs && millis() - t0 < TIMEOUT_MS) {
      sampleAndLog(t0);
    }
    setPump(0);
  } else {
    // CLOSED-LOOP: compare measured level rise to the target and stop when reached.
    while (millis() - t0 < TIMEOUT_MS) {
      float v = sampleAndLog(t0);
      float remaining = target - STOP_EARLY_CM - v;
      if (remaining <= 0) break;                                  // on-off decision
      if (SLOW_ZONE_CM > 0 && remaining < SLOW_ZONE_CM) setPump(PWM_SLOW);
      else setPump(PWM_FULL);
    }
    setPump(0);
  }

  // Let residual water drip and the surface settle, then take the final reading.
  unsigned long ts = millis();
  float v = 0;
  while (millis() - ts < SETTLE_MS) { v = sampleAndLog(t0); }

  Serial.print("RESULT,");
  Serial.print(closedLoop ? "closed" : "open");
  Serial.print(",target=");  Serial.print(target, 1);
  Serial.print(",final=");   Serial.print(v, 2);
  Serial.print(",error=");   Serial.print(v - target, 2);
  Serial.print(",percent="); Serial.println(v / target * 100.0, 1);
  Serial.println("Record the REAL level with the ruler on the glass too.");
}

// ---------- Arduino entry points ----------
void setup() {
  Serial.begin(9600);
  pinMode(PIN_PUMP, OUTPUT);
  setPump(0);                                // pump OFF at boot
  pinMode(PIN_START,  INPUT_PULLUP);
  pinMode(PIN_MODE,   INPUT_PULLUP);
  pinMode(PIN_TRIG, OUTPUT);
  pinMode(PIN_ECHO, INPUT);
  digitalWrite(PIN_TRIG, LOW);

  Serial.println("Water Level Control ready.");
  Serial.println("D2 = start, D4 switch = mode (HIGH open / LOW closed)");
  Serial.print("Target: "); Serial.print(TARGET_CM, 1); Serial.println(" cm rise (set TARGET_CM in the code)");
}

void loop() {
  bool closedLoop = (digitalRead(PIN_MODE) == LOW);
  static int8_t lastMode = -1;
  if (lastMode != (int8_t)closedLoop) {      // print the mode whenever the switch moves
    lastMode = closedLoop;
    Serial.print("Mode selected: ");
    Serial.println(closedLoop ? "CLOSED-LOOP" : "OPEN-LOOP");
  }
  if (pressed(PIN_START)) {
    runCycle(digitalRead(PIN_MODE) == LOW);
  }
}
```

### Code to control-theory map

| Code                                      | Control concept                                                                      |
| ----------------------------------------- | ------------------------------------------------------------------------------------ |
| `TARGET_CM`                               | Reference input $r$ (fixed in the code)                                              |
| `readLevel()`                             | Sensor output $y = d_0 - d$ (feedback)                                               |
| `medianDistance()`                        | Sensor filtering (adds a small delay $T_f$)                                          |
| `remaining = target - v`                  | Error $e = r - y$                                                                    |
| `if (remaining <= 0) break;`              | On-off controller                                                                    |
| `setPump(...)`                            | Actuator command $u$                                                                 |
| Fixed `runMs` in open-loop                | No feedback, time from the model only                                                |
| `percent` in the `RESULT` line            | Reports $p = y/r \times 100\%$. Reads $y$ but **never feeds the controller**          |

---

## Phase 6 Testing and data collection

*(PDF Steps 4 and 5)*

### Test procedure (for each target: 3 cm and 6 cm)

1. Place the **empty glass** under the sensor. Check that the pump has water, the tube runs down the inner wall, and the ruler is readable.
2. Set `TARGET_CM` in the code (3.0 or 6.0) and upload it.
3. Switch to **open-loop**, press **Start**, then record the Arduino reading, the `percent` from the `RESULT` line, and the real level rise (read the ruler at eye level after the 3 s settle).
4. Repeat **3 times**. Empty the glass and dry the walls between trials.
5. Switch to **closed-loop** and repeat steps 3 and 4.
6. Compute the error for each trial.

**Absolute error and percent error:**

$$e_{abs} = \lvert h_{real} - r \rvert, \qquad e_{\%} = \frac{e_{abs}}{r} \times 100\%$$

### Data table template

| Mode   | Target (cm) | Trial | Arduino reading (cm) | Real level rise (cm, ruler) | Abs. error (cm) |
| ------ | ----------- | ----- | -------------------- | --------------------------- | --------------- |
| Open   | 3           | 1     |                      |                             |                 |
| Open   | 3           | 2     |                      |                             |                 |
| Open   | 3           | 3     |                      |                             |                 |
| Closed | 3           | 1     |                      |                             |                 |
| Closed | 3           | 2     |                      |                             |                 |
| Closed | 3           | 3     |                      |                             |                 |
| Open   | 6           | 1     |                      |                             |                 |
| Open   | 6           | 2     |                      |                             |                 |
| Open   | 6           | 3     |                      |                             |                 |
| Closed | 6           | 1     |                      |                             |                 |
| Closed | 6           | 2     |                      |                             |                 |
| Closed | 6           | 3     |                      |                             |                 |

Add an **average error** row for each mode and target.

### Required comparison (PDF Section 4)

| Test condition                                                        | Open-loop | Closed-loop |
| --------------------------------------------------------------------- | --------- | ----------- |
| Reference / target setting                                            | Record    | Record      |
| Measured output                                                       | Record    | Record      |
| Absolute steady-state error                                           | Calculate | Calculate   |
| Response to a change in target (`TARGET_CM` 3 → 6 cm, re-upload)      | Observe   | Observe     |
| Response to a disturbance                                             | Observe   | Observe     |

### Disturbance test

Pick one and apply it to **both** modes:

- **Weaker batteries (avoid with this module):** if the pack falls below the module's supply minimum (about 6 V is listed by one retailer), the pump may switch erratically instead of just running slower, which does not match the model
- Raise or lower the **reservoir or glass height** by a few centimeters (re-check that the sensor is still 5 to 10 cm above the highest water level)
- **Slightly pinch** the tube with a clip (the easiest way to get a clear difference)

> [!success] Expected result
> If the true level rise rate is a fraction $\alpha$ of the estimate ($K_{h,true} = \alpha\hat{K}_h$), open-loop gives a steady-state error of
>
> $$e_{ss,open} = r\,(1 - \alpha)$$
>
> For example, $\alpha = 0.8$ gives 0.6 cm at the 3 cm target and 1.2 cm at the 6 cm target.
>
> Closed-loop does not depend on $\alpha$, because it measures the real output. Its error is only the small post-shutoff overshoot plus the sensor resolution (about $\pm 0.3$ cm). This is your clearest proof that feedback helps.

### Optional bonus: reduce overshoot

Try one at a time and record the effect:

1. `STOP_EARLY_CM = 0.3` (tune from test runs)
2. `SLOW_ZONE_CM = 1.5` with `PWM_SLOW = 150` (a simple P-like slowdown)

---

## Phase 7 Report and demonstration

*(PDF Step 6)*

### Report outline (about 4–6 pages, excluding code and appendices)

1. **Introduction and objectives:** the application, control problem, objectives.
2. **System design and mathematical model:** block diagram, components, assumptions, $G(s) = \dfrac{K_h}{s(\tau s + 1)}$ and the sensor equation $y = d_0 - d$.
3. **Prototype development and procedure:** circuit, controller logic, testing procedure.
4. **Results and discussion:** simulation plot, data table, open vs closed-loop comparison, consistency with the model.
5. **Conclusion:** were the objectives achieved? State **one limitation** (sensor resolution and dripping overshoot) and **one improvement** (PWM slowdown, stop-early margin, or drain pump).

**Appendices:** wiring diagram and source code only.

### Live demo checklist

- [ ] Dry run beforehand (spill tray ready, splash guard in place)
- [ ] Demo at least two targets (change `TARGET_CM` and re-upload; rehearse this so it is quick, or keep two builds ready)
- [ ] Show the Serial Monitor `RESULT` line after each run (open-loop may stop short of 100%, closed-loop stops at about 100%)
- [ ] Show open-loop, then closed-loop, then a disturbance in both
- [ ] Ruler on the glass ready to confirm the real level
- [ ] Be ready to explain the sensor, controller, and feedback path
- [ ] Record a backup video (the PDF's recommended arrangement mentions a recorded video demonstration)

> [!TLDR]- Timeline (3 weeks)
> ```mermaid
> flowchart LR
>     W1["Week 1<br/>Topic approval<br/>Block diagram<br/>Model and transfer function<br/>Initial simulation"] --> W2["Week 2<br/>Build and calibrate<br/>Implement controller<br/>Test open and closed loop<br/>Record data"]
>     W2 --> W3["Week 3<br/>Disturbance tests<br/>Final comparison<br/>Report<br/>Demo and video"]
> ```

---

## Troubleshooting

| Problem                                           | Likely cause / fix                                                                                              |
| ------------------------------------------------- | --------------------------------------------------------------------------------------------------------------- |
| Distance always 0 or never changes                | Trig and Echo swapped, no 5 V on VCC, or nothing in front of the sensor                                         |
| Readings jump around with still water             | Sensor not pointing straight down, tube or glass rim inside the beam, or sensor too close (under about 5 cm)    |
| Level rise reads negative or too low                  | Tare taken with water already in the glass, or the sensor moved after the tare                                  |
| Level rise wrong by a constant factor                 | Sensor not pointing straight down, or the glass is not straight-sided: redo the ruler check at 3 cm and 6 cm                                                        |
| Closed-loop stops too early                       | Splash or ripples read as a high level: aim the tube at the inner glass wall and keep the median filter on      |
| Level reading is noisy only while filling        | Normal ripples. The 3 s settle before the final reading handles this                                            |
| Pump never turns on                               | Pump (−) not on the **LOAD** terminal, reversed battery polarity, dead or weak battery (below about 6 V), PWM or signal GND wires loose, or terminal screws not clamping the wires |
| Pump runs at boot or will not stop                | Rare with an optocoupler input. Add a 10 kΩ resistor from the PWM pad to the signal GND pad, and check the pump (−) is on LOAD, not on the − terminal |
| Pump stalls at `PWM_SLOW`                         | Raise `PWM_SLOW` (try 180)                                                                                      |
| Upload fails on Nano                              | Tools → Processor → "ATmega328P (Old Bootloader)"                                                              |
| Large open-loop errors                            | Re-measure `RISE_CM_PER_S` with the same battery and tube setup                                                 |
