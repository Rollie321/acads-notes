## Idea List
### List 1:
> [!tldr]- **Piezo Piano (Button Musical Instrument)**
> Line up 3 to 5 pushbuttons and a tiny piezo buzzer on your breadboard. Assign a musical note (like C, D, E, F, G) to each button to create a mini electronic keyboard.
 
> [!tldr]- **The "Ping" Indicator (Network Status LED)**
> 
> Program the ESP32 to ping your home router or a website (like google.com) every 10 seconds. Light up a **Green LED** on your breadboard if the internet is connected, and a **Red LED** if your Wi-Fi goes down.

> [!tldr]- **Digital Dice Roller**
>  
>  Place a pushbutton and a sequence of 6 LEDs on your breadboard. Pressing the button causes the LEDs to flash rapidly and then stop on a random "rolled" number from 1 to 6.

> [!tldr]- Optomechatronic Luminance Calibration Loop
> 
> - **The Functionality:** An LED shines across a small gap on the breadboard into a photoresistor (LDR). A micro servo motor moves a small plastic flag up and down to physically block the light path (acting as the variable system dynamic). The Arduino monitors the LDR voltage. If ambient light changes or the LED is dimmed, the Arduino drives the servo to adjust the mechanical flag position, stabilizing the system at the maximum safe target brightness.
> - **Transfer Function Type:** 2nd-Order Electromechanical (Servo position dynamics + Optical attenuation).

### List 2:

> [!TLDR]- DC Motor Speed Control System
> 
> Functionality: An Arduino controls the speed of a small DC motor using PWM. A speed sensor measures the motor's RPM, allowing the Arduino to adjust the PWM output to maintain the desired speed.
> 
> Second-order model: Motor electrical and mechanical dynamics, including armature inductance and rotor inertia.
> 
> Components: DC motor, transistor or MOSFET, flyback diode, speed sensor, and optional potentiometer.
> 
> Estimated extra components: ₱50–₱150

> [!TLDR]- Automatic Light Intensity Control
> 
> Functionality: An LDR measures ambient light, and the Arduino adjusts LED brightness to maintain a target light level.
> 
> Second-order model: Add a small RC sensor-filter stage and model the combined dynamics, or characterize the LED and sensor response experimentally.
> 
> Components: LDR, LED, resistors, capacitor, and transistor.
> 
> Estimated extra components: ₱30–₱80

> [!TLDR]- Motor Acceleration and Braking Analyzer
> 
> Functionality: Arduino starts and stops a small motor while measuring its speed over time.
> 
> Second-order model: A motor with electrical inductance and mechanical inertia; compare its measured speed response with a simplified model.
> 
> Components: DC motor, transistor, diode, and optional optical speed sensor.
> 
> Estimated extra components: ₱50–₱150

> [!TLDR]- Automatic Position Correction System
> 
> Functionality: A potentiometer measures the position of a small movable arm. Arduino commands a motor to correct position errors.
> 
> Second-order model: A motor-driven position plant can be modeled as a second-order system under suitable assumptions.
> 
> Components: DC motor, potentiometer, transistor or motor driver, and Arduino.
> 
> Estimated extra components: ₱80–₱180

> [!TLDR]- An Active Self-Balancing Tabletop Platform
> ####  Project Description
> 
> This project is a miniature, breadboard-mounted prototype of an automated self-balancing platform. It is designed to physically counteract external tilts and disturbances in real-time. By utilizing an inertial sensor to detect changes in orientation, an Arduino microcontroller dynamically drives an actuator to adjust the tabletop angle. This ensures the miniature platform surface remains perfectly flat and horizontal relative to the Earth, regardless of how the base is rotated.
> 
> ---
> 
> #### System Functionality
> 
> - **Real-Time Attitude Sensing:** Continually samples 3-axis gravitational and rotational data to detect any manual tilting or displacement of the breadboard base.
> - **Automatic Position Correction:** Computes orientation errors instantaneously using an Arduino microcontroller to offset angular shifts.
> - **Active Counter-Rotation:** Drives a motorized axis in the exact equal and opposite direction of the base movement to preserve a level platform surface.
> - **Startup Automatic Calibration:** Executes a "zeroing" phase upon power-up, defining the initial flat surface as the baseline reference point.


## Prompts:

> [!QUOTE]- Prompt 1 
> ```
> I'm a computer engineering student making a simple project for a subject feedback systems.
> 
> We need to build something in breadboard only, using either esp32 or arduino.
> 
> The sample that my professor gave me is an electric fan with motor and arduino.
> 
> The task here is to build a simple circuit, no need for fancy stuff, we just need to use either arduino or esp32. After that we have to provide a transfer function, calculation of transient response as well as damping.
> 
> This means the project should have a second order function that can be presented (MATLAB is allowed to use here.)
> 
> But right we prefer Arduino for cheap project.
> 
> So now, I want you to provide me a list of ideas to be built as a project. Make it cheap andsimple only. No casing, just breadboard and Arduino/ESP32 (arduino preferably).
> 
> list down 20 ideas, by providing project name and functionality.
> ```
> 