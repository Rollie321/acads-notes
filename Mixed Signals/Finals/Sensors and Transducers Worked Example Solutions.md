# Sensors and Transducers: Worked Example Solutions

Solutions to the three example problems in the BSCPE III Sensors and Transducers slides.

## Example 1: Copper-Constantan Thermocouple

**Given:**

- $c = 3.75 \times 10^{-2}\ \text{mV/°C}$
- $k = 4.5 \times 10^{-5}\ \text{mV/°C}^2$
- $T_1 = 100\ °\text{C}$
- $T_2 = 0\ °\text{C}$ (cold junction kept in ice)

**Formula:**

$$E = c,(T_1 - T_2) + k,(T_1^2 - T_2^2)$$

**Solution:**

$$E = (3.75 \times 10^{-2})(100 - 0) + (4.5 \times 10^{-5})(100^2 - 0^2)$$

$$E = 3.75 + 0.45$$

$$E = 4.20\ \text{mV}$$

**Answer:** $E = 4.20\ \text{mV}$

---

## Example 2: Platinum Resistance Thermometer

**Given:**

- $R_o = 150\ \Omega$ at $20\ °\text{C}$
- $\alpha_{20} = 0.00392\ /°\text{C}$
- Operating temperature $= 50\ °\text{C}$

**Formula:**

$$R = R_o,(1 + \alpha \Delta T)$$

**Solution:**

$$\Delta T = 50 - 20 = 30\ °\text{C}$$

$$R = 150\left[1 + (0.00392)(30)\right]$$

$$R = 150,(1.1176)$$

$$R = 167.64\ \Omega$$

**Answer:** $R = 167.64\ \Omega$

---

## Example 3: Venturi Flowmeter

**Given:**

- $\Delta P = 16\ \text{kPa}$
- $k = 0.75$

**Formula:**

$$Q = k\sqrt{\Delta P}$$

**Solution:**

$$Q = 0.75\sqrt{16}$$

$$Q = 0.75,(4)$$

$$Q = 3.0$$

**Answer:** $Q = 3.0$ (in units of $k\sqrt{\text{kPa}}$, since the slides don't give the units of $k$)

> [!note] Unit check If your instructor expects $\Delta P$ in SI base units, use $\Delta P = 16,000\ \text{Pa}$: $$Q = 0.75\sqrt{16,000} \approx 94.87$$ Use whichever convention your class follows. The slides leave the unit of $k$ unspecified, so $Q = 3.0$ with $\Delta P$ in kPa is the most direct reading.

---

## Summary

|Problem|Quantity|Answer|
|---|---|---|
|1. Thermocouple|EMF, $E$|$4.20\ \text{mV}$|
|2. Platinum RTD|Resistance, $R$|$167.64\ \Omega$|
|3. Venturi meter|Flow rate, $Q$|$3.0$ (using $\Delta P$ in kPa)|