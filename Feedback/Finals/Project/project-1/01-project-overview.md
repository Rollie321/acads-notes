---
title: Project Overview
aliases: [Overview]
tags: [project, overview, control-systems]
status: planning
---

# Automatic Water Dispenser: Open-Loop vs Closed-Loop

> [!abstract] Summary
> An Arduino Nano system that pumps a selected volume of water into a glass. It runs in two modes, **open-loop** (timed) and **closed-loop** (water level measured by an HC-SR04 ultrasonic sensor), so the effect of feedback can be compared directly.

**Notes in this project:** [[01-project-overview|Overview]] · [[02-material-requirements|Materials]] · [[03-project-procedure|Procedure]] · [[04-project-simulation|Simulation]] · [[05-component-testing|Component Testing]]

---

## 1. Operating modes

| Mode            | How it works                                                  | Sensor used for control?         |
| --------------- | ------------------------------------------------------------- | -------------------------------- |
| **Open-loop**   | Pump runs for a pre-calculated time $t_{run} = \dfrac{r}{\hat{K}}$ | No (Serial log only)    |
| **Closed-loop** | Pump runs until the measured volume reaches the target        | Yes                              |

Here $r$ is the target volume (ml) and $\hat{K}$ is the *estimated* flow rate (ml/s).

> [!info] Level to volume
> The HC-SR04 sits above the glass and measures the distance $d$ down to the water surface. For a straight-sided glass with inner cross-section $A$ (cm²), and $1\ \text{cm}^3 = 1\ \text{ml}$:
>
> $$V = A\,(d_0 - d)$$
>
> where $d_0$ is the empty-glass distance, measured automatically at the start of every run (the "tare").

---

### Progress and result over Serial

There is no display. Every run prints a CSV log (`time_ms,volume_ml`) and a final `RESULT` line that includes the percentage of the target reached:

$$p = \frac{y_{final}}{r} \times 100\%$$

> [!note] Logging only
> The Serial output reads the same measured value $y$ that the closed-loop controller uses, but it is **not** part of the control law, so it does not change the open-loop vs closed-loop comparison. In open-loop mode it simply shows the truth the controller ignores: the percentage ends near $\alpha \times 100\%$ when the real flow is a fraction $\alpha$ of the estimate. In closed-loop mode it stops at about 100% and may read slightly above it because of dripping overshoot.

---

## 2. System identification

| Element                      | In this project                                                                                          |
| ---------------------------- | -------------------------------------------------------------------------------------------------------- |
| **Plant**                    | Pump + tubing + glass (accumulating volume)                                                              |
| **Reference input** $r$      | Target volume (ml), set in the code (`TARGET_ML`)                                                        |
| **Output** $y$               | Volume of water actually in the glass (ml)                                                               |
| **Sensor**                   | HC-SR04 ultrasonic distance sensor, converted to volume with $V = A\,(d_0 - d)$                          |
| **Actuator**                 | DC submersible pump, switched by an optocoupler-isolated MOSFET module (LR7843)                          |
| **Controller**               | Arduino Nano, on-off comparison                                                                          |
| **Feedback path**            | HC-SR04 → Arduino (median filter, level to volume) → MOSFET → pump                                       |
| **Disturbances**             | Tube height change, pinched tube, residual dripping, surface ripples (avoid weak batteries, see [[02-material-requirements]]) |

---

## 3. Block diagrams

### Open-loop

```mermaid
flowchart LR
    R["Target r (ml)"] --> T["Time calc<br/>t = r / K̂"]
    T --> D["MOSFET driver"]
    D --> P["Pump + tubing"]
    P --> G["Glass (integrator)"]
    G --> Y["Volume y (ml)"]
    Y -.->|"monitor only"| M["HC-SR04<br/>level to volume"]
```

### Closed-loop

```mermaid
flowchart LR
    R["Reference r (ml)"] --> S(("Σ"))
    S -->|"e = r - y"| C["On-off controller<br/>(Arduino)"]
    C -->|"u"| A["MOSFET + pump"]
    A -->|"Q"| G["Glass (integrator)"]
    G --> Y["Output y (ml)"]
    Y --> M["HC-SR04<br/>level to volume<br/>(sensor)"]
    M -->|"measured y"| S
```

### On-off control law

$$
u(t) =
\begin{cases}
1 & \text{if } e(t) = r - y(t) > 0 \\
0 & \text{if } e(t) \le 0
\end{cases}
$$

---

## 4. Features

- [ ] Two modes selected with a slide switch (open-loop / closed-loop)
- [ ] Target volume set by one constant in the code (`TARGET_ML`, for example 100 or 200 ml)
- [ ] Single **Start** button
- [ ] Automatic empty-level reference (tare) before each run (place the empty glass first)
- [ ] Median-of-3 filtering of the ultrasonic readings
- [ ] CSV logging over Serial (`time_ms,volume_ml`)
- [ ] Result summary after each run: target, final value, error, % of target
- [ ] 60 s safety timeout
- [ ] Optional stop-early margin and PWM slowdown near the target
- [ ] Built-in simulation mode (`SIMULATE 1`), see [[04-project-simulation]]

---

## 5. Mapping to the PDF requirements

| PDF requirement                                   | Covered in                                                                                          |
| ------------------------------------------------- | --------------------------------------------------------------------------------------------------- |
| Plant, input, output, sensor, actuator            | Section 2 above                                                                                     |
| Block diagram and component list                  | Section 3 above, [[02-material-requirements]]                                                       |
| Mathematical model and transfer function          | [[03-project-procedure#Phase 2 Mathematical model]]                                                 |
| One open-loop simulation plot                     | [[04-project-simulation#B. Open-loop step response plot]]                                           |
| Functional prototype and source code              | [[03-project-procedure#Phase 5 Main Arduino code]]                                                  |
| Open vs closed-loop comparison table              | [[03-project-procedure#Phase 6 Testing and data collection]]                                        |
| Disturbance response                              | [[03-project-procedure#Disturbance test]]                                                           |
| Short report, live demo                           | [[03-project-procedure#Phase 7 Report and demonstration]]                                           |

### Rubric weights (100 pts)

```mermaid
pie showData
    title Assessment rubric
    "Functional prototype" : 30
    "Math model and transfer function" : 15
    "Testing and comparison" : 15
    "System design and block diagram" : 10
    "Simulation and analysis" : 10
    "Short report" : 10
    "Demo and explanation" : 10
```

---

## 6. Expected results

> [!success] What to show in the report
> - **Open-loop:** accurate only when conditions match the calibration run. Error grows with a pinched tube or a different tube height.
> - **Closed-loop:** lands near the target regardless of those changes. Small errors remain from residual dripping and the sensor resolution.
> - **Serial result:** open-loop ends well away from 100% when conditions change (for example about 80% with a pump 20% weaker than estimated). Closed-loop ends at about 100% to 102%. Copy the `RESULT` lines for the report.

Overshoot after the pump stops is roughly the flow at shutoff multiplied by the pump time constant plus the sensor filter delay $T_f$:

$$\Delta V_{overshoot} \approx Q_{stop}\,(\tau + T_f)$$

With $Q_{stop} = 5\ \text{ml/s}$ and $T_f \approx 0.1$ to $0.2\ \text{s}$, the filter adds only about 1 ml.

### Sensor resolution

The HC-SR04 is good to about $\pm 3\ \text{mm}$. The volume uncertainty is

$$\Delta V_{sensor} \approx A \times 0.3\ \text{cm}$$

For a 6 cm wide glass ($A \approx 28\ \text{cm}^2$) this is about $\pm 8\ \text{ml}$, and for a 5 cm wide glass ($A \approx 20\ \text{cm}^2$) about $\pm 6\ \text{ml}$. Open-loop errors from a weaker pump are tens of ml, so feedback still clearly wins.

> [!tip] Report limitation and improvement
> **Limitation:** sensor resolution and residual dripping limit closed-loop accuracy.
> **Improvement:** a narrower glass (finer ml per mm), a drain pump or valve, or PWM slowdown near the target.

---

## 7. Scope notes

> [!warning] Keep it simple
> - The PDF emphasizes basic control concepts over a sophisticated system.
> - Get the topic approved by your instructor first (one approved topic per group). Mention that this is a **dispense-a-set-volume variant of PDF topic D** (water-level control), using an on-off controller.
> - A drain pump for removing excess water is deliberately **out of scope** to save cost and reduce failure risk.
