
---

## tags: [reviewer, cpe12, data-communications, midterm] aliases: [CPE12 Reviewer, Data Comm Reviewer]

# CPE12 – Data and Digital Communications Reviewer

> [!info] Coverage Lecture 01 (Information Theory, Standards, OSI), `lesson-4` class notes, Lecture 02 (Transmission Media)

**Jump to:**
[[#1. Information Theory]] 
[[#2. Standards Organizations]]
[[#3. Data Transmission and Circuits]]
[[#4. OSI Model]]
[[#5. Transmission Media]]
[[#Formula Sheet]]
[[#Practice Questions]]

---

## 1. Information Theory

- **Information Theory**
    - Highly theoretical study of the _efficient use of bandwidth_ to propagate information through electronic communications systems
    - Used to determine the **information capacity** of a data communications system
    - Scientific study of information and of communication systems designed to handle it
- **Information Capacity**
    - Measure of how much information can be propagated through a system
    - Function of **bandwidth** and **transmission time**
    - = number of independent symbols carried through a system in a given unit of time
- **Binary digit (bit)**
    - Most basic digital symbol used to represent information
- **Bit rate**
    - Number of bits transmitted during **one second**
    - Unit: **bps** (bits per second)

### Hartley's Law (Information Capacity Theorem)

- Ralph Hartley
- Total information that can be transmitted is **proportional to the frequency range (bandwidth) and the time** of transmission
    - $I \propto B \times T$
        - $I$ = information capacity (bps)
        - $B$ = bandwidth (Hz)
        - $T$ = transmission time (s)
    - Class example: **Flashlight**
- **Hartley Law (channel capacity form)**
    - $C = 2B\log_{2}N$ (bps)
        - $C$ = channel capacity
        - $B$ = channel bandwidth
        - $N$ = number of coding (signal) levels

### Shannon–Hartley Theorem

- Channel **bandwidth (B)** and **signal-to-noise ratio (SNR)** are _exchangeable_: you can trade one for the other for a prescribed system performance
- Ideal relation:
    - $C = B\ \log_{2}(1 + SNR)$ bps
    - $C = 3.32\ B\ \log_{10}(1+SNR)$ bps
        - $C$ = information capacity, $B$ = bandwidth, $SNR$ = signal-to-noise ratio

> [!warning] Exam trap In the Shannon formula, **SNR must be a plain ratio, not dB.** Convert first: $SNR = 10^{,dB/10}$. Example: 30 dB → $10^{3} = 1000$.

### Signal-to-Noise Ratio (in dB)

- Formula depends on what is given (power, voltage, or current):
    - $\frac{S}{N},dB = 10\log\left(\frac{P_s}{P_n}\right)$ → **power**
    - $\frac{S}{N},dB = 20\log\left(\frac{V_s}{V_n}\right)$ → **voltage**
    - $\frac{S}{N},dB = 20\log\left(\frac{I_s}{I_n}\right)$ → **current**

### Noise Factor (F) and Noise Figure (NF)

- **Noise Factor, F** = ratio of S/N at the **input** to S/N at the **output**
    - $F = \dfrac{(S/N)_i}{(S/N)_o}$
- **Noise Figure** (F in dB)
    - $F_{dB} = NF = 10\log_{10}F$
- Ideal noiseless network
    - $(S/N)_i = (S/N)_o$ → $F = 1$, $F_{dB} = 0\ dB$
- Network that contributes noise
    - $(S/N)_i > (S/N)_o$ → $F > 1$

### Worked examples (from lecture, solved)

> [!example]- Example 1 — Telephone line: SNR = 30 dB, B = 3 kHz. Find information capacity.
> 
> 1. Convert dB → ratio: $SNR = 10^{30/10} = 1000$
> 2. $C = 3.32,B\log_{10}(1+SNR) = 3.32(3000)\log_{10}(1001)$
> 3. $C = 3.32(3000)(3.0004) \approx 29{,}884$ bps
> 4. Check with $\log_2$: $3000\log_2(1001) \approx 29{,}901$ bps
> 
> **Answer: ≈ 29.9 kbps** (small difference is just rounding of 3.32)

> [!example]- Example 2 — Amplifier with S/N = 10 (input) and 5 (output). Find noise figure.
> 
> 1. $F = \frac{10}{5} = 2$
> 2. $NF = 10\log_{10}(2) = 3.01\ dB$
> 
> **Answer: NF ≈ 3 dB** (F > 1, so the amplifier adds noise)

---

### Definitions

- **Data** — information (numbers, text, images, sounds) in a form suitable for storage in or processing by a computer
- **Data Communication** — process of transferring digital information between **two or more points**
- **Compression** — encoding data so it requires **less disk space** for storage and transmission
- **Encryption** — converting data/messages into something incomprehensible using a **key**; only an authorized recipient with the matching key can reconvert it

---

## 2. Standards Organizations

- **ITU** (International Telecommunications Union)
    - **United Nations** specialized agency for information and communication technologies (ICTs)
    - Allocates global **radio spectrum** and **satellite orbits**, develops technical standards so networks interconnect, improves ICT access for underserved communities
    - **3 main sectors**
        1. **ITU-R** – Radio Communications Sector
        2. **ITU-T** – Telecommunications Standardization Sector
        3. **ITU-D** – Development Sector
    - **ITU-T**
        - Government authorities and representatives from many countries
        - Standards organization for the UN; recommends rules/standards for **telephone and data communications**
        - Formerly **CCITT**
- **ANSI** (American National Standards Institute)
    - Founded **1918**
    - Coordinates and harmonizes **private-sector** standards development
    - **U.S. representative to ISO**
    - Class example: _shoe company_
- **IEEE** (Institute of Electrical and Electronics Engineers)
    - Worldwide professional association
    - Responsible for **LAN standards** (incl. **FDDI**)
    - Deals with SONET only peripherally
    - **SONET** – Synchronous Optical Networking (WAN / optical)
    - **FDDI** – Fiber Distributed Data Interface
- **ISO** (International Standards Organization)
    - Made up of the **national standards organizations** (e.g., ANSI) of various nations
    - "Global umbrella" body; heavily influences ITU-T standards
    - Best known for the **OSI model**
- **EIA** (Electronic Industries Association)
    - Founded **1924** as the _Radio Manufacturers Association_
    - Develops the **RS (Recommended Standard)** series for data and telecom
    - "Universal plug and wire design"
    - **RS-232** → 1960s, old "legacy" serial cable; physical layer example (RS-232C)
- **TIA** (Telecommunications Industries Association)
    - Leading trade association in the communications / IT industry
    - Market development, trade promotion, trade shows, advocacy, **standards development**

**Hierarchy diagram (from lecture):**

```
                ISO
     ┌───────────┼───────────┐
   ITU-T       IEEE        ANSI
                          ┌──┴──┐
                         EIA   TIA
```

> [!tip] Quick memory aid **I**SO sits on top → **I**TU-T, **I**EEE, **A**NSI under it → **E**IA and **T**IA under ANSI.

---

## 3. Data Transmission and Circuits

### Serial vs Parallel

||Serial|Parallel|
|:--|:--|:--|
|How bits move|One after another|**All at once**|
|Also called|**Serial-by-bit**|**Parallel-by-bit** or **serial-by-character**|
|Lines needed|**1** line/wire|**8** lines for 8 bits|
|Hardware|Parallel/serial converter at sender; serial/parallel converter at receiver|Separate line per bit|

### Data Communications Circuit

- **DTE – Data Terminal Equipment**
    - Data equivalent of **CPE** (Customer Premise Equipment) in the voice world
    - The computer transmit/receive equipment, including **dumb terminals** (no embedded programmed logic)
- **DCE – Data Communications Equipment**
    - Interfaces the DTE to the network
    - Resolves incompatibilities between domains: digital vs analog, voltage level, transmission speed, bit density
    - Examples: modem

> [!tip] DTE → DCE → Network DTE = the _user's device_. DCE = the _adapter/translator_ between the user's device and the network.

### Transmission Methods

- **Asynchronous** (character-framed)
    - **Start-stop** method
    - Each character (letter, number, control) is preceded by a **start bit** and followed by a **stop bit**
    - Stop bit tells the receiver that the set of information has ended
    - **Gaps** between data units
    - Used for slower / low-volume character data
- **Synchronous** (message-framed)
    - For **high-speed** applications
    - Large set of data is **framed/blocked**
    - One or more **synchronization bits/patterns** synchronize the receiver to the transmission rate
    - No start/stop bits per character

||Asynchronous|Synchronous|
|:--|:--|:--|
|Framing|Character-framed|Message-framed|
|Per-unit overhead|Start + stop bit per character|Sync bits per block/frame|
|Speed|Lower|**High-speed**|

---

## 4. OSI Model

- **OSI – Open Systems Interconnection**
    - Set of standards for communicating among computers
    - Primary purpose: **structural guideline** for exchanging information between computers, workstations, and networks
    - Class example: **sending a letter overseas**
    - Endorsed by **both ISO and ITU-T** (essentially identical standards)
    - **1983** → ISO and ITU-T (CCITT) adopted the **7-layer** reference model
    - Separates network responsibilities into 7 distinct layers to facilitate intercommunication of data processing equipment

> [!tip] Mnemonic (top → bottom) **A**ll **P**eople **S**eem **T**o **N**eed **D**ata **P**rocessing → Application, Presentation, Session, Transport, Network, Data link, Physical (bottom → top: **P**lease **D**o **N**ot **T**hrow **S**ausage **P**izza **A**way)

|Layer|Description|Protocol / Examples|Unit (PDU)|
|:--|:--|:--|:--|
|**7 – Application**|- Services to the **user**<br>- File transfer, graphics/doc interpretation, document processing<br>- _Network process to application_|HTTP, HTTPS, FTP, DNS, SMTP, POP, Telnet, P2P, X.400 e-mail|Data|
|**6 – Presentation**|- How you **present/format** data<br>- **Translation, compression, encryption**<br>- Isolates app data format from lower layers; syntax conversion|JPEG, MP3/MP4, AVI, HTML, DOC, Sockets|Data|
|**5 – Session**|- **Establishes, maintains, terminates** connections/data streams<br>- **Dialog control and synchronization**<br>- First of the 3 upper layers|SIP (Session Initiation Protocol), RTP, RPC / Named pipes, TCP session setup|Data|
|**4 – Transport**|- Uses **port numbers**<br>- **End-to-end** delivery (process to process)<br>- Reliable and unreliable transport|**TCP** (reliable), **UDP** (unreliable), SCTP, SSL, TLS|**Segments**|
|**3 – Network** (WAN)|- Anything with **IP**<br>- Logical addressing, **routing**, switching, packet sequencing, flow control<br>- Source host → destination host|IP, ARP, IPsec, ICMP, IGMP, OSPF, routing protocols|**Packets**|
|**2 – Data Link** (LAN)|- Understands **MAC address**<br>- Error detection (**CRC**), priority<br>- Controls how a device **gains access to the medium**<br>- Hop-to-hop delivery|Ethernet, PPP, HDLC, 802.11, Frame Relay, ATM, Token Ring; devices: **switch, access point**|**Frames**|
|**1 – Physical**|- **Mechanical, electrical, functional, procedural** steps<br>- Moves **individual raw bits** hop to hop<br>- Host components, transmission media|RS-232 / RS-232C, V.24, V.34, RJ45, 100BASE-TX, SDH, DSL|**Bits**|

> [!important] Must-know pairings Physical = **bits** · Data link = **frames** · Network = **packets** · Transport = **segments** Physical example **RS-232C** · Data link example **HDLC** · Network example **IP** · Transport example **TCP**

---

## 5. Transmission Media

### Physical Layer recap

- Layer 1, **bottom-most** layer of OSI
- **Sole job:** transmit and receive **raw, unformatted bits (1s and 0s)** over a physical medium
- Media: copper cable, fiber optics, wireless signals
- Examples: **UTP Cat6, coaxial cable, single-mode / multi-mode fiber, Wi-Fi radio frequencies**

### Transmission Media – Classification

- **Transmission media** = physical path that carries information (data signals) from **transmitter → receiver**
    - **1. Guided (wired / bounded)**
        - Physical, solid conductors giving a **bounded path** for electromagnetic or **light** signals
        - Branches:
            - **A. Balanced** → Twisted pair
            - **B. Unbalanced** → Coaxial
            - **C. Optical** → Fiber optic cable
    - **2. Unguided (wireless / unbounded)**
        - EM signals through **air, vacuum, or water** without physical conductors
        - Example: **Free space**

```
                 Transmission Media
              ┌─────────┴──────────┐
       Guided (wired)        Unguided (wireless)
     ┌──────┼───────┐               │
 Balanced Unbalanced Fiber       Freespace
 (Twisted  (Coaxial)  Optic
  Pair)               Cable
```

> [!note] Fiber optic Only named in these slides (single-mode / multi-mode). No further detail is given in Lecture 02 yet.

---

### A. Balanced Media — Twisted Pair Cable

- Two **insulated copper wires twisted together** (looks like phone wire)
- Credited to **Alexander Graham Bell**
- **2 types:**

||**UTP** – Unshielded Twisted Pair|**STP** – Shielded Twisted Pair|
|:--|:--|:--|
|Construction|No shield|**Foil / braid shield** around the pairs|
|History|Commonly used in Ethernet, historically **"10BaseT"**|Historically linked to **Token Ring** (the "LAN Wars")|
|Today|Standard LANs (Cat5e / Cat6), telephone lines (Cat3 / RJ-11)|Used extensively in **modern high-speed Ethernet**|
|Requirement|–|**Needs proper grounding**|
|Where used|Homes, offices|**Industrial factories, data centers**, heavy machinery environments|

#### Cable vs Connector

- **Cable** = the long, flexible bundle of insulated copper wires running through walls/floor (e.g., Cat 3, Cat 5e, Cat 6 UTP/STP)
- **Connector (plug)** = device that connects the cable to equipment or another cable; the clear plastic head **crimped** on the end that snaps into a port/jack (e.g., **RJ11, RJ45**)

|Connector|Pins|Used with|
|:--|:--|:--|
|**RJ45**|**8 pins**|Ethernet / LAN|
|**RJ11**|**4 pins** (per lecture)|Telephone|

#### Twisted-pair categories

|Category|Max Speed|Max Bandwidth / Frequency|Distance Limit|Primary Use|
|:--|:--|:--|:--|:--|
|**Cat 3**|10 Mbps|16 MHz|100 m|Legacy landline telephones (RJ11)|
|**Cat 5e**|1 Gbps (1,000 Mbps)|100 MHz|100 m|Basic home & office Ethernet|
|**Cat 6**|1 Gbps (**10 Gbps up to 55 m**)|250 MHz|100 m|Standard commercial LAN cabling|
|**Cat 6a**|10 Gbps|500 MHz|100 m|Enterprise networks & servers|
|**Cat 8**|25G / 40G bps|2,000 MHz (2 GHz)|**30–36 m**|Data center switch-to-server runs|

> [!tip] Pattern Speed and frequency go **up** each category, but only **Cat 8** drops the distance limit (to ~30–36 m). Everything else is **100 m**.

#### Twisted-pair characteristic impedance

- Standard value: **100 Ω**
- Same for **all** Ethernet twisted-pair categories (Cat 3 through Cat 7)
- A **fixed physical property** designed into the cable to match network hardware
- **Formula (parallel / twisted pair):**

$$Z_0 = \frac{276}{\sqrt{\epsilon_r}}\ \log_{10}\left(\frac{2D}{d}\right)$$

- $D$ = spacing between the two conductors (center to center; labelled $S$ in the figure)
- $d$ = diameter of each conductor
- $\epsilon_r$ = relative dielectric constant of the insulation

---

### B. Unbalanced Media — Coaxial Cable

- **Invention:** Oliver Buckley, Lloyd Espenschied, Herman Affel
    - Problem they solved: _how to send high-frequency radio signals through a cable without the signal dying out or burning up the wire_
- **Construction:** **center signal conductor** surrounded by an **outer ground shield**
    - The outer shield acts as **both the return path and protective ground**
- Parts (from figure): **copper wire** (center) → **insulation** → **copper mesh** (shield) → **outside insulation**

#### Characteristic impedance

|Impedance|Cables|Used for|Mode|
|:--|:--|:--|:--|
|**75 Ω**|RG-6, RG-59, RG-11|Video, TV & broadband internet|**Broadband** (analog, FDM)|
|**50 Ω**|RG-58 (**Thinnet**), RG-8 (**Thicknet**)|Data networks & RF antennas|**Baseband** (digital)|

- **Broadband transmission (75 Ω)**
    
    - Analog, **frequency-multiplexed** — uses **FDM** (Frequency Division Multiplexing)
    - Splits the cable's high bandwidth into multiple channel frequencies **simultaneously**
    - Why one coax carries hundreds of TV channels, digital radio, and high-speed internet to a home modem
    - Applications: **CATV, satellite TV drops, cable broadband internet (DOCSIS)**, analog CCTV, RF antennas
- **Baseband transmission (50 Ω)**
    
    - **Digital**, a single **unmodulated** signal using the **entire bandwidth** of the cable
    - Example: early Ethernet **10Base2** (Thinnet) / **10Base5** (Thicknet)
- **Formula (coaxial):**
    

$$Z_0 = \frac{138}{\sqrt{\epsilon_r}}\ \log_{10}\left(\frac{D}{d}\right)$$

- $D$ = inside diameter of the outer conductor (shield)
- $d$ = diameter of the center conductor
- $\epsilon_r$ = relative dielectric constant

> [!warning] Don't mix up the constants Twisted pair → **276** with $\frac{2D}{d}$ · Coax → **138** with $\frac{D}{d}$

#### Coaxial connectors

|Connector|Connection mechanism|Uses|Impedance (slide)|
|:--|:--|:--|:--|
|**F-Type**|**Threaded (screw-on)**; tight, weather-resistant seal|CATV, satellite TV, cable modems (DOCSIS), RG-6 / RG-59; **least expensive**; TVs, VCRs, DVD players|75 Ω|
|**BNC** (Bayonet Neill–Concelman)|**Bayonet** (twist-and-lock, quarter-turn, slot-and-pin)|Analog CCTV, lab equipment (oscilloscopes), legacy 10Base2 (Thinnet), RF radios; used on 0.25-inch cables for test equipment|50 Ω|
|**RCA**|–|Primarily **audio** equipment|–|
|**N-Type**|–|**Best performing**; large cables at **higher frequencies**|–|

---

### Balanced vs Unbalanced (summary)

||Balanced (Twisted pair)|Unbalanced (Coax)|
|:--|:--|:--|
|Conductors|Two twisted insulated wires|Center conductor + outer shield|
|Return path|The second wire|The **outer shield** (also ground)|
|Impedance|**100 Ω**|**75 Ω** or **50 Ω**|
|Typical connector|RJ45 / RJ11|F-type, BNC, N-type, RCA|
|Typical use|LAN, telephone|TV, broadband, RF, legacy Ethernet|

---

# Formula Sheet

|Topic|Formula|
|:--|:--|
|Hartley (capacity ∝)|$I \propto B \times T$|
|Hartley (levels)|$C = 2B\log_{2}N$|
|Shannon–Hartley|$C = B\log_{2}(1+SNR) = 3.32,B\log_{10}(1+SNR)$|
|dB → ratio|$ratio = 10^{,dB/10}$|
|SNR (power)|$10\log\frac{P_s}{P_n}$|
|SNR (voltage / current)|$20\log\frac{V_s}{V_n}$, $20\log\frac{I_s}{I_n}$|
|Noise factor|$F = \frac{(S/N)_i}{(S/N)_o}$|
|Noise figure|$NF = 10\log_{10}F$ (ideal: F = 1, 0 dB)|
|Twisted-pair $Z_0$|$\frac{276}{\sqrt{\epsilon_r}}\log_{10}\frac{2D}{d}$ (standard 100 Ω)|
|Coax $Z_0$|$\frac{138}{\sqrt{\epsilon_r}}\log_{10}\frac{D}{d}$ (75 Ω or 50 Ω)|

---

# Practice Questions

> [!question]- 1. State Hartley's Law and define each variable. Information that can be transmitted is proportional to bandwidth and time: $I \propto B \times T$. $I$ = information capacity (bps), $B$ = bandwidth (Hz), $T$ = transmission time (s).

> [!question]- 2. What does the Shannon–Hartley theorem say? Channel bandwidth and SNR are exchangeable (you can trade one for the other for the same performance). Capacity: $C = B\log_{2}(1+SNR)$.

> [!question]- 3. A channel has B = 4 kHz and SNR = 20 dB. Find the capacity. $SNR = 10^{20/10} = 100$. $C = 4000\log_{2}(101) = 4000(6.658) \approx 26.6$ kbps.

> [!question]- 4. A channel with B = 3 kHz uses 4 coding levels. Find C using Hartley's Law. $C = 2B\log_{2}N = 2(3000)\log_{2}4 = 2(3000)(2) = 12{,}000$ bps = **12 kbps**.

> [!question]- 5. S/N is 20 at the input and 4 at the output of an amplifier. Find F and NF. $F = 20/4 = 5$. $NF = 10\log 5 \approx 7$ dB.

> [!question]- 6. What is the noise figure of an ideal noiseless network? $F = 1$, so $NF = 0$ dB.

> [!question]- 7. Which organization is the "global umbrella" of standards bodies, and what falls under ANSI? **ISO**. Under ISO: ITU-T, IEEE, ANSI. Under ANSI: **EIA** and **TIA**.

> [!question]- 8. Which body develops the RS series (e.g., RS-232), and when was it founded? **EIA**, founded **1924** as the Radio Manufacturers Association. (RS = Recommended Standard.)

> [!question]- 9. Differentiate serial and parallel transmission. Serial: bits sent one after another over **one line** (serial-by-bit). Parallel: bits sent **all at once** over multiple lines, e.g., 8 lines for 8 bits (parallel-by-bit / serial-by-character).

> [!question]- 10. Differentiate DTE and DCE. **DTE** = the user's computer / terminal (data equivalent of CPE). **DCE** = equipment that interfaces the DTE to the network and resolves incompatibilities (digital vs analog, voltage, speed, bit density).

> [!question]- 11. Differentiate asynchronous and synchronous transmission. Async: character-framed, **start bit + stop bit** per character, gaps between units. Sync: message-framed, large blocks with **sync bits/patterns**, used for **high-speed** applications.

> [!question]- 12. Name the OSI layers top to bottom, with the PDU for layers 1 to 4. Application, Presentation, Session, Transport (**segments**), Network (**packets**), Data Link (**frames**), Physical (**bits**).

> [!question]- 13. Which OSI layer handles port numbers, and which handles MAC addresses and CRC? Port numbers → **Transport (4)**. MAC address and CRC → **Data Link (2)**.

> [!question]- 14. Which layer handles translation, compression, and encryption? **Presentation (6)**.

> [!question]- 15. UTP vs STP: which needs grounding and where is it used? **STP** needs proper grounding. Used in industrial factories, data centers, and environments with heavy machinery.

> [!question]- 16. Which twisted-pair category runs 10 Gbps up to only 55 m? Which has only a 30–36 m limit? **Cat 6** (10 Gbps up to 55 m). **Cat 8** (30–36 m; 25G/40G; 2 GHz).

> [!question]- 17. Characteristic impedance of twisted pair, 75 Ω coax, and 50 Ω coax? Twisted pair: **100 Ω**. 75 Ω: RG-6, RG-59, RG-11 (TV / broadband, FDM). 50 Ω: RG-58 Thinnet, RG-8 Thicknet (data / RF, baseband).

> [!question]- 18. Differentiate broadband and baseband coaxial transmission. **Broadband (75 Ω):** analog, uses FDM to carry many channels at once (CATV, DOCSIS). **Baseband (50 Ω):** digital, one unmodulated signal using the whole bandwidth (10Base2 / 10Base5).

> [!question]- 19. Compare F-type and BNC connectors. **F-type:** threaded screw-on, 75 Ω, cable TV / satellite / DOCSIS, cheapest. **BNC:** bayonet twist-lock (quarter turn), 50 Ω, CCTV / oscilloscopes / 10Base2 / RF.

---

> [!success] Study plan suggestion
> 
> 1. Memorize the **OSI table** and the **cable / impedance tables** (sections 4 and 5)
> 2. Drill the **dB conversion + Shannon / noise-figure** problems (section 1)
> 3. Finish with the **Practice Questions**, covering the answers first