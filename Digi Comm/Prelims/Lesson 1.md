# Digital Communications — Review Notes

---

## I. Pioneers of Communication Technology

### A. Guglielmo Marconi
- Guglielmo Marconi (1874–1937)
- Italian inventor AKA  father of practical **wireless telegraphy (radio)**.
- Proved radio waves used to send  over **long distances without wires**.

#### Wireless Telegraph System
- 1895, he built  at his family estate in Italy.
- 1901, first successful **transatlantic radio transmission**, sending a signal (the Morse letter "S") England to Newfoundland, Canada.
- laid  foundation for modern radio, broadcasting, and wireless communication.
- **1909 Nobel Prize in Physics** for contributions to wireless telegraphy.

### B. John Ambrose Fleming and the Vacuum Tube ("Audion")
 -  Invented  **Fleming valve** in 1904
  - First practical **thermionic (vacuum) tube**, also called a **diode**.

- Fleming valve **worked as rectifier/detector**
- Convert alternating radio signals into a usable direct current
- Allowed weak radio signals to be reliably detected and interpreted.
- Based on the "Edison effect" (observation current could flow between a heated filament and a metal plate inside a vacuum bulb).
-  Marked  **birth of electronics**.

>[!NOTE]
>Note: The **Audion** (a three-element vacuum tube, or **triode**) was actually invented in 1906 by **Lee de Forest**, an American inventor, who added a third electrode (the *grid*) to Fleming's diode design. This addition allowed the tube to **amplify** weak signals — not just detect them. Fleming's diode and de Forest's Audion are often studied together because the Audion was a direct improvement on Fleming's original valve, enabling long-distance radio and eventually broadcast communication.

---

## II. Elements of a Communication System

**Information Source → Encoder → Transmitter → Channel → Receiver → Decoder → Destination**

(Noise, distortion, and attenuation act on the signal as it passes through the channel.)
### Transmitter
 - Takes the message/information signal and converts it into a form suitable for transmission .
 - It typically performs
     - encoding
     - modulation
     - amplification

### Modulation
- process of varying a **carrier signal** (ampli, freq, or phase) according to the information signal so it can be efficiently transmitted.
- **Analog modulation:** AM (Amplitude Modulation), FM (Frequency Modulation), PM (Phase Modulation)
- **Digital modulation:** ASK, FSK, PSK, QAM

### Encoder
- Converts  original message (voice, text, data, etc.) into a **code / signal format** suitable for transmission 
- ***example***:
    - converting information into binary digits (bits) in digital systems.

### Communication Channel
The **physical medium or path** through which the signal travels from the transmitter to the receiver. It can be a wire, a fiber-optic cable, or free space (air/vacuum).

### Electrical Conductors
- Physical wired media, carry info as **electrical signals**.
- Simple and low-cost, but susceptible to electromagnetic interference and signal loss over distance.
- ***example :***
    - twisted-pair cable
    - coaxial cable

### Optical Media
-  AKA **fiber-optic cables**  transmit information as **pulses of light**.
- Offer:
    - very high bandwidth
    - low signal loss
    - Immunity to electromagnetic interference
    - ideal for high-speed, long-distance communication.

### Free Space / Wireless Radio
- Transmission of signals through  **air or vacuum**
- uses electromagnetic (radio) waves without any physical medium.
- Used in
    - radio
    - television
    - satellite
    - mobile communication
>[!WARNING] **Free Space** is more prone to noise and interference.

### Noise
- **Unwanted random signals**
- interfere with the transmitted signal, degrading quality and possibly causing errors at receiver.
- ***Common sources:*** 
    - thermal noise
    - atmospheric noise
     - Man-made electrical interference

### Distortion
- **alteration of a signal's waveform/shape**  as it passes through the channel
- usually caused by  imperfect (non-linear or limited-bandwidth) characteristics of the transmission medium or equipment.

>[!NOTE]
> Unlike noise, distortion is generally predictable/repeatable and can often be reduced
> through equalization.

### Attenuation / Signal Degradation
 - **gradual loss of signal strength (power)** as it travels through a medium
 - caused by resistance (in wires), absorption, or spreading of energy (in wireless/optical media).
 - Measured in decibels (dB); compensated for using **amplifiers or repeaters** along the transmission path.

### Decoder
* **reverses the encoding process**
- convert the received coded signal back into a form that represents the original message.

### Receiver
- accepts the transmitted signal from the channel and processes it
- Through:
    - amplification
    - demodulation
    - decoding
* to **recover the original message** for the destination.

---

## III. Types of Signals

### Analog Signal
* **continuous** signal that varies smoothly over time in amplitude, frequency, or phase.
- Directly represents continuous real-world quantities (e.g., sound waves, temperature).
- Susceptible to noise accumulation over distance.

### Digital Signal
- **discrete** signal represented using a finite set of values,
- typically binary (**0s and 1s**).
    
> [!NOTE]
>It is more resistant to noise and distortion, easier to process, store, encrypt, and regenerate
> accurately over long distances.

---



## IV. Types of Communication Channels / Media

| #   | Channel Type              | Description                                     | Example                     |
| --- | ------------------------- | ----------------------------------------------- | --------------------------- |
| 1   | **Electrical Conductors** | Wired media carrying electrical signals         | Twisted-pair, coaxial cable |
| 2   | **Optical Media**         | Light-based transmission through fiber          | Fiber-optic cable           |
| 3   | **Free Space**            | Wireless transmission via electromagnetic waves | Radio, satellite, Wi-Fi     |

---

## V. Modes of Transmission

### 1. Simplex
- flows in **one direction only**
- one device is always the transmitter and the other is always the receiver.
- **Example:** Radio broadcasting, television broadcasting.

### 2. Duplex
Communication flows in **both directions** between two devices. It is further divided into:

- **Half-Duplex:** Both devices can send and receive, but **not at the same time** (they take turns).
  - *Example:* Walkie-talkies, two-way radios.
- **Full-Duplex:** Both devices can send and receive **simultaneously**.
  - *Example:* Telephone calls, mobile phone conversations.

---

## Quick Recap Table

| Term           | Key Idea                                                   |
| -------------- | ---------------------------------------------------------- |
| Marconi        | Pioneer of practical wireless telegraphy/radio             |
| Fleming        | Invented the vacuum tube diode (signal detector/rectifier) |
| Transmitter    | Converts message into a transmittable signal               |
| Modulation     | Varies a carrier signal to carry information               |
| Encoder        | Converts message into transmittable code                   |
| Channel        | Path/medium the signal travels through                     |
| Noise          | Unwanted interference degrading the signal                 |
| Distortion     | Change in signal shape due to imperfect medium             |
| Attenuation    | Loss of signal strength over distance                      |
| Decoder        | Converts received code back to original form               |
| Receiver       | Recovers the original message from the signal              |
| Analog Signal  | Continuous, smoothly varying                               |
| Digital Signal | Discrete, binary values                                    |
| Simplex        | One-way communication                                      |
| Duplex         | Two-way communication (half or full)                       |
