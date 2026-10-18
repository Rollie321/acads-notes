ADC
DAC

Digital System
1. For a 5-bit DAC digital input of 10100, an output current of 10mA is produced. What will Io be for a digital input of 11101?

2.  What is the largest value of output voltage from an 8-bit DAC that produces 1.0V for a digital input of 00110010?
	* Convert to decimal number:
	$$ 00110010 = 32+16+2 = \boxed{50_{10}}$$
	* Multiply to the maximum decimal value of 8 bit which is:
	$$ 11111111_2 = 128+64+32+16+8+4+2+1$$
	$$ 11111111_2 = \underline{255}$$
	* or:
	$$ 1111\ 1111_2 = 2^8-1=\underline {255} $$
		$$\frac{1.0 \mathrm{v}}{50}{(2^8-1)}$$
	$$ \frac{1.0 \mathrm{v}}{50}{255} = 5.1\mathrm v$$

3.  For a 6-bit DAC, a digital input of 1011002 produces an output current of 22mA. What will the output current Io be for a digital input of 1101112 ?
	* Find the decimal value:
	$$ 101100_2 = 32+8+4 = 44_{10}$$
	$$ 110111_2 = 32+16+0+4+2+1 = 55_{10}$$
	* Use the given ratio
	$$55\frac{22\ \mathrm{mA}}{44}=27.5\ \mathrm{mA}$$
 
 4. A 10-bit DAC has $V_{ref} = 5\mathrm v$.
	1. What is the output voltage for the digital input $1000000000_2$ (mid-scale)?
		* Convert to decimal:
		$$ 1000000000_2 = 512_{10}$$
		* Apply the formula:
		$$ \frac{V_{ref}}{2^n}(\text{Digital input})$$
		$$ \frac{5\mathrm v}{2^{10}}512 = 2.5\mathrm v$$
	2. Is it exactly half of full-scale?
		* Obtain the full scale:
		$$ \mathrm {\text{full scale}}=\frac{5\mathrm v}{2^{10}}(2^{10}-1)= 4.995\mathrm v $$
		* Take the half of full scale and compare to previous answer:
		$$ \mathrm{\text{half of full scale}}=\frac{4.995\mathrm v}{2}=2.498\mathrm v$$
		* Check the mid scale:
		$$ 2.5 \neq2.498$$
		* Answer: No, half of **full-scale** is not equal to the **mid-scale**

 5.  Assume the following values for the digital ramp ADC of : clock frequency = 1 MHz; VT = 0.1 mV; DAC has full scale output = 10.23 V and a 10-bit input.
	* Determine the following values:
		* a) The digital equivalent obtained for $V_A=3.728 V$
		* b) The conversion time
		* c) The resolution of this converter
	* Answers:
		* a)
			* Find the full scale:  $2^{10} - 1 = 1023$
			* Use the rate to convert:
				* $(3.728\mathrm v)(\frac{1023}{10.23\mathrm v}) = 373\ \mathrm{steps}$
				* $373_{10} = \underline {0101110101_2}$
		* b)
			* $\text{Bit rate} = \frac{bits}{time}$
			* $time = \frac{bits}{\text{bit rate}}$
			* $time = \frac{373}{1\ MHz} = 373\times 10^{-6}s =  373 \micro s$

		 * c)
			* Resolution: $\frac{1}{1023}\times 100 \% = 0.09\%$

6. A 5-bit DAC produces an output of 6.4 V when the input is 10100.
	* **(a)** What is the output for input 11111?
	*  **(b)** What digital input will produce 3.2 V?
	* Answers
		* (a).
			* $10100_2=20_{10}$
			* $11111_2 = 31_{10}$
			* $\frac{6.4\mathrm v}{20}(31) = \boxed{9.92\mathrm v}$
		* (b).
			* $3.2\mathrm v \frac{20}{6.4\mathrm v} = 10$
			* $10_{10} = 01010_2$

7. A 6-bit DAC has a maximum output of 20 mA.
	* (a) What output current will be produced for input 100000?
	* (b) What digital input will give 5 mA?
	* Answers:
		* (a).
			* $2^6-1=63$
			* $100000_2=32_{10}$
			* $32\frac{20\mathrm{mA}}{63} = \boxed{10.16\mathrm{mA}}$
		* (b)
			* $5\mathrm{mA} \frac{63}{20\mathrm{mA}}=16$
			 $16_{10} = 010000_2$