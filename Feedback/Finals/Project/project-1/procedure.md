
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
- [ ] Place the pump in the reservoir and aim the tube over the glass

### Install the library

Arduino IDE → Library Manager → search **HX711** → install _HX711 Arduino Library_ by Bogdan Necula.

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

> [!note] Set `#define SIMULATE 0` for real hardware and `1` for the Wokwi simulation ([[04-project-simulation]]).

> [!INFO]- Controller flow
> 
> ```mermaid
> flowchart TD
>     A(["Start pressed"]) --> B["Read mode switch (D4)"]
>     B --> C["Tare scale"]
>     C --> D{"Closed-loop?"}
>     D -- "No" --> E["Pump ON for t = target / flow"]
>     E --> F["Pump OFF"]
>     D -- "Yes" --> G["Read weight"]
>     G --> H{"weight >= target?"}
>     H -- "No" --> I["Pump ON"]
>     I --> G
>     H -- "Yes" --> F
>     F --> J["Wait 3 s for dripping"]
>     J --> K["Print RESULT line"]
> ```
> 
### Code

```cpp
/*
  Water Dispenser: Open-Loop vs Closed-Loop (On-Off) Control
  Board: Arduino Nano
  Library: HX711 (bogde), only needed when SIMULATE = 0

  Mode switch (D4):  HIGH = OPEN-LOOP,  LOW = CLOSED-LOOP
  Target button (D3): cycles 100 / 200 / 300 ml
  Start button (D2):  runs one dispensing cycle
  Output: CSV over Serial at 115200 baud (time_ms, weight_g)
*/

#define SIMULATE 0        // 0 = real hardware, 1 = software plant model (Wokwi)

#if !SIMULATE
  #include "HX711.h"
  HX711 scale;
#endif

// ---------- Pins ----------
const uint8_t PIN_HX_DT   = 5;
const uint8_t PIN_HX_SCK  = 6;
const uint8_t PIN_PUMP    = 9;   // PWM -> MOSFET gate
const uint8_t PIN_START   = 2;
const uint8_t PIN_TARGET  = 3;
const uint8_t PIN_MODE    = 4;

// ---------- Calibration and tuning (EDIT THESE) ----------
float CAL_FACTOR = 420.0;                  // from calibration
const float FLOW_ML_PER_S = 5.0;           // measured flow (open-loop estimate)
const int   PWM_FULL = 255;                // full pump power
const int   PWM_SLOW = 150;                // slow speed near target (bonus; pump may stall if too low)
const float STOP_EARLY_ML = 0.0;           // closed-loop: stop this many ml early (try 2-5 later)
const float SLOW_ZONE_ML  = 0.0;           // closed-loop: slow down in last N ml (0 = pure on-off; try 20)
const unsigned long TIMEOUT_MS = 60000UL;  // safety: max pump time
const unsigned long SETTLE_MS  = 3000UL;   // wait for dripping before final reading

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

  Serial.println();
  Serial.print("MODE: ");   Serial.println(closedLoop ? "CLOSED-LOOP" : "OPEN-LOOP");
  Serial.print("TARGET_ML: "); Serial.println(target, 0);
  Serial.println("Taring... keep the empty glass on the platform.");
  tareScale();
  Serial.println("time_ms,weight_g");

  unsigned long t0 = millis();

  if (!closedLoop) {
    // OPEN-LOOP: fixed, pre-calculated time. The sensor is read ONLY for logging.
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
    Serial.print("Target set to: "); Serial.print(TARGETS_ML[tIdx], 0); Serial.println(" ml");
  }
  if (pressed(PIN_START)) {
    bool closedLoop = (digitalRead(PIN_MODE) == LOW);
    runDispense(closedLoop);
  }
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

---

## Phase 6 Testing and data collection

_(PDF Steps 4 and 5)_

### Test procedure (for each target: 100 ml and 200 ml)

1. Place the **empty glass** on the platform. Check that the pump has water and the tube is aimed correctly.
2. Choose the target with the D3 button.
3. Switch to **open-loop**, press **Start**, then record the Arduino reading and the real volume (graduated cylinder).
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