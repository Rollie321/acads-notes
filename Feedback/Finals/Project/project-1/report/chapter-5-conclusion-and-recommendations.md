# Chapter 5. Conclusion and Recommendations

Automatic Water-Level Control: Open-Loop versus Closed-Loop Operation
Feedback and Control Systems Performance Task · BS Computer Engineering, Third Year

---

## 5.1 Conclusion

The project achieved its three objectives. A working prototype was built using an Arduino Nano, an HC-SR04 ultrasonic sensor, and a submersible pump driven through an isolated MOSFET module. The system dispensed water to the 3 cm and 6 cm targets, and the real level was checked against a ruler. The closed-loop mode ended 0.13 cm from the 3 cm target and 0.43 cm from the 6 cm target, while the open-loop mode ended 0.34 cm and 0.47 cm away. The system therefore dispensed water close to the target, and feedback gave the better result at the lower target.

The plant, reference input, output, sensor, actuator, controller, and feedback path were identified and presented in a block diagram. The plant was modeled as a first-order pump lag in series with the glass integrator, giving $G(s) = 0.367/[s(0.66s + 1)]$. The calibration runs gave $\hat{K}_h = 0.367$ cm/s and $\tau \approx 0.66$ s, and the simulated step response matched the measured data to within 0.07 to 0.09 cm.

The closed-loop system used the sensor reading to decide when to stop the pump, so it corrected its own error instead of relying only on a fixed run time. This was clearest when the tube was pinched: the open-loop mode stopped at 5.03 cm (0.97 cm short), while the closed-loop mode reached 6.38 cm (0.38 cm from the target). The measured disturbance strength, $\alpha \approx 0.84$, predicted the open-loop error almost exactly and did not affect the closed-loop result in the same way. The main limitation is that the HC-SR04 resolution and the water that continues to drip after shut-off add about 0.3 cm of uncertainty, which is why the two modes were nearly tied at 6 cm.

## 5.2 Recommendations

The closed-loop result can be improved by reducing the overshoot. The controller can stop the pump slightly before the target using `STOP_EARLY_CM = 0.2` to `0.3`, or slow the pump near the target using `SLOW_ZONE_CM = 1.5` and `PWM_SLOW = 150`. A simple PI or PWM-based controller would give a smoother approach and is the next step if better accuracy is needed.

The sensor readings should also be improved. The HC-SR04 should be calibrated against the ruler at several levels, read with more pings, and protected from surface ripples with a longer settle time or a small baffle. Using the same battery voltage for calibration and trials, taking more trials, and logging the full time history would make the results more repeatable and easier to compare.

For future work, a drain pump or valve would allow the system to correct an overshoot instead of only underfilling, and a more precise level sensor would reduce the remaining uncertainty. With these changes, the same control concept can reach the target more consistently.
