# Mechanical Impedance:
| Name           | Impedance |
| :------------- | --------- |
| Spring         | $K$       |
| Viscous Damper | $f_vs$    |
| Mass           | $Ms^2$    |

# Control Systems
| symbol       | formula                                                  |
| :----------- | -------------------------------------------------------- |
| $$\omega_n$$ | $$\sqrt{c}$$                                             |
| $$\zeta$$    | $$\frac{b}{2\omega_n}$$                                  |
| $$\omega_d$$ | $$\omega_n\sqrt{1-\zeta^2}$$                             |
| $$T_p$$      | $$\frac{\pi}{\omega_d}$$                                 |
| $$\% OS$$    | $$100e^{-{\frac{\zeta\pi}{\sqrt{1-\zeta^2}}}}$$          |
| $$G(s)$$     | $$\frac{\omega^2_n}{s^2 + 2\zeta \omega_ns+\omega^2_n}$$ |
