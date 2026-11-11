---
title: Data Collection Guide
aliases: [Missing Data, Bench Trials, Measurement Guide]
tags: [project, testing, data, report]
status: active
---

# Data Collection Guide

**Notes in this project:** [[01-project-overview|Overview]] · [[02-material-requirements|Materials]] · [[03-project-procedure|Procedure]] · [[04-project-simulation|Simulation]] · [[05-component-testing|Component Testing]]

This guide lists the data still needed by the report and how to obtain each value. The files in `data/` currently contain only the three full-drive calibration runs used for K̂ₕ and τ. They do not contain the component checks, the 3 cm and 6 cm comparison trials, or the disturbance runs.

Use the report tables in [[chapter-4-results-and-discussion]] as the destination for the measurements.

---

## 1. What still needs to be recorded

| Report location | Missing data | Priority |
| --- | --- | --- |
| Table 3.3 | Component/bench test results: Nano, button/switch, HC-SR04 accuracy and stability, HC-SR04 level response, battery voltage, diode, pump, driver, grounds, dry/noise test | Required |
| Table 2.5 and Tables 4.6–4.8 | Battery voltage before and during each run | Required |
| Tables 4.6–4.7 | Open-loop and closed-loop trials at r = 3 cm and r = 6 cm, three trials each | Required |
| Table 4.8 | Pinched-tube disturbance at r = 6 cm, three trials per mode | Required |
| Table 4.9 | Averages, percent errors, and observations for the comparison | Required |
| Table 4.5 | Wokwi `RESULT` lines, if the simulation is rerun with the final code | Optional |
| Figure 4.1 | Measured open-loop run overlaid on the model plot | Optional |

The model values already established are **K̂ₕ = 0.367 cm/s**, **τ ≈ 0.66 s**, and **G(s) = 0.367 / [s(0.66s + 1)]**. The open-loop run times are **8.2 s for 3 cm** and **16.4 s for 6 cm**.

---

## 2. Equipment and setup

Use the same hardware for every trial: same pump, silicone tube, battery pack, glass, sensor height, and tube outlet position.

- Ruler taped vertically on the glass, 0 mark at the inside floor.
- Multimeter for battery voltage, diode check, and continuity.
- Stopwatch or phone for the pump calibration and timing checks.
- Small clip for the pinched-tube disturbance.
- Arduino IDE Serial Monitor at **9600 baud**.
- Splash guard and tray in place; keep water away from the breadboard and HC-SR04.

Before each run, confirm that the glass is empty and dry, the pump is submerged, the tube outlet is against the inner glass wall, and the sensor is pointing straight down.

---

## 3. How to obtain each data set

### A. Component and bench checks (Table 3.3)

Use the sketches in [[05-component-testing]]. Record the result of each test, not just "pass".

1. **Arduino Nano:** upload the blink/Serial sketch. Record `Pass` if the LED blinks and Serial prints at 9600 baud.
2. **Button and switch:** print D2 and D4. Record `Pass` if the button shows PRESSED/released and the switch shows OPEN-LOOP/CLOSED-LOOP correctly.
3. **HC-SR04 accuracy:** point the sensor at a flat target at 10, 20, and 30 cm. Record the average reading and the error in cm.
4. **HC-SR04 stability:** take 20 readings at one fixed distance. Record the spread (max − min) in cm and the number of missed echoes.
5. **HC-SR04 level response:** with the empty glass in place, record d₀. Pour water to 3.0 cm on the ruler and record d₃; repeat at 6.0 cm. Record the computed rises d₀ − d₃ and d₀ − d₆.
6. **Battery pack:** measure the no-load voltage and the voltage while the pump runs. Record both in volts.
7. **Flyback diode:** multimeter in diode mode. Record `Pass` if it conducts in one direction only.
8. **Pump alone:** connect it briefly to the battery with the pump submerged. Record `Pass` if the flow is steady.
9. **Driver and pump:** run the PWM ramp test. Record `Pass` if the pump speed follows PWM and stops at 0.
10. **Grounds and isolation:** continuity check. Record `Pass` if the Nano-side grounds are joined, the battery − is separate, and there are no shorts.
11. **Dry run and noise test:** run both modes with the pump in the reservoir. Record `Pass` if both modes finish and Serial shows no glitches or resets.

### B. Main open-loop and closed-loop trials (Tables 4.6 and 4.7)

Make one block of runs for each target and mode.

1. Set `TARGET_CM = 3.0`, keep `RISE_CM_PER_S = 0.367`, and upload the code.
2. Record the battery no-load voltage.
3. Select **open-loop** on the switch (D4 HIGH). Press **Start**.
4. Wait for the 3 s settle, then record:
   - the `RESULT` line (`final`, `error`, `percent`);
   - the real level rise on the ruler, read at eye level;
   - the battery voltage while the pump was running, if it was observed.
5. Empty and dry the glass. Repeat for three trials.
6. Switch to **closed-loop** (D4 LOW) and repeat steps 3–5 for three trials.
7. Set `TARGET_CM = 6.0`, re-upload, and repeat the open-loop and closed-loop blocks.

Compute:

$$e_{abs} = |h_{real} - r|, \qquad e_{\%} = \frac{e_{abs}}{r}\times 100\%$$

Alternate the mode order between blocks (open first, then closed, or the reverse) so that battery drift does not always favour one mode. Keep the same battery pack throughout; if the pack falls below about 6 V, replace it and note the change.

### C. Disturbance test (Table 4.8)

1. Set `TARGET_CM = 6.0`.
2. Fit the same clip position on the silicone tube for both modes. Mark the clip position so it can be repeated.
3. Run three open-loop trials and record the ruler rise, error, and percent of target.
4. Run three closed-loop trials with the same pinch.
5. For the open-loop result, estimate the rate ratio from the final level:

$$\alpha \approx \frac{h_{real}}{r}$$

A pinched tube should give α < 1. The open-loop error should be close to r(1 − α), while the closed-loop error should stay near the overshoot plus sensor resolution. Do not use weak batteries as the disturbance.

### D. Response to a change in target

Compare the 3 cm and 6 cm blocks. Open-loop should change its run time from 8.2 s to 16.4 s; closed-loop should simply stop at the new measured level. Record one sentence for each mode in Table 4.9.

### E. Optional Wokwi check

If the simulation is rerun with `SIMULATE 1` and `TARGET_CM = 6.0`, copy the two `RESULT` lines into Table 4.5. If the measured `K̂ₕ = 0.367` is used in the simulation, record that value and the resulting overshoot instead of the placeholder values.

---

## 4. Recording rules

- Read the ruler at eye level, to the nearest **0.1 cm**.
- Record battery voltage to **0.01 V**.
- Empty and dry the glass between trials; do not start a trial with leftover water.
- Take the tare with the empty glass before every run.
- Keep the sensor height and tube position fixed; a moved sensor changes d₀.
- Record the `RESULT` line exactly as printed, even when it disagrees with the ruler.
- Note any spill, ripple, reset, or unusual pump sound in the Notes column.
- Take one photo or screenshot of the Serial `RESULT` line for at least one trial per block.
- Do not invent or estimate missing measurements; leave the cell blank and repeat the trial.

---

## 5. Master recording table

Copy this table for each block: **3 cm open**, **3 cm closed**, **6 cm open**, **6 cm closed**, **6 cm pinched open**, and **6 cm pinched closed**. The first three rows below are **sample values only**, to show the expected format; replace them with real measurements. The remaining rows are blank for recording.

| Row | Test block | Trial | Battery no-load (V) | Battery under load (V) | d₀ (cm) | Arduino final (cm) | Ruler final rise (cm) | Abs. error (cm) | Percent error (%) | Notes |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | 3 cm open | 1 | 6.30 | 6.12 | 14.02 | 3.04 | 3.0 | 0.00 | 0.0 | SAMPLE ONLY |
| 2 | 3 cm open | 2 | 6.28 | 6.10 | 14.02 | 3.02 | 3.1 | 0.10 | 3.3 | SAMPLE ONLY |
| 3 | 3 cm closed | 1 | 6.29 | 6.11 | 14.01 | 3.21 | 3.2 | 0.20 | 6.7 | SAMPLE ONLY; overshoot |
| 4 |  |  |  |  |  |  |  |  |  |  |
| 5 |  |  |  |  |  |  |  |  |  |  |
| 6 |  |  |  |  |  |  |  |  |  |  |
| 7 |  |  |  |  |  |  |  |  |  |  |
| 8 |  |  |  |  |  |  |  |  |  |  |
| 9 |  |  |  |  |  |  |  |  |  |  |
| 10 |  |  |  |  |  |  |  |  |  |  |

---

## 6. After collection

1. Compute the average absolute error and average percent error for each mode and target.
2. Fill Tables 4.6, 4.7, 4.8, and 4.9 in [[chapter-4-results-and-discussion]].
3. Complete the three discussion paragraphs that are currently marked pending.
4. Replace the sample table entries with the real values and keep the raw Serial logs with the report.
