
> [!TLDR]- Project Manifest & Implementation Strategy
> 
> ---
> 
> 📦 **System Architecture & Component Mapping**
> 
> Instead of a loose mechanical assembly, the **entire breadboard serves as the active platform surface**. All electronic intelligence is self-contained on the moving plane, mitigating mechanical wire fatigue.
> 
> - **The Controller (The Brain):** **Arduino Nano** (or Uno), mounted centrally on the breadboard.
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
>    $$e(t) = \text{Target Angle } (0^\circ) - \text{Current Angle } (y(t))$$
> 4. **2nd-Order Closed-Loop Correction:** Rather than executing an algebraic mapping, the Arduino continuously processes the error dynamics to send real-time Pulse Width Modulation (PWM) position updates to the servo. The servo counter-rotates the entire breadboard mass back toward the baseline plane.
> 
> ---
> 
> 🎓 **Academic Defense Plan (The Mathematical Modeling)**
> 
> To satisfy your professor’s constraints, you will use the physical properties of this specific architecture to justify your mathematical modeling:
> 
> - **Why it is a 2nd-Order Plant:** The plant is defined by the total mass and inertia ($J$) of the populated breadboard alongside the motor's damping characteristics ($B$). Accelerating this structural mass forces the physical transfer function to conform to a standard 2nd-order differential equation.
> - **Tuning the Transient Response:** The system parameters ($\zeta$ and $\omega_n$) will be shaped directly inside your software. Modifying your proportional code gain adjusts the "virtual spring stiffness" $\omega_n$, while adjusting your derivative code gain acts as a "virtual shock absorber" ($\zeta$).
> - **Experimental Metric Extraction:** You will introduce a physical step disturbance (tapping the edge of the tilting breadboard). By logging the transient curve over the serial monitor, you can capture **Peak Time ($T_p$)** and **Maximum Overshoot ($M_p$)** to calculate the system's exact damping ratio ($\zeta$) and natural frequency ($\omega_n$).
> [!info] 📦 Main Controller & Sensors
> * **Arduino Nano (or Uno)** 
>   * *Quantity:* 1
>   * *Purpose:* Acts as the central microcontroller processing your closed-loop feedback code.
> * **MPU-6050 6-Axis IMU Module** 
>   * *Quantity:* 1
>   * *Purpose:* Measures the physical tilt angle of the breadboard in real-time.
>   * *Estimated Local Cost:* ₱45 – ₱65

> [!INFO]- Project Materials
> > [!TLDR]- Main Controller & Sensors
> > * **Arduino Nano (or Uno)** 
> >   * *Quantity:* 1
> >   * *Purpose:* Acts as the central microcontroller processing your closed-loop feedback code.
> > * **MPU-6050 6-Axis IMU Module** 
> >   * *Quantity:* 1
> >   * *Purpose:* Measures the physical tilt angle of the breadboard in real-time.
> >   * *Estimated Local Cost:* ₱45 – ₱65
>
> > [!TLDR]- Actuators & Power
> > * **SG90 Micro Servo Motor**
> >   * *Quantity:* 1
> >   * *Purpose:* The second-order physical muscle that tilts the breadboard back to level.
> >   * *Estimated Local Cost:* ₱35 – ₱45
> > * **Mini USB (for Nano) or Type-B USB Cable (for Uno)**
> >   * *Quantity:* 1
> >   * *Purpose:* Powers the system from your laptop and streams live data back for your transient response graph.
>
> > [!TLDR]- Breadboard & Prototyping Hardware
> > * **Full-Sized Solderless Breadboard (830 Points)**
> >   * *Quantity:* 1
> >   * *Purpose:* Serves as both your circuit connector and the actual physical table surface that tilts.
> > * **Male-to-Male Jumper Wires**
> >   * *Quantity:* 4–6 wires
> >   * *Purpose:* Connects the MPU-6050 and Servo directly into the breadboard rails.
> > * **Double-Sided Foam Tape or Blue-Tack**
> >   * *Quantity:* Small amount
> >   * *Purpose:* Secures the servo horn (spinning arm) firmly to the underside center of your breadboard.
>
> > [!abstract]- Structural Base (DIY)
> > * **Heavy Anchor Base**
> >   * *Quantity:* 1 
> >   * *Purpose:* A heavy object resting on your desk (e.g., a thick piece of cardboard, a small block of wood, or a heavy plastic container) used to tape down and anchor the servo motor casing securely so it doesn't wobble.
> > 