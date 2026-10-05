

* Ralph Hartley
* Info to Cap Theorem
	* $I\  \alpha\  B\times T$
* Hartley's Laws:
	* (define)
* Shannon Hartley Theorem
	* (define)
	* for BpS :
		* $C = B\ \log_{2}(1 + SNR)$ 
		* $C = 3.32\ B\  \log(1+SNR)$
	* In terms of Db (decibels): it could vary depending on given (power, voltage, current)
		* $\frac{S}{N}dB = 10 log(\frac{P_s}{P_n})$
		* $\frac{S}{N}dB = 20 log(\frac{V_s}{V_n})$
		* $\frac{S}{N}dB = 20 log(\frac{I_s}{I_n})$
		* $\frac{S_i}{N_i} = N = \frac{S_o}{N_o}$
		* $F=\frac{In}{Out}$
		* $F_{dB}=10 \log F$

- - - 

- IEEE
	- (define)
	- For LAN
* SONET
	* (define)
	* For WAN
* ISO
	* (define)
	- Global umbrella network
- EIA
	- universal plug and wire design at **1924**
	- RS - **R**ecommended **S**tandard
		- RS232:
			- 1960's
			- old cable
			- "legacy cable"
- TIA
	- market dev in **modern**
* Serial transmission
	* (define)
* Parallel transmission
	* (define)

- - -
- Data Comm Circuit
	- DTE
		- (define)
		- data equivalent of CPE 
	- DCE
		- (define)
		- Data communications Eq

- - - 
- Transmission Method
	- Asynchronous 
		- (define)
		- With start and stop bit
		- OSI
			- Open system interconnection

- - - 

| Layer           | Description                                                                                       | Protocol                                                     |
| :-------------- | ------------------------------------------------------------------------------------------------- | ------------------------------------------------------------ |
| Application     | - user                                                                                            | http, https, ftp                                             |
| Presentation    | - how you present/format data                                                                     | JPEG, MP4, JPG                                               |
| Session         | - establishes, maintain, terminates, starts, connection                                           | - SIP (session initiation protocol)                          |
| Transport       | - use port number<br>- end-to-end delivery<br>- responsible for reliable and unreliable transport | - TCP (reliable)<br>- UDB (unreliable)                       |
| Network (WAN)   | - Anything with IP<br>- routing<br>- hacking<br>- IP address / connectivity                       | - IP<br>- routing protocol                                   |
| Data link (LAN) | - Understand mock address<br>- Error detection<br>- CRC<br>- priority                             | - Ethernet<br>- PPP<br>- HDLC<br>- uses switch, access point |
| Physical        | - elec, mech, procedural, functional steps.<br>- host component, transmission media               | - V.24                                                       |
