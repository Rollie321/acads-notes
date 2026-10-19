> [!TLDR]- Project Manifest & Implementation Strategy
> 
> ---
> 
> 📦 **System Architecture & Component Mapping**
> 
> Instead of a loose mechanical assembly, the **entire breadboard serves as the active platform surface**. All electronic intelligence is self-contained on the moving plane, mitigating mechanical wire fatigue.
> 
> - **The Controller (The Brain):** **Arduino Nano**, plugged directly into the breadboard near its center. It is far lighter than an Uno, which keeps the tilting mass and inertia ($J$) low.
> - **The Feedback Sensor (The Eyes):** **MPU-6050 IMU**, pinned directly to the breadboard grid to capture real-time pitch/roll orientation relative to gravity.
> - **The Actuator (The Muscle):** **SG90 Micro Servo Motor**, anchored to a stationary desk fixture. Its horn (spinning arm) is physically coupled to the underside of the breadboard to act as a dynamic pivot axis.
> - **The External Interface:** A slack-looped USB cable connecting the Arduino to a PC for live data logging and transient analysis.
> 
> ---
> 
> ⚙️ **Core Functionality & Closed-Loop Logic**
> 
> The system operates as a **true 2nd-order closed-loop feedback position plant** through the following sequence:
> 
> ```
>     Desired Angle (0°)                               Physical Action
>           │                                                 │
>           ▼                                                 ▼
>    [Summation/Error] ──► [Arduino PID] ──► [Servo Motor] ──► [Breadboard Tilts]
>           ▲                                                         │
>           │                                                         ▼
>           └─────────────────── [MPU-6050 IMU Sensor] ◄──────────────┘
> ```
> 
> 1. **System Power-Up & Static Calibration:** Upon initialization, the system samples the MPU-6050 while resting on a level plane to establish a $0^\circ$ Reference Baseline.
> 2. **Continuous Attitude Polling:** The MPU-6050 continuously streams current angular displacement $y(t)$ via the I2C bus to the Arduino.
> 3. **Error Vector Computation:** The Arduino continuously calculates system error:  
>     $$e(t) = \text{Target Angle } (0^\circ) - \text{Current Angle } (y(t))$$
> 4. **2nd-Order Closed-Loop Correction:** Rather than executing an algebraic mapping, the Arduino continuously processes the error dynamics to send real-time Pulse Width Modulation (PWM) position updates to the servo. The servo counter-rotates the entire breadboard mass back toward the baseline plane.
> 
> ---
> 
> 🎓 **Academic Defense Plan (The Mathematical Modeling)**
> 
> To satisfy your professor’s constraints, you will use the physical properties of this specific architecture to justify your mathematical modeling:
> 
> - **Why it is a 2nd-Order Plant:** The plant is defined by the total mass and inertia ($J$) of the populated breadboard alongside the motor's damping characteristics ($B$). Accelerating this structural mass forces the physical transfer function to conform to a standard 2nd-order differential equation.
>     
> - **Tuning the Transient Response:** The system parameters ($\zeta$ and $\omega_n$) will be shaped directly inside your software. Modifying your proportional code gain adjusts the "virtual spring stiffness" $\omega_n$, while adjusting your derivative code gain acts as a "virtual shock absorber" ($\zeta$).
>     
> - **Experimental Metric Extraction:** You will introduce a physical step disturbance (tapping the edge of the tilting breadboard). By logging the transient curve over the serial monitor, you can capture **Peak Time ($T_p$)** and **Maximum Overshoot ($M_p$)** to calculate the system's exact damping ratio ($\zeta$) and natural frequency ($\omega_n$). [!info] 📦 Main Controller & Sensors
>     
> - **Arduino Nano (ATmega328P)**
>     
>     - _Quantity:_ 1
>     - _Purpose:_ Acts as the central microcontroller processing your closed-loop feedback code.
>     - _Buy note:_ Get one **with header pins already soldered** so it plugs straight into the breadboard. Low-cost clones usually use a CH340 USB chip, which works fine but may need a driver (see Section 4).
> - **MPU-6050 6-Axis IMU Module**
>     
>     - _Quantity:_ 1
>     - _Purpose:_ Measures the physical tilt angle of the breadboard in real-time.
>     - _Estimated Local Cost:_ ₱45 – ₱65

> [!INFO]- Project Materials
> 
> > [!TLDR]- Main Controller & Sensors
> > 
> > - **Arduino Nano (ATmega328P)**
> >     - _Quantity:_ 1
> >     - _Purpose:_ Acts as the central microcontroller processing your closed-loop feedback code.
> >     - _Buy note:_ Get one **with header pins already soldered** so it plugs straight into the breadboard. Low-cost clones usually use a CH340 USB chip, which works fine but may need a driver (see Section 4).
> > - **MPU-6050 6-Axis IMU Module**
> >     - _Quantity:_ 1
> >     - _Purpose:_ Measures the physical tilt angle of the breadboard in real-time.
> >     - _Estimated Local Cost:_ ₱45 – ₱65
> 
> > [!TLDR]- Actuators & Power
> > 
> > - **SG90 Micro Servo Motor**
> >     - _Quantity:_ 1
> >     - _Purpose:_ The second-order physical muscle that tilts the breadboard back to level.
> >     - _Estimated Local Cost:_ ₱35 – ₱45
> > - **USB cable matching your Nano's port**
> >     - _Quantity:_ 1
> >     - _Purpose:_ Powers the system from your laptop and streams live data back for your transient response graph.
> >     - _Check:_ Original Nanos use Mini-USB, but some clones use Micro-USB or USB-C. Use a **data** cable, not a charge-only one, or the board will not appear as a port.
> 
> > [!TLDR]- Breadboard & Prototyping Hardware
> > 
> > - **Full-Sized Solderless Breadboard (830 Points)**
> >     - _Quantity:_ 1
> >     - _Purpose:_ Serves as both your circuit connector and the actual physical table surface that tilts.
> > - **Male-to-Male Jumper Wires**
> >     - _Quantity:_ 4–6 wires
> >     - _Purpose:_ Connects the MPU-6050 and Servo directly into the breadboard rails.
> > - **Double-Sided Foam Tape or Blue-Tack**
> >     - _Quantity:_ Small amount
> >     - _Purpose:_ Secures the servo horn (spinning arm) firmly to the underside center of your breadboard.
> 
> > [!abstract]- Structural Base (DIY)
> > 
> > - **Heavy Anchor Base**
> >     - _Quantity:_ 1
> >     - _Purpose:_ A heavy object resting on your desk (e.g., a thick piece of cardboard, a small block of wood, or a heavy plastic container) used to tape down and anchor the servo motor casing securely so it doesn't wobble.

---

# 🛠️ Execution Guide & MATLAB Analysis

## 1. Execution Overview

|Phase|Goal|Output|
|---|---|---|
|1|Assemble hardware|Stable platform on servo|
|2|Wire the circuit|MPU-6050 + servo connected|
|3|Upload the firmware|PD controller running at 100 Hz|
|4|Test and tune|Platform self-levels|
|5|Record step response|`log.csv` of angle vs. time|
|6|MATLAB analysis|$T_p$, $M_p$, $\zeta$, $\omega_n$|

---

## 2. Hardware Assembly

1. **Anchor the servo.** Tape or clamp the SG90 casing to the heavy base so it cannot move. Position it at the edge of the desk so the horn can rotate freely.
2. **Attach the horn.** Fit the servo horn onto the output shaft with the servo at its **center position (90°)**. Keep the horn straight and use the screw that comes with it.
3. **Mount the breadboard.** Stick the **center of the underside** of the breadboard to the horn using double-sided foam tape. The tilt axis must run through the middle of the board, so the mass is balanced on both sides.
4. **Populate the breadboard.** Plug the Arduino Nano into the breadboard so it straddles the center channel, with its USB port facing the edge where the cable will leave. Place the MPU-6050 on the opposite side of the pivot so the weight stays balanced. Fix the MPU-6050 so it **cannot shift** relative to the board.
5. **Route cables.** Run the servo wires along the pivot axis so they do not pull on the board when it tilts. Leave a **slack loop** in the USB cable.

> [!warning] Mechanical rules
> 
> - The MPU-6050 must be rigidly fixed and flat on the board, otherwise you measure vibration instead of tilt.
> - The tilt axis of the servo must be the same axis the MPU-6050 uses for roll. If your angle reads zero while the board visibly tilts, the sensor is oriented on the wrong axis.
> - Keep the board's weight low and balanced. The SG90 has only about 1.8 kg·cm of torque.

---

## 3. Wiring

|MPU-6050 pin|Arduino Nano pin|Notes|
|---|---|---|
|VCC|5V|Most GY-521 modules have an onboard regulator. Use 3.3V if yours does not.|
|GND|GND||
|SDA|A4|I2C data|
|SCL|A5|I2C clock|
|AD0|GND|Sets I2C address to `0x68`|

|SG90 wire|Arduino pin|
|---|---|
|Brown / Black (GND)|GND|
|Red (V+)|5V|
|Orange / Yellow (signal)|**D9** (PWM)|

> [!tip] If the Arduino resets when the servo moves The servo's current spikes are dropping the supply voltage. Add a **100–470 µF capacitor** across the servo's 5V and GND, and use a USB port that can supply enough current.

> [!info] Arduino Nano pin notes
> 
> - The pin labels on the Nano are printed on the board: `A4`, `A5`, `D9`, `5V`, and several `GND` pins (any of them works).
> - Do not use `VIN` for the 5V supply. Use the `5V` pin.
> - The Nano has no power jack. It is powered through the USB cable, which also carries the serial data.
> - If the servo still causes resets, power it from a separate 5V source (for example a USB power bank) and **connect its GND to the Nano's GND**.

---

## 4. Firmware (Arduino IDE)

**Setup:** Install the Arduino IDE. Under **Tools → Board** choose **Arduino Nano**, under **Tools → Processor** choose **ATmega328P**, and select the COM port. Set the Serial Monitor to **115200 baud**.

> [!warning] Nano upload problems
> 
> - If the upload fails with `programmer is not responding` or `not in sync`, change **Tools → Processor** to **ATmega328P (Old Bootloader)** and upload again. This is very common on clone boards.
> - If no COM port appears, install the **CH340 driver** (most clones use this chip) and try a different, data-capable USB cable. The code only uses the built-in `Wire` and `Servo` libraries.

**How the code works:**

1. At power-up it holds still for about 2 seconds, then averages 200 samples to find the **0° reference** and the gyro bias (static calibration).
2. It estimates the angle using a **complementary filter**, which fuses the accelerometer (accurate long-term) and gyro (accurate short-term).
3. It computes the error $e = r - y$ and the control signal $u = K_p e + K_i\int e,dt - K_d,\dot{y}$.
4. It drives the servo around its 90° center position and logs `time, setpoint, angle` as CSV.

```cpp
#include <Wire.h>
#include <Servo.h>

const uint8_t MPU_ADDR = 0x68;
Servo servo;

// ---------- Tunable gains ----------
float Kp = 1.5;     // proportional: virtual spring stiffness (sets wn)
float Ki = 0.0;     // keep 0 at first
float Kd = 0.05;    // derivative: virtual damper (sets zeta)

// ---------- Settings ----------
const int   SERVO_PIN   = 9;
const int   DIR         = 1;      // change to -1 if the board tilts the wrong way
const float U_LIMIT     = 40.0;   // max servo travel from center (deg)
const float ALPHA       = 0.98;   // complementary filter weight
const unsigned long TS  = 10;     // sample time: 10 ms (100 Hz)

float angle = 0, angleOffset = 0, gyroOffset = 0;
float setpoint = 0, integral = 0;
unsigned long tPrev, tStart;

int16_t read16() {
  int16_t hi = Wire.read();
  int16_t lo = Wire.read();
  return (hi << 8) | lo;
}

void readMPU(float &accAngle, float &gyroRate) {
  Wire.beginTransmission(MPU_ADDR);
  Wire.write(0x3B);                       // first accel register
  Wire.endTransmission(false);
  Wire.requestFrom(MPU_ADDR, (uint8_t)14, (uint8_t)true);
  int16_t ax = read16();
  int16_t ay = read16();
  int16_t az = read16();
  read16();                               // skip temperature
  int16_t gx = read16();
  // gy, gz not used
  accAngle = atan2((float)ay, (float)az) * 180.0 / PI;   // roll from gravity
  gyroRate = gx / 131.0;                                  // deg/s at +/-250 dps
}

void setup() {
  Serial.begin(115200);
  Wire.begin();
  Wire.beginTransmission(MPU_ADDR);
  Wire.write(0x6B); Wire.write(0);        // wake up MPU-6050
  Wire.endTransmission(true);

  servo.attach(SERVO_PIN);
  servo.writeMicroseconds(1500);          // center
  delay(2000);                            // keep the board level and still

  // ---- Static calibration: define the 0-degree baseline ----
  float sa = 0, sg = 0, a, g;
  for (int i = 0; i < 200; i++) {
    readMPU(a, g);
    sa += a; sg += g;
    delay(5);
  }
  angleOffset = sa / 200.0;
  gyroOffset  = sg / 200.0;

  Serial.println("t_ms,setpoint,angle");
  tPrev = tStart = millis();
}

void loop() {
  // Send 's' in the Serial Monitor to toggle the setpoint between 0 and 10 deg
  if (Serial.available()) {
    char c = Serial.read();
    if (c == 's') setpoint = (setpoint == 0) ? 10.0 : 0.0;
  }

  if (millis() - tPrev < TS) return;
  float dt = (millis() - tPrev) / 1000.0;
  tPrev = millis();

  float acc, gyro;
  readMPU(acc, gyro);
  acc  -= angleOffset;
  gyro -= gyroOffset;

  // Complementary filter
  angle = ALPHA * (angle + gyro * dt) + (1.0 - ALPHA) * acc;

  // PID (derivative taken on the measurement to avoid kick on setpoint steps)
  float e = setpoint - angle;
  integral = constrain(integral + e * dt, -20, 20);
  float u = Kp * e + Ki * integral - Kd * gyro;
  u = constrain(u, -U_LIMIT, U_LIMIT);

  // 1500 us = center, about 11.1 us per degree on an SG90
  servo.writeMicroseconds(1500 + (int)(DIR * u * 11.1));

  Serial.print(millis() - tStart); Serial.print(',');
  Serial.print(setpoint);          Serial.print(',');
  Serial.println(angle);
}
```

---

## 5. Test and Tune

1. **Power up with the board level and still.** Wait for the 2-second calibration. Upload and power on only when the board is resting flat.
2. **Check the sign.** Gently tilt the board by hand. The servo should push **against** the tilt. If it pushes the same way, change `DIR` to `-1`.
3. **Start with `Kp` only.** Set `Kd = 0`, `Ki = 0`, and raise `Kp` from 0.5 upward until the board is stiff but oscillates. You should see a clear underdamped ringing.
4. **Add damping.** Increase `Kd` gradually (0.02, 0.05, 0.1...) until the oscillation dies out quickly with a small overshoot.
5. **Leave `Ki` at 0** unless there is a visible steady-state offset.

> [!tip] Typical symptoms
> 
> |Symptom|Likely cause|
> |---|---|
> |Violent buzzing or jitter|`Kd` too high (the gyro noise is amplified). Lower it.|
> |Slow, sluggish return|`Kp` too low|
> |Growing oscillation|`Kp` too high or `DIR` is wrong|
> |Angle drifts at rest|Calibration was done while the board was moving|

---

## 6. Recording the Transient Response

Use a **setpoint step**. A step input is the cleanest way to extract $T_p$ and $M_p$, because the standard formulas assume a step. The tap disturbance can then be used as a second test (Section 7, Part B).

1. Open the Serial Monitor at 115200 baud with the board settled at 0°.
2. Type `s` and press Enter. The setpoint jumps from 0° to 10° and the board responds.
3. Wait about 3 seconds for it to settle, then **stop** the logging.
4. Copy the output from the Serial Monitor into a text file and save it as `log.csv` (keep the header line).
5. Repeat for each gain setting you test and keep a separate file for each (e.g. `log_kp1.5_kd0.05.csv`).

> [!info] Alternative capture You can read the serial port directly in MATLAB with `s = serialport("COM3",115200);` and `readline(s)`, but copying from the Serial Monitor or a terminal tool such as CoolTerm is simpler and just as accurate.

For the **tap disturbance test**, keep `setpoint = 0`, tap the edge of the board once, and log the decaying oscillation.

---

## 7. MATLAB Analysis

### Theory used

For a standard underdamped 2nd-order system

$$G(s)=\frac{\omega_n^2}{s^2+2\zeta\omega_n s+\omega_n^2}$$

the step-response metrics are:

|Quantity|Formula|
|---|---|
|Percent overshoot|$M_p = \dfrac{y_{peak}-y_{ss}}{y_{ss}-y_0}\times 100%$|
|Peak time|$T_p = \dfrac{\pi}{\omega_d}$, where $\omega_d=\omega_n\sqrt{1-\zeta^2}$|
|Damping ratio|$\zeta = \dfrac{-\ln(M_p/100)}{\sqrt{\pi^2+\ln^2(M_p/100)}}$|
|Natural frequency|$\omega_n = \dfrac{\pi}{T_p\sqrt{1-\zeta^2}}$|
|Settling time (2%)|$T_s \approx \dfrac{4}{\zeta\omega_n}$|

For the tap (free-decay) test, use the **logarithmic decrement** over $N$ cycles:

$$\delta=\frac{1}{N}\ln\frac{x_0}{x_N},\qquad \zeta=\frac{\delta}{\sqrt{4\pi^2+\delta^2}},\qquad \omega_d=\frac{2\pi}{T_d}$$

### Part A: Step response, $T_p$, $M_p$, $\zeta$, $\omega_n$

```matlab
%% ---- Part A: step-response analysis ----
clear; clc; close all;

data = readmatrix('log.csv');          % columns: t_ms, setpoint, angle
t  = (data(:,1) - data(1,1)) / 1000;   % seconds
sp = data(:,2);
y  = data(:,3);

% Isolate the step: find the instant the setpoint changes
i0 = find(diff(sp) ~= 0, 1) + 1;
t  = t(i0:end) - t(i0);
y  = y(i0:end);
R  = sp(i0);                           % commanded step (deg)
y0 = 0;                                % starting angle

% Steady-state value (average of the last 0.5 s)
Ts_s = mean(diff(t));
nEnd = round(0.5 / Ts_s);
yss  = mean(y(end-nEnd:end));

% Peak time and overshoot
[ypk, ipk] = max(y);
Tp = t(ipk);
Mp = (ypk - yss) / (yss - y0) * 100;   % percent

% Damping ratio and natural frequency
zeta = -log(Mp/100) / sqrt(pi^2 + log(Mp/100)^2);
wn   = pi / (Tp * sqrt(1 - zeta^2));
wd   = wn * sqrt(1 - zeta^2);
Ts2  = 4 / (zeta * wn);                % 2% settling time

fprintf('Peak time   Tp   = %.3f s\n', Tp);
fprintf('Overshoot   Mp   = %.2f %%\n', Mp);
fprintf('Damping     zeta = %.3f\n', zeta);
fprintf('Nat. freq.  wn   = %.3f rad/s (%.2f Hz)\n', wn, wn/(2*pi));
fprintf('Settling    Ts   = %.3f s\n', Ts2);

% Build the identified model and compare with the measured data
G = tf(wn^2, [1 2*zeta*wn wn^2]);
opt = stepDataOptions('StepAmplitude', yss - y0);
[ym, tm] = step(G, opt, t(end));

figure;
plot(t, y, 'b', 'LineWidth', 1.5); hold on;
plot(tm, ym, 'r--', 'LineWidth', 1.5);
yline(yss, 'k:');
plot(Tp, ypk, 'go', 'MarkerFaceColor', 'g');
xlabel('Time (s)'); ylabel('Angle (deg)');
legend('Measured', 'Identified 2nd-order model', 'Steady state', 'Peak', ...
       'Location', 'southeast');
title(sprintf('Step response  (\\zeta = %.2f, \\omega_n = %.1f rad/s)', zeta, wn));
grid on;

% Cross-check with MATLAB's own metrics on the fitted model
stepinfo(G)
```

> [!warning] If `Mp` is 0 or negative The response is **overdamped** (no overshoot), so the peak-time formulas do not apply. Reduce `Kd` or increase `Kp` until you get a clear overshoot, then record again.

### Part B: Tap disturbance (logarithmic decrement)

```matlab
%% ---- Part B: free-decay after a tap ----
clear; clc; close all;

data = readmatrix('log_tap.csv');      % setpoint stays at 0
t = (data(:,1) - data(1,1)) / 1000;
y = data(:,3);

% Find successive positive peaks of the decaying oscillation
[pks, locs] = findpeaks(y, 'MinPeakProminence', 0.5);   % needs Signal Processing Toolbox
N = min(4, numel(pks) - 1);                             % use up to 4 cycles

delta = (1/N) * log(pks(1) / pks(N+1));                 % log decrement
zeta  = delta / sqrt(4*pi^2 + delta^2);

Td = mean(diff(t(locs(1:N+1))));                        % damped period
wd = 2*pi / Td;
wn = wd / sqrt(1 - zeta^2);

fprintf('Log decrement delta = %.3f\n', delta);
fprintf('Damping ratio zeta  = %.3f\n', zeta);
fprintf('Damped freq.  wd    = %.2f rad/s\n', wd);
fprintf('Natural freq. wn    = %.2f rad/s\n', wn);

figure; plot(t, y, 'b'); hold on; plot(t(locs), pks, 'ro');
xlabel('Time (s)'); ylabel('Angle (deg)'); grid on;
title('Tap response with detected peaks');
```

> [!info] No Signal Processing Toolbox? Replace `findpeaks` with a manual search: a sample is a peak if `y(k) > y(k-1) && y(k) > y(k+1) && y(k) > 0.5`.

### Part C: Compare against the theoretical prediction

Model the board as a rotating inertia with a PD controller acting as a virtual spring and damper. The ideal closed-loop parameters are

$$\omega_n=\sqrt{\frac{K_p}{J}},\qquad \zeta=\frac{B+K_d}{2\sqrt{K_p J}}$$

where $J \approx \dfrac{mL^2}{12}$ for a board of mass $m$ and length $L$ measured **perpendicular to the tilt axis**, and $B$ is the motor/servo damping. Here $K_p$ and $K_d$ must be expressed in torque units, so treat the equations as showing the **trends**, and use the measured $\zeta$ and $\omega_n$ as your authoritative values.

```matlab
%% ---- Part C: effect of gains on the measured response ----
% Fill in the values from each logged run
Kp_list   = [1.0  1.5  1.5  2.0];
Kd_list   = [0.05 0.02 0.05 0.10];
zeta_list = [ ];    % <-- results from Part A for each run
wn_list   = [ ];

T = table(Kp_list', Kd_list', zeta_list', wn_list', ...
    'VariableNames', {'Kp','Kd','zeta','wn_rad_s'});
disp(T)
```

Expected trend to confirm in your report:

- Increasing $K_p$ raises $\omega_n$ (faster, stiffer response) and slightly lowers $\zeta$.
- Increasing $K_d$ raises $\zeta$ (less overshoot, quicker settling) with little effect on $\omega_n$.

---

## 8. Results Table (fill in during experiments)

|Run|$K_p$|$K_d$|$T_p$ (s)|$M_p$ (%)|$\zeta$|$\omega_n$ (rad/s)|$T_s$ (s)|
|---|---|---|---|---|---|---|---|
|1||||||||
|2||||||||
|3||||||||
|4||||||||

---

## 9. Report Checklist

- [ ] Photo of the assembled platform and wiring diagram
- [ ] Block diagram of the closed loop (already above)
- [ ] Plot of measured vs. identified step response (Part A)
- [ ] Table of $T_p$, $M_p$, $\zeta$, $\omega_n$ for at least 3 gain settings
- [ ] Plot of the tap response with detected peaks (Part B)
- [ ] Discussion of how $K_p$ and $K_d$ moved $\omega_n$ and $\zeta$
- [ ] Sources of error (see below)

> [!abstract]- Limitations to mention in your discussion
> 
> - The SG90 has its **own internal position loop** and a limited speed (roughly 0.1 s per 60°). This adds dynamics beyond the ideal model, so the identified $\zeta$ and $\omega_n$ describe the **overall closed-loop behavior** of servo + board + controller.
> - The servo's command resolution and gear backlash cause small steady-state offsets and jitter.
> - The complementary filter introduces a small lag, and accelerometer readings are corrupted by motion and vibration.
> - A sample time of 10 ms is adequate for a response of roughly 1 Hz, but faster dynamics would need a higher rate.

---

# 🧪 Pre-Build Simulation (MATLAB)

Do this **before Section 2 (assembly)**. It predicts the response for your chosen $K_p$ and $K_d$, so you arrive at the hardware with sensible starting gains. The result is also a "theoretical vs. experimental" comparison for your report.

## 10.1 Model Used

The servo with the board attached behaves like a 2nd-order position system (motor inertia plus its internal position loop), with command angle in and board angle out:

$$G_p(s)=\frac{\omega_s^2}{s^2+2\zeta_s\omega_s s+\omega_s^2}$$

The firmware controller is $u = K_p(r-y) - K_d,\dot y$ (derivative on the measurement, taken from the gyro). Closing the loop gives

$$\frac{Y(s)}{R(s)}=\frac{K_p,\omega_s^2}{s^2+\left(2\zeta_s\omega_s+K_d,\omega_s^2\right)s+\omega_s^2(1+K_p)}$$

which is a standard 2nd-order system with

$$\omega_n=\omega_s\sqrt{1+K_p},\qquad \zeta=\frac{2\zeta_s\omega_s+K_d,\omega_s^2}{2,\omega_s\sqrt{1+K_p}},\qquad \text{DC gain}=\frac{K_p}{1+K_p}$$

This supports your defense plan: $K_p$ sets the virtual spring stiffness ($\omega_n$), and $K_d$ sets the virtual damper ($\zeta$).

> [!warning] Steady-state offset With $K_i = 0$ the DC gain is $K_p/(1+K_p)$, not 1. For example, $K_p = 1.5$ and a 10° step command gives a final angle of about **6°**. This does not affect the level-hold case (setpoint 0°), but it matters for the step test. Either report $y_{ss}$ as measured (Part A already does) or add a small $K_i$ (set `Ki` below to see its effect).

## 10.2 Simulation Script

Requires the Control System Toolbox. `ws` and `zs` are **placeholder values** for the servo and board. Replace them after you build (Section 10.3).

```matlab
%% ---- Pre-build simulation: servo + board with PD control ----
clear; clc; close all;
s = tf('s');

% Servo + board plant (PLACEHOLDERS, identify from hardware later)
ws = 40;          % rad/s  servo/board natural frequency
zs = 0.7;         % servo/board damping ratio
Gp = ws^2 / (s^2 + 2*zs*ws*s + ws^2);

% Optional: add the 10 ms sample delay of the 100 Hz loop (Pade approx.)
useDelay = true;
if useDelay
    Gp = Gp * pade(tf(1,1,'InputDelay',0.010), 2);
end

% Gains to compare (same units as the firmware)
Kp_list = [1.5  1.5  1.5  3.0];
Kd_list = [0    0.01 0.03 0.01];
Ki      = 0;      % try 0.5 to remove the steady-state offset
r       = 10;     % step command (deg)

figure; hold on; grid on;
for k = 1:numel(Kp_list)
    Kp = Kp_list(k);  Kd = Kd_list(k);
    C   = Kp + Ki/s;                                  % PI part on the error
    Gcl = minreal( Gp*C / (1 + Gp*(C + Kd*s)) );      % derivative on measurement

    [y, t] = step(r*Gcl, 1.5);
    plot(t, y, 'LineWidth', 1.5, ...
        'DisplayName', sprintf('Kp=%.1f, Kd=%.2f', Kp, Kd));

    info = stepinfo(Gcl);
    % Analytic values (exact when the delay is off)
    wn   = ws*sqrt(1+Kp);
    zeta = (2*zs*ws + Kd*ws^2) / (2*ws*sqrt(1+Kp));
    fprintf('Kp=%.1f Kd=%.2f | Tp=%.3f s  Mp=%.1f %%  Ts=%.3f s | zeta=%.2f wn=%.1f rad/s | final=%.2f deg\n', ...
        Kp, Kd, info.PeakTime, info.Overshoot, info.SettlingTime, ...
        zeta, wn, r*dcgain(Gcl));
end
yline(r, 'k:', 'Command');
xlabel('Time (s)'); ylabel('Board angle (deg)');
title('Simulated step response for different gains');
legend('Location', 'southeast');
```

### How to read the output

|What you see|What it means|
|---|---|
|`Kd = 0` gives a large overshoot and long ringing|Underdamped, so you need derivative action|
|Increasing `Kd` lowers $M_p$|The virtual damper is working|
|Increasing `Kp` shortens $T_p$|$\omega_n$ is increasing|
|Final value below the command|The $K_p/(1+K_p)$ offset|
|Violent response or instability with `useDelay = true`|Gains are too high for a 10 ms loop, so lower them|

Choose the 2 or 3 gain pairs that give a clean, slightly underdamped response ($\zeta \approx 0.4$–$0.7$) and use them as your **first values in the firmware**.

## 10.3 Replace the Placeholders with Real Values

The placeholder `ws` and `zs` strongly affect the result, so identify them once the hardware is built:

1. Set `Kp = 0`, `Kd = 0` in the firmware and command the servo directly, for example from 1500 µs to 1600 µs.
2. Log the board angle with the IMU and extract $\zeta_s$ and $\omega_s$ with the same Part A method from Section 7.
3. Put those numbers into `ws` and `zs`, rerun the simulation, and check how well it predicts your closed-loop recordings.

Put the **simulated vs. measured** step response on one plot in your report, and comment on any differences (servo rate limit, backlash, filter lag, sensor noise).

## 10.4 Simulating the Circuit and Arduino Code (Wokwi)

[Wokwi](https://wokwi.com/) is a free online simulator that runs your **actual Arduino code** against a virtual Arduino Nano, MPU-6050, and servo. It has a ready-made MPU-6050 part (I2C address `0x68`, set with accelerometer values in g and gyro values in °/s), so the register-level reads in the firmware can be tested.

**What it can and cannot tell you**

|Wokwi can check|Wokwi cannot check|
|---|---|
|The code compiles and runs on an Arduino|The board's real mechanical response|
|Wiring is correct (I2C, PWM pin)|Servo speed limits, backlash, vibration|
|Calibration, angle calculation, and PD output are logical|Sensor noise and drift|
|The servo moves in the **correct direction**|Tuning of $K_p$ and $K_d$ (use Section 10.2 for that)|
|CSV logging format||

The physical loop is **not closed** in the simulation. Moving the virtual servo does not change the virtual sensor, so _you_ act as the physics by changing the sensor values by hand.

### Setup

1. Go to wokwi.com, click **New Project**, and choose **Arduino Nano**.
    
2. Click the **+** button and add an **MPU6050** and a **Servo**.
    
3. Wire them exactly as in Section 3:
    
    |Part|Pin|Connect to|
    |---|---|---|
    |MPU6050|VCC|5V|
    |MPU6050|GND|GND|
    |MPU6050|SDA|A4|
    |MPU6050|SCL|A5|
    |Servo|GND|GND|
    |Servo|V+|5V|
    |Servo|PWM|D9|
    
4. Replace the contents of `sketch.ino` with the firmware from Section 4. `Wire` and `Servo` are built in, so no extra libraries are needed.
    
5. Press **Start Simulation**. Leave the MPU6050 at its default level values (X = 0, Y = 0, Z = 1 g) for the first 3 seconds, because that is when the code calibrates the 0° baseline.
    
6. Open the **Serial Monitor** in Wokwi to see the `t_ms,setpoint,angle` output. Click the MPU6050 while the simulation is running to change its acceleration and rotation values.
    

### Test checklist

For a tilt angle $\theta$, set the accelerometer to $a_y=\sin\theta$ and $a_z=\cos\theta$ (in g), with the gyro at 0 °/s.

|#|Test|Set the MPU6050 to|Expected result|
|---|---|---|---|
|1|Level hold|Y = 0, Z = 1|`angle` stays near 0 and the servo stays near center|
|2|Tilt +10°|Y = 0.174, Z = 0.985|`angle` rises toward 10 (takes about 1 s because of the filter) and the servo swings about 15° off center **in one direction**|
|3|Tilt −10°|Y = −0.174, Z = 0.985|The servo swings the **opposite** way to test 2|
|4|Direction check|Compare tests 2 and 3 with the real tilt|The servo must push **against** the tilt. If it does not, set `DIR = -1`|
|5|Saturation|Tilt about 60° (Y = 0.866, Z = 0.5)|The servo stops at its limit (±40° from center) and does not go further|
|6|Setpoint step|Level values, then type `s` in the Serial Monitor|The setpoint in the CSV changes to 10 and the servo moves about 15° to try to reduce the error|
|7|Derivative action|Level values, set rotation X to 50 °/s for a moment, then back to 0|The servo twitches immediately, which shows the $K_d$ term reacts to angular rate|

> [!warning] Keep the gyro at 0 °/s except for test 7 A constant gyro value in the simulator acts like a constantly rotating board. The filter integrates it and the angle will drift away. This is expected and is not a code bug.

### If something goes wrong

|Symptom|Likely cause|
|---|---|
|`angle` is always 0 or stuck|The I2C wiring is wrong (check SDA to A4, SCL to A5), or the MPU6050 is not powered|
|Servo does not move|PWM wire is not on D9, or the servo is not powered|
|Servo moves the wrong way|Set `DIR = -1`|
|Compile errors|The sketch was not fully replaced, or the code was truncated while pasting|

> [!info] If the raw I2C register reads do not work in the simulator The firmware reads the MPU-6050 registers directly. If Wokwi returns zeros anyway, try the Adafruit MPU6050 library (add it in the Library Manager) just to confirm the wiring and part, then go back to the direct-register code for the real hardware.

When all seven tests behave as expected, the wiring and control logic are verified. The remaining work (tuning and the transient response) has to be done on the real hardware.

# Image

![[table-stable-sample-image.png]]