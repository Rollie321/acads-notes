# Automatic Water-Level Control: Open-Loop versus Closed-Loop Operation

Feedback and Control Systems Performance Task · BS Computer Engineering, Third Year

Group members: Rollie, Angelica, Leira, Charlyne · Instructor: Efren · Date: 11 November 2026

## 1. Introduction and Objectives

### 1.1 Application

Controlling the level of a liquid in a tank is one of the most common problems in process and automation engineering. Household water tanks, beverage dispensers, aquariums, irrigation reservoirs, and industrial storage vessels all need to hold or reach a desired water level without constant human attention. A system that does this must decide when to start the pump, how long to run it, and when to stop.

This project builds a small automatic water-level control system on a laboratory scale. An Arduino Nano controls a submersible DC pump that fills a straight-sided glass. An HC-SR04 ultrasonic sensor, mounted above the glass and pointing straight down, measures the distance to the water surface. The controlled output is the level rise, y = d₀ − d, where d₀ is the empty-glass distance (measured automatically at the start of each run) and d is the current distance to the water. The system is implemented in two modes, open-loop and closed-loop, so the effect of feedback can be observed directly on the same hardware.

### 1.2 Control Problem

The control problem is to raise the water level in the glass by a selected target height r (in cm) and stop as close to that target as possible, even when operating conditions change.

In **open-loop** operation, the controller does not use the sensor. It runs the pump for a fixed, pre-calculated time t_run = r / K̂ₕ, where K̂ₕ is the level rise rate at full pump drive estimated during calibration. This works only if the pump behaves exactly as it did during calibration. If the real rise rate differs from the estimate, for example because of a pinched tube, a different tube height, or a weaker pump, the final level misses the target. For a true rate K_h,true = α·K̂ₕ, the open-loop steady-state error is r(1 − α).

In **closed-loop** operation, the sensor measures the level rise continuously and the controller compares it with the target. An on-off controller keeps the pump running while the error e = r − y is positive and switches it off when the error reaches zero or less. Because the controller acts on the measured output, the final level should not depend on the actual pump rate. The plant (pump, tubing, and glass) behaves as an integrator with a first-order pump lag, and is modeled by G(s) = K_h / [s(τs + 1)].

The project therefore examines a central question of control theory: how much does feedback improve accuracy and robustness compared with a controller that relies only on a model?

### 1.3 Objectives

**General objective.** To design, simulate, construct, and test a water-level control system that demonstrates the difference between open-loop and closed-loop control.

**Specific objectives.** The project aims to:

1. Identify the plant, reference input, output, sensor, actuator, controller, and feedback path, and present them in a block diagram.
2. Develop a simplified mathematical model of the pump, tubing, and glass and obtain the plant transfer function G(s), stating all assumptions.
3. Simulate the open-loop step response of the plant using the measured or estimated values of K_h and τ.
4. Build a working prototype with an Arduino Nano, HC-SR04 sensor, and an optocoupler-isolated MOSFET pump driver, and implement both a timed open-loop mode and an on-off closed-loop mode.
5. Test both modes at two reference settings (3 cm and 6 cm level rise), with three trials each, and compute the absolute steady-state error.
6. Apply a disturbance (such as a pinched tube) to both modes and compare their responses.
7. Explain whether feedback improves accuracy, and whether the measured results agree with the model and simulation.

### 1.4 Scope and Limitations

This project emphasizes fundamental control concepts rather than a sophisticated system. It uses a low-voltage (about 6 V) supply, a straight-sided glass so the level rise rate stays constant, and an on-off controller only. No glass volume or cross-sectional area is calculated, since only the level matters. A drain pump is deliberately excluded to limit cost and risk. The accuracy of the closed-loop mode is limited by the sensor resolution (about ±3 mm) and by residual dripping after the pump stops.
