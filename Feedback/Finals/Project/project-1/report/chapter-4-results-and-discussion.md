# Chapter 4. Results and Discussion

Automatic Water-Level Control: Open-Loop versus Closed-Loop Operation
Feedback and Control Systems Performance Task · BS Computer Engineering, Third Year

---

## 4.1 Overview

This chapter presents (1) the model parameters identified from the pump calibration runs, (2) the open-loop step response simulated from the model and compared with the measured data, (3) the experimental measurements in both modes at 3 cm and 6 cm, and (4) the open-loop versus closed-loop comparison, including the response to a disturbance. The true water level was always confirmed with the ruler taped on the glass.

---

## 4.2 Model Parameter Identification

The pump was run at full drive (u = 1) with an empty, dry glass, and the level rise was recorded every 1 s for three runs. A straight line h = m·t + b was fitted to each run by least squares. The raw per-second logs are `data/test-1.csv`, `data/test-2.csv`, and `data/test-3.csv`; `data/summary.csv` collects the per-run values.

**Table 4.1. Calibration runs (full pump drive)**

| Run      | Samples | Final level (cm) | Slope m = K_h (cm/s) | R²     | Time-axis crossing t₀ (s) |
| -------- | ------- | ---------------- | -------------------- | ------ | ------------------------- |
| 1        | 18      | 7.3              | 0.3708               | 0.9989 | n/a (clock started late)  |
| 2        | 21      | 7.2              | 0.3638               | 0.9984 | 0.606                     |
| 3        | 21      | 7.2              | 0.3660               | 0.9991 | 0.721                     |
| **Mean** |         |                  | **0.367 ± 0.004**    |        | **0.66**                  |

**Level rise rate K̂ₕ.** The mean slope is **0.367 cm/s**, and the three runs agree within 1% (standard deviation 0.004 cm/s). The fits are highly linear (R² ≥ 0.998), with residuals of about 0.06 to 0.09 cm, which is the 0.1 cm resolution of the readings. This supports the constant-rate assumption (Section 2.4, assumption 4): the straight-sided glass gives a steady level rise rate. For comparison, averaging the per-second differences instead of fitting a line gives 0.3585 cm/s, 2.3% lower, because that method depends only on the two end points.

**Time constant τ.** For the model $h(t) = K_h[t − τ(1 − e^{(−t/τ)})]$, the ramp asymptote $K_h(t − τ)$ crosses zero at t = τ. The fitted lines of Runs 2 and 3 cross zero at 0.606 s and 0.721 s, so **τ ≈ 0.66 s**. Run 1 is excluded from this average because its stopwatch started after the pump, so its time axis is offset (its line crosses zero at −1.87 s); its slope is still valid. Since the readings were taken every 1 s, τ cannot be resolved more finely than this, so it is stated as an **estimate**. It also includes any small start-up delay of the pump, tubing, and sensor.

**Table 4.2. Identified model parameters**

| Parameter | Value              | Source                                                 |
| --------- | ------------------ | ------------------------------------------------------ |
| K̂ₕ       | 0.367 cm/s         | Mean of 3 regression slopes                            |
| τ         | 0.66 s (estimate)  | Zero crossing of the fitted line, Runs 2 and 3         |
| T_f       | about 0.1 to 0.2 s | Median-of-3 filter and sampling (assumed, Section 2.4) |

The plant transfer function is therefore

$$G(s) = \frac{0.367}{s\,(0.66\,s + 1)}$$

and the open-loop run times used in the code are

$$t_{run} = \frac{r}{\hat{K}_h} = \frac{3}{0.367} = 8.2\ \text{s} \quad (r = 3\ \text{cm}), \qquad \frac{6}{0.367} = 16.4\ \text{s} \quad (r = 6\ \text{cm})$$

---

## 4.3 Simulation Results

### 4.3.1 Open-loop step response

Figure 4.1 shows the step response $h(t) = K_h\left[t − τ(1 − e^{−t/τ})\right]$ for a full-drive step, with the three measured runs overlaid (Run 1 shifted by 2.54 s to align its clock). The 3 cm and 6 cm targets are marked.

*Figure 4.1. Open-loop step response: model with K_h = 0.367 cm/s and τ = 0.66 s, compared with measured data.*

**Interpretation.**

- After a brief start-up lag of about τ, the level rises as a ramp with slope K_h. The curve has no steady value: the glass integrates the flow, so the level keeps rising for as long as the pump runs.
- The model agrees closely with the measurements. The root-mean-square difference is 0.08 cm for Run 1, 0.09 cm for Run 2, and 0.08 cm for Run 3. This is about the 0.1 cm resolution of the readings, so the first-order-lag-plus-integrator model is adequate for this plant.
- The model predicts that the level reaches 3 cm at 8.8 s and 6 cm at 17.0 s. The measured times are 8.8 to 9.0 s and 16.8 to 17.0 s.

**Table 4.3. Model versus measurement**

| Quantity               | Model | Measured                        |
| ---------------------- | ----- | ------------------------------- |
| Time to reach 3 cm (s) | 8.8   | 8.8, 9.0, 8.9 (Runs 2, 3, 1)    |
| Time to reach 6 cm (s) | 17.0  | 16.8, 17.0, 16.8 (Runs 2, 3, 1) |

### 4.3.2 Predicted behavior of the two controllers

A pump that switches off does not stop the water instantly. The lag in the pump carries on delivering water, but the total water delivered is still K_h times the on-time. Two consequences follow from the model:

- **Open-loop:** after the settle, the final level is K_true × t_run. It equals r exactly when K_true = K̂ₕ, whatever the value of τ, and it equals α·r when the real rate is α·K̂ₕ.
- **Closed-loop:** the pump is switched off when the measured y reaches r, and the lag and filter delay add a final overshoot of about

$$\Delta h_{overshoot} \approx K_h\,(\tau + T_f) = 0.367\,(0.66 + T_f) \approx 0.28 \text{ to } 0.32\ \text{cm}$$

**Table 4.4. Model predictions**

| Condition                               | r (cm) | Open-loop final level (cm) | Closed-loop final level (cm)              |
| --------------------------------------- | ------ | -------------------------- | ----------------------------------------- |
| Pump matches calibration (α = 1)        | 3      | 3.0 (error about 0)        | about 3.2 to 3.3 (overshoot 0.28 to 0.32) |
|                                         | 6      | 6.0 (error about 0)        | about 6.2 to 6.3                          |
| Pump 20% weaker (α = 0.8), pinched tube | 3      | 2.4 (error −0.6)           | about 3.2 (overshoot about 0.2)           |
|                                         | 6      | 4.8 (error −1.2)           | about 6.2                                 |

### 4.3.3 Controller simulation (Wokwi)

The controller logic was also tested in Wokwi with `SIMULATE 1`, using the plant model in the code with the pump 20% weaker than estimated (`SIM_GAIN = 0.8`).

**Table 4.5. Wokwi results** (target = 6.0 cm; software plant model with K̂ₕ = 0.20 cm/s, α = 0.8, τ = 0.5 s)

| Mode        | RESULT line                                                    | Final (cm) | Error (cm) | Percent |
| ----------- | -------------------------------------------------------------- | ---------- | ---------- | ------- |
| Open-loop   | `RESULT,open,target=6.0,final=4.80,error=-1.20,percent=80.0`   | 4.80       | −1.20      | 80.0    |
| Closed-loop | `RESULT,closed,target=6.0,final=6.08,error=0.08,percent=101.3` | 6.08       | +0.08      | 101.3   |

With the weaker pump, the open-loop run ends at 80% of the target, while the closed-loop run lands almost exactly on it. These are outputs of the software plant model, not bench measurements. The small closed-loop overshoot (0.08 cm) comes from the simulated pump lag, Δh ≈ Q_h,stop·τ = 0.16 × 0.5.

---

## 4.4 Experimental Measurements

> **Data status.** The three CSV files in `data/` are the full-drive calibration runs used for K̂ₕ and τ. They do not contain the 3 cm and 6 cm open-loop/closed-loop trials or the pinched-tube disturbance runs. The cells in Tables 4.6 to 4.9 therefore remain pending until the bench trial log is entered; no measured values are inferred from the model.

Each mode was tested at r = 3 cm and r = 6 cm with three trials. The real level rise was read on the ruler at eye level after the 3 s settle, and the absolute error was computed as e_abs = |h_real − r|.

**Table 4.6. Measurements at r = 3 cm**

| Mode        | Trial | Battery (V) | Arduino reading (cm) | Real rise, ruler (cm) | Abs. error (cm) |
| ----------- | ----- | ----------- | -------------------- | --------------------- | --------------- |
| Open-loop   | 1     | [__]        | [__]                 | [__]                  | [__]            |
| Open-loop   | 2     | [__]        | [__]                 | [__]                  | [__]            |
| Open-loop   | 3     | [__]        | [__]                 | [__]                  | [__]            |
|             |       |             |                      | **Average error**     | **[__]**        |
| Closed-loop | 1     | [__]        | [__]                 | [__]                  | [__]            |
| Closed-loop | 2     | [__]        | [__]                 | [__]                  | [__]            |
| Closed-loop | 3     | [__]        | [__]                 | [__]                  | [__]            |
|             |       |             |                      | **Average error**     | **[__]**        |

**Table 4.7. Measurements at r = 6 cm**

| Mode        | Trial | Battery (V) | Arduino reading (cm) | Real rise, ruler (cm) | Abs. error (cm) |
| ----------- | ----- | ----------- | -------------------- | --------------------- | --------------- |
| Open-loop   | 1     | [__]        | [__]                 | [__]                  | [__]            |
| Open-loop   | 2     | [__]        | [__]                 | [__]                  | [__]            |
| Open-loop   | 3     | [__]        | [__]                 | [__]                  | [__]            |
|             |       |             |                      | **Average error**     | **[__]**        |
| Closed-loop | 1     | [__]        | [__]                 | [__]                  | [__]            |
| Closed-loop | 2     | [__]        | [__]                 | [__]                  | [__]            |
| Closed-loop | 3     | [__]        | [__]                 | [__]                  | [__]            |
|             |       |             |                      | **Average error**     | **[__]**        |

### Disturbance test (pinched tube, r = 6 cm)

The same pinch was applied in both modes.

**Table 4.8. Response to a disturbance**

| Mode        | Trial | Real rise, ruler (cm) | Abs. error (cm) | Percent of target |
| ----------- | ----- | --------------------- | --------------- | ----------------- |
| Open-loop   | 1     | [__]                  | [__]            | [__]              |
| Open-loop   | 2     | [__]                  | [__]            | [__]              |
| Open-loop   | 3     | [__]                  | [__]            | [__]              |
| Closed-loop | 1     | [__]                  | [__]            | [__]              |
| Closed-loop | 2     | [__]                  | [__]            | [__]              |
| Closed-loop | 3     | [__]                  | [__]            | [__]              |

---

## 4.5 Open-Loop versus Closed-Loop Comparison

**Table 4.9. Required comparison (averages of three trials)**

| Test condition                             | Open-loop                                   | Closed-loop                                     |
| ------------------------------------------ | ------------------------------------------- | ----------------------------------------------- |
| Reference setting                          | 3 cm and 6 cm                               | 3 cm and 6 cm                                   |
| Measured output, r = 3 cm (cm)             | pending bench trial                         | pending bench trial                             |
| Measured output, r = 6 cm (cm)             | pending bench trial                         | pending bench trial                             |
| Absolute steady-state error, r = 3 cm (cm) | pending bench trial                         | pending bench trial                             |
| Absolute steady-state error, r = 6 cm (cm) | pending bench trial                         | pending bench trial                             |
| Response to a change in target (3 → 6 cm)  | Run time becomes 8.2 s then 16.4 s; error pending | Stops at the new target; error pending     |
| Response to a disturbance (pinched tube)   | Model predicts e = r(1 − α); value pending  | Model predicts near-target; value pending       |

---

## 4.6 Discussion

**Model and simulation.** The calibration runs are very linear and repeatable, so the pump-plus-glass behaves as a constant-rate integrator with a short lag, as modeled. The simulated step response in Figure 4.1 agrees with the measured data to within the reading resolution, and the model predicts the times to reach 3 cm and 6 cm to within about 0.2 s.

**Which configuration tracks the target more accurately.** Pending bench measurements. The comparison will use the average absolute errors in Tables 4.6 and 4.7: the model predicts open-loop to be nearly exact when the pump matches calibration, and closed-loop to land within about one overshoot above the target.

**Effect of the disturbance.** Pending bench measurements. The model predicts that a pinch reducing the pump rate to α·K̂ₕ makes open-loop end at α·r, an error of r(1 − α), while closed-loop remains near the target because it stops on the measured level. The pinched-tube result will give α ≈ h_real / r for comparison with Table 4.4.

**Consistency with the model.** The calibration data support the model: the three fits are linear with R² ≥ 0.998, the measured rates agree within 1%, and the model reproduces the measured times to reach 3 cm and 6 cm to within about 0.2 s. The open-loop and closed-loop trial errors still have to be compared with the predictions in Table 4.4, including whether the closed-loop overshoot is close to the predicted 0.28 to 0.32 cm.

**Points to note when interpreting the results.**

- With the pump matching its calibration, the model predicts open-loop to be nearly exact, while closed-loop on-off control carries an overshoot of roughly 0.3 cm (about 10% of the 3 cm target and 5% of the 6 cm target). Feedback is therefore expected to show its advantage mainly when conditions change: the pinched tube, a different tube or reservoir height, or a weaker pump. This is what the model means by "open-loop trusts the model, closed-loop trusts the measurement."
- Open-loop accuracy depends on K̂ₕ having been measured under the same battery, tube, and reservoir conditions as the test. The calibration used a ruler and a stopwatch, so a timing error of about 0.5 s corresponds to about 0.18 cm of level.
- The ultrasonic sensor resolution is about ±0.3 cm, so differences smaller than this between modes are not significant.
- The 1 s sampling interval of the calibration cannot resolve τ much below 1 s, so τ remains an estimate.

---

## 4.7 Summary and Conclusion

The calibration runs gave K̂ₕ = 0.367 cm/s and τ ≈ 0.66 s, giving G(s) = $\frac{0.367}{s(0.66s + 1)}$. The simulated step response agrees with the measured data to within about 0.1 cm, so the first-order-lag-plus-integrator model is adequate for this plant. The design, model, and simulation objectives were therefore met, and the exam-ready comparison is set up in Tables 4.6 to 4.9.

The main limitation is accuracy: the HC-SR04 resolution is about ±0.3 cm, and residual dripping after shut-off adds about 0.28 to 0.32 cm of overshoot in closed-loop mode. A useful improvement is to slow the pump near the target (`SLOW_ZONE_CM` with `PWM_SLOW`) or stop slightly early (`STOP_EARLY_CM`). The remaining step is to enter the bench trial values; at that point the final comparison can state which mode was more accurate and by how much.