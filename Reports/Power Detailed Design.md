# Power Subsystem Detailed Design  
*Smart Golf Visor*

---

## Function of the Subsystem

The Power Subsystem provides all regulated electrical energy required for the visor-mounted electronics of the Smart Golf Visor. Its primary role is to safely receive, store, condition, and distribute energy from a single-cell 3.7-V lithium-polymer battery to the ESP32-S3 microcontroller, the SSD1309 OLED display, the ICM-20948 IMU, the LED battery indicator, and auxiliary logic components that support the remaining subsystems.  

The subsystem must maintain stable voltage regulation during high-current wireless events, deliver low-ripple power for sensor stability, and protect against electrical faults that could compromise user safety. Because the visor is a wearable device, the subsystem must also minimize thermal rise and ensure that external surfaces remain within comfortable touch temperature limits.  

Furthermore, the Power Subsystem supplies mechanical dimensions, power connector locations, wire routing paths, and mounting constraints to the Visor Design Subsystem, ensuring that battery cells, switches, and PCBs can be securely integrated into the visor housing.

---

## Specifications and Constraints


## Specifications  
Listed below are the specification **shall** statements for the Power Subsystem:

- The power subsystem **shall** use a single-cell (1S) Li-Po battery rated at **3.7 V nominal** and **3 000–5 000 mAh**, providing **≥ 6 hours** of continuous operation.  
- The power subsystem **shall** generate a regulated output of **3.3 V ± 5%**.  
- The subsystem **shall** comply with **NESC** and **NEC low-voltage requirements** for grounding, overcurrent protection, and conductor insulation.  
- The subsystem **shall** incorporate a **Battery Management System (BMS)** providing Over-Voltage (OV), Under-Voltage (UV), Over-Current Protection (OCP), and Over-Temperature Protection (OTP) during both charge and discharge.  
- The subsystem **shall** include an inline fuse rated at **≥ 1.5×** the maximum steady-state current to protect against overcurrent conditions.  
- The subsystem **shall** inhibit **charging below 0 °C or above 45 °C** and shall disable discharge under **undervoltage** conditions.  
- User-touchable surfaces **shall not exceed 41 °C** after 60 minutes of operation at **25 °C ambient**.  
- The subsystem **shall** meet **NEC Article 480** and **NESC Section 12** requirements for safe battery installation, wiring practices, and short-circuit protection.  
- The subsystem **shall** accept **USB-C 5 V input** and shall perform **CC/CV Li-Po charging** per the charger IC manufacturer’s specifications.  
- In **CC mode**, current shall remain constant as pack voltage rises; in **CV mode**, the charger shall regulate voltage while current tapers to a safe cutoff near **4.2 V**.  
- The subsystem **shall** limit charge current to the manufacturer-approved **C-rate (0.5–1C)**, where **1C = 6 000 mA** for a 6 000 mAh cell.  
- The subsystem **shall** achieve **≥ 90% efficiency** for the 3.3 V and 5 V regulators (if both rails are used).  
- The subsystem **shall** include margin calculations for converter losses, battery derating, and thermal headroom in accordance with NESC best practices.

---

## Constraints  
Listed below are the constraints the subsystem must abide by, including rationales describing how each relates to engineering requirements and regulatory compliance.



<Center>
  <strong>Table I</strong><br>
  Unified Constraints, Standards/Guidance, and Design Impact Table
</Center>

| **Constraint** | **Standard** | **Relevance to Design** | **Design Rationale** |
|---|---|---|---|
| **Battery Safety** | IEC 62133-2 (Rechargeable Li-ion cells)[1]; NEC Article 480 (National Electrical Code); NESC Part 1, Sec. 12 (National Electrical Safety Code) | Ensures safe charging, discharging, wiring, and integration of Li-Po cells into a wearable electronic device | Requires 1S Li-Po with integrated BMS (OV/UV/OCP/OTP), inline fuse on battery rail, charge-temperature limits, and conductor sizing per NEC; enclosure must manage thermal and fault conditions |
| **Wiring & Power Conductors** | NEC/NESC Low-Voltage Wiring Requirements [2] | Ensures wires can handle load current safely without overheating or causing electrical hazards | Drives selection of AWG conductor size, insulation rating, and inline fault-isolating fuse placement |
| **Converter Efficiency** | IEC 62301 [3] (Low-power efficiency guidance) | Minimizes wasted power, reduces system temperature, and extends battery runtime | Requires ≥85% efficiency target in DC-DC conversion stages to reduce thermal load and improve battery life |
| **Thermal Hot Spots (Skin Contact Area)** | ISO/IEC general thermal-exposure guidelines for consumer electronics | Device is worn on the user’s head; must avoid discomfort or thermal injury | Thermal-budget analysis; pack isolation; surface temperature limited to ≤41 °C; ventilation/air-gap in enclosure |
| **Battery Fault or Misuse Risk** | IEC 62133-2 (thermal & electrical fault behavior)[1]; NEC/NESC wiring protections | Lithium battery faults may lead to venting or fire; must ensure user safety in all conditions | Use protected 1S Li-Po pack with BMS, NEC-compliant wiring, inline fuse, and a safety-rated enclosure with relief pathway |







## Compliance With Applicable Standards

### Battery Safety — IEC 62133-2 & NEC Article 480

IEC 62133-2 defines protection and safety requirements for rechargeable lithium cells, including constraints on acceptable charge voltage, discharge limits, fault-protection features, and thermal operating windows [1]. In this design, these requirements are satisfied through the ETA9740 charger/booster IC and the onboard Battery Management System (BMS), which incorporates hardware-level protections such as over-voltage regulation, under-voltage cut-off, over-current limiting, and controlled charging termination; the specific implementation of these protections is described in later sections. These safety mechanisms ensure that the Li-Po cell cannot exceed its maximum safe charge voltage and will not be discharged below a chemically harmful threshold.

NEC Article 480 introduces requirements for safe battery installation, conductor sizing, and short-circuit protection [2]. The subsystem satisfies these requirements by using an inline fuse on the regulated rail, applying wire gauges that support the peak system current, and ensuring all battery-carrying wiring is insulated and strain-relieved within the visor enclosure.

Together, these measures ensure safe charge and discharge behavior across all expected operating conditions.

---

### Wiring & Power Conductors — NEC/NESC

NEC/NESC guidelines require that conductors be selected based on expected current loads, insulation type, and environmental factors. The visor electronics draw up to 400–500 mA during ESP32-S3 peak wireless transmission. According to NEC 310.16, AWG 26 & 30 conductors have more than sufficient current-carrying capability for this application.  

To ensure mechanical robustness inside the wearable enclosure, flexible silicone-insulated wiring is used for the battery and regulator outputs. Proper insulation and strain relief prevent conductor damage due to repeated movement or flexing of the visor. The inline fuse on the regulated rail protects the wiring from overheating during abnormal conditions, and it will be implemented in an enclosed, insulated fuse holder (or fully heat-shrunk inline assembly) located inside the visor housing. This keeps all live conductors mechanically protected and shielded from sweat, moisture, and incidental contact, supporting compliance with NEC/NESC fault-protection and environmental-protection expectations.


---

### Efficiency — IEC 62301

IEC 62301 emphasizes minimizing standby losses, which are the small but continuous power drains that occur even when a device is not performing active work [3]. In battery-powered electronics, standby losses arise from quiescent current, background fuel-gauge operation, trickle-charge behavior, and any residual converter activity when the load is light. Managing these losses is essential because they directly reduce runtime and generate avoidable heat within the wearable device.

The ETA9740 minimizes standby losses through its low quiescent current of approximately 80 µA at VBAT = 3.6 V [4], combined with an efficient automatic mode-switching architecture. This low idle draw reduces the trickle drain on the battery while preserving nearly full capacity for active operation. The ETA9740 also exhibits stable switching behavior across its operating modes, and because it uses a synchronous architecture with controlled switching transitions, it does not introduce significant on/off switching noise or transient spikes that could interfere with the downstream 3.3-V regulator or sensitive IMU electronics. This ensures predictable regulation performance during transitions between light-load, standby, and active states.

The high conversion efficiency—reaching up to 96% in boost mode according to its datasheet—reduces heat generation inside the visor during normal operation. Minimizing self-heating is critical in a wearable device, because user-touchable surfaces must remain below 41 °C after 60 minutes of typical use to meet the subsystem’s defined thermal safety requirement. Lower standby losses therefore directly support both runtime and thermal margins, helping the design maintain safe temperatures when worn on the user’s head.

The reduced idle drain also improves alignment with the six-hour minimum operational requirement by ensuring that off-cycle and low-activity periods do not prematurely deplete the battery.

In addition to the charger, the TPS63031 buck-boost converter also supports IEC 62301 efficiency expectations by offering exceptionally low quiescent current—only **35 µA typical**, as shown in its datasheet (page 5) [5]. This is critical because the converter remains connected to the battery at all times, even when the rest of the system is idle. Its automatic power-save mode further reduces switching activity under light-load conditions, preventing unnecessary conversion losses while maintaining a stable 3.3-V rail. When active, the TPS63031 operates with up to **94% efficiency in buck mode and 90%+ in boost mode**, minimizing heat generation within the visor enclosure and supplementing the thermal benefits provided by the ETA9740. Together, the low standby drain and high operating efficiency of the TPS63031 contribute directly to extended battery life, reduced waste heat, and compliance with the subsystem’s runtime and thermal-safety requirements.

---

## Overview of Proposed Solution



The Power Subsystem is centered on the ETA9740 charger/booster, which manages both the charging of the Li-Po battery through a USB-C port and the conversion of battery voltage to downstream rails when the visor is in use. When connected to USB-C, the device charges the battery in a constant-current/constant-voltage profile, ensuring safe and efficient energy replenishment [6]. When disconnected, it switches to discharge mode and provides a stable voltage for subsequent regulation.

A dedicated 3.3-V regulator supplies the system logic, providing clean power to the ESP32-S3, the OLED driver, and the IMU. This regulation is handled by the TPS63031 buck-boost converter, which maintains a fixed 3.3-V output even as the battery voltage varies from 4.2 V down to its 2.75 V cutoff. This capability ensures uninterrupted operation during heavy load events and prevents brownout conditions as the battery discharges.

Instead of previously using an ideal-diode IC, reverse-polarity and reverse-current protection is implemented using an **LTC4412 ideal-diode controller** driving an external **AON7403 P-channel MOSFET**. The LTC4412 regulates based on small forward drop thresholds (≈ ±20 mV), controlling the MOSFET gate to emulate an ideal diode while blocking reverse current when the output rises above the input [10]. The AON7403’s low RDS(ON) supports a very small conduction drop at the visor’s load currents [11].

To preserve flexibility for EMI/noise tuning, the PCB includes 0 Ω “option” resistor footprints placed immediately after the ETA9740 5-V output node and after the TPS63031 3.3-V output node. These allow quick substitution of a ferrite bead later if switching-noise coupling is observed during testing.

This architecture minimizes power loss, lowers thermal output, and ensures consistent operation under high-current conditions such as wireless transmissions and rapid OLED refresh cycles.

---

## Interface with Other Subsystems



The Power Subsystem provides a regulated 3.3-V power rail to the Communications Subsystem, supplying the ESP32-S3 microcontroller through its dedicated 3.3-V input pin and ground reference. It also powers the Heads-Up Display Subsystem, including both the IMU and the OLED display. Because these components draw dynamic and burst-type current loads—particularly the ESP32-S3 during wireless activity—the regulator is selected and configured to maintain output stability with sufficient transient response to support these fluctuations. The TPS63031 buck-boost converter further assists in this role by holding the rail at 3.3 V even as battery voltage changes, ensuring consistent operation throughout the discharge cycle.

The subsystem also integrates a user-controlled power input through the RR551D1123 [7] rocker switch and provides battery-level feedback through dedicated LED indicator's on the PCB. Mechanical integration information—including battery footprint, PCB outline, and switch dimensions—is provided to the Visor Design Subsystem to ensure proper placement and secure mounting inside the visor housing.

The regulated power path is routed from the 3.3-V header output pin to the inline fuse holder (containing the 700 mA protection fuse [8]), then to the main power switch, and finally to the input power nodes of both the Communications Subsystem and the Heads-Up Display Subsystem. This ensures that all downstream electronics receive protected, switched 3.3-V power from a single controlled distribution point.

---

## Buildable Schematic 

    
The overall schematic, as shown in Figures 1–7, fully satisfies the established shall statements and functional requirements for the Power Subsystem at this stage of the design. The charging-circuit schematic provides compliant Li-Po charging, protection, and safe power-path management, while the buck-boost converter schematic delivers the regulated 3.3-V rail required by the subsystem loads. The reverse-polarity protection circuit ensures safe battery connection, and the LED battery-level indicator schematic provides basic state-of-charge visibility for user feedback. Additionally, the output header-pin interface supports proper connectivity to downstream subsystems, and the optional 5-V regulation stage is included to preserve flexibility for future system needs. While the present schematic meets all electrical, safety, and interface requirements and is fully capable of supporting prototyping and integration, it remains subject to revision as the project progresses; optional circuits may be removed or simplified if later testing or design updates indicate they are unnecessary, redundant, or non-optimal for the final implementation.   
<Center>
    
![Screenshot 2026-02-03 153527](https://hackmd.io/_uploads/BJHfV7bPbg.png)

    

Figure 1. Overall system schematic
</Center>

Figure 1 presents the complete power subsystem schematic for the device, integrating USB-C input handling, battery charging, protection, voltage regulation, and output power distribution into a unified design. The circuit accepts 5-V power from a standard USB-C source and conditions it through protection elements—including ESD suppression and transient clamping—before routing it into the ETA9740 switching charger, which manages safe Li-Po charging and provides real-time charge-level indication.

Reverse-polarity and reverse-current protection of the battery path is implemented using an LTC4412 ideal-diode controller driving an external AON7403 P-channel MOSFET, providing low-loss ideal-diode behavior while preventing backfeed or accidental reverse connection. A TPS63031 buck-boost converter generates a stable 3.3-V rail for system electronics across the full battery discharge range.

Unlike the previous revision, the design no longer includes a switchable 5-V selection stage. Instead, regulated outputs—including USB VBUS (raw 5 V), ETA9740 5 V, TPS63031 3.3 V, battery voltage, and ground references—are routed directly to a centralized output header for integration with the broader system. Each functional block of the schematic is described in more detail in the following sections.

    
<Center>
    
![Screenshot 2026-02-04 135729](https://hackmd.io/_uploads/SJx6NmZD-e.png)



Figure 2. Charging-circuit schematic
    </Center>

The charging schematic shown in Figure 2 implements the complete Li-ion charging front end for the system using the ETA9740 single-inductor switching charger. USB power is accepted through the USB-C connector, where 5.1 kΩ pull-down resistors on the CC1 and CC2 pins identify the device as a power sink and enable a standard 5-V supply from any compliant USB-C source. The incoming VBUS rail is routed through a 3-A fuse for over-current protection, followed by an ESD (Electrostatic Discharge) suppression diode, which clamps high-voltage ESD events that commonly occur during cable insertion and protects sensitive downstream components [9]. This diode works together with the bank of high-frequency and bulk capacitors to filter transient spikes and stabilize the input rail. The conditioned 5-V supply then feeds the ETA9740, which uses a 1-µH inductor and the surrounding bypass capacitors to operate as a high-efficiency buck converter, allowing the device to safely perform pre-charge, fast-charge, and constant-voltage regulation of a 1-cell Li-Po battery [4]. The charge current is set by the 56-kΩ ISET resistor, corresponding to a 3-A fast-charge mode per the datasheet specifications. The BAT pin distributes controlled charging current to the battery while the integrated fuel-gauge hardware drives the LED indicators to display approximate charge level. Reverse-polarity and reverse-current protection is implemented using an LTC4412 ideal-diode controller driving an external P-channel MOSFET, replacing the previous dedicated ideal-diode IC solution. Collectively, these elements form a protected, standards-compliant, high-efficiency charging subsystem suitable for powering downstream 3.3-V regulators and system loads.

    

 <Center>

![Screenshot 2026-02-04 135913](https://hackmd.io/_uploads/Hy7BrXZvZg.png)



    
Figure 3. Buck-boost converter schematic
</Center>

The buck-boost schematic shown in Figure 3 implements the TPS63031 synchronous regulator (U5), which provides a regulated 3.3-V output even as the Li-Po battery voltage rises above or falls below the 3.3-V rail. The battery input (BAT+Prot) is filtered through C12 (10 µF) and C13 (100 nF), which supply instantaneous current during switching transitions and reduce high-frequency noise at the VIN and VINA pins. The 1.5-µH inductor (L2) serves as the main energy-storage element that enables the device to automatically transition between buck and boost operation depending on battery voltage, as described in the TPS63031 operating principles [5].

On the output side, C14 (10 µF) and C15 (10 µF) provide bulk decoupling for the 3.3-V rail, while C16 (100 nF) serves as the high-frequency bypass capacitor located near the output test point (TP2). Together, these components stabilize the 3.3-V rail, reduce ripple, and maintain regulation during sudden load changes. A 0-Ω resistor (R11) is included in series with the 3.3-V output as a configurable placeholder, allowing substitution of a ferrite bead in future revisions if additional filtering of switching noise is required.

Together, these components allow the converter to deliver a stable 3.3-V supply for downstream digital electronics while maintaining high efficiency across the battery’s full discharge range.

    
<Center>
    
![Screenshot 2026-02-02 134052](https://hackmd.io/_uploads/ByPOr7-w-e.png)


    
Figure 4. Reverse-polarity protection for battery connection
 </Center>  
    
The reverse-polarity protection schematic in Figure 4 uses an LTC4412 ideal-diode controller (U2) driving an external AON7403 P-channel MOSFET (Q3) in series with the battery connector J1 (JST 2 mm) and the system power path. The positive terminal of the battery (BAT+) from the 2 mm JST connector feeds the source of Q3, while the protected system voltage (BAT+Prot) is taken from the drain side of the MOSFET.

The LTC4412 monitors the voltage difference between its VIN and SENSE pins and drives the MOSFET gate accordingly. When the battery is connected with correct polarity and its voltage exceeds the downstream rail, the controller enhances the MOSFET, allowing current to flow with only a very small voltage drop determined by the MOSFET’s RDS(ON). If the battery is accidentally reversed, or if the downstream rail rises above the battery voltage, the LTC4412 rapidly turns the MOSFET off, blocking reverse current flow and preventing damage to the battery and downstream electronics.

With the CTL pin tied appropriately for continuous operation and the STAT pin left unused, this configuration functions as an efficient solid-state ideal diode. Compared to a traditional series diode, it significantly reduces forward voltage drop and power dissipation while still providing reliable reverse-polarity and reverse-current protection for the battery input.

    
<Center>
    
![Screenshot 2026-02-04 140146](https://hackmd.io/_uploads/ryF2Sm-v-l.png)



Figure 5. LED battery-level indicator schematic
</Center>    
    
This portion of the schematic implements the LED battery-level indicator driven by the ETA9740’s fuel-gauge outputs. Each LED pin from the charger (LED1, LED2, LED3) switches its corresponding LED pair on or off to display the battery’s approximate charge state. The LEDs are arranged so that different combinations illuminate as the battery voltage increases, providing a simple visual indication of 25%, 50%, 75%, and full charge levels. This circuit does not regulate current itself—the ETA9740 internally controls the LED drive, making this section a straightforward visual status indicator for the charging subsystem.

    
<Center>
    
![Screenshot 2026-02-04 140232](https://hackmd.io/_uploads/SJCJI7-wbx.png)



Figure 6. Output header-pin interface
</Center>    
    
This schematic block defines the main output header used to distribute power and control signals to the rest of the system. The header provides access to key rails generated or managed by the power subsystem, including the raw USB input (VUSB), battery voltage (BAT), regulated 5-V and 3.3-V outputs, as well as multiple ground references for stable current return paths. An enable (EN) pin is also included to allow the connected device to control power delivery or activate downstream circuitry. This header serves as the central interface point between the power board and the rest of the system’s electronics.

    
<Center>
    
![Screenshot 2026-02-04 140435](https://hackmd.io/_uploads/ByKPIm-vbe.png)




Figure 7. 5-V power-regulation stage
</Center>

The 5-V stage shown in Figure 7 implements the regulated 5VOUT rail generated by the ETA9740 charger/booster (U1). The 5-V output passes through a 0-Ω series resistor (R12), which serves as an optional placeholder for future filtering or current measurement if required during testing.

Local bulk decoupling is provided by capacitors C7, C8, and C9 (10 µF each), which stabilize the 5-V rail, reduce ripple, and supply transient current during sudden load changes. These capacitors are placed close to the output node to minimize trace inductance and maintain regulation stability.

A 5.1-V Zener diode (D6, MMSZ4689) is connected from the 5-V rail to ground to provide overvoltage clamping protection. In the event of abnormal regulation behavior, ringing, or external injection through the 5-V header, the Zener limits the rail voltage to a safe level, protecting downstream electronics.

Unlike previous revisions, this stage no longer includes a high-side MOSFET switching network or selectable 5-V/3.3-V routing. The 5VOUT rail is now provided directly to the output header as a continuously available regulated supply, simplifying the architecture while preserving protection and filtering functionality.

---

## Printed Circuit Board Layout


The current PCB layout, shown in Figures 8 and 9, is implemented as a two-layer board to support clear routing of the charging circuitry, buck-boost converter, protection stages, and subsystem interfaces during early development. This configuration ensures clean power distribution, controlled return paths, and adequate separation between sensitive analog and high-current traces. However, the layout is expected to transition to a more compact and cost-efficient two-layer design in later revisions once the schematic is finalized and non-essential circuitry—such as the optional 5-V regulation stage and other contingency components—has been validated or deemed unnecessary. As the design progresses, certain nets may be consolidated, routing simplified, and components reduced to streamline manufacturability and minimize board area. While the present revision fully satisfies the subsystem’s electrical and mechanical requirements and is suitable for prototyping, it remains subject to iterative refinement as testing, integration feedback, and updated system needs inform the final board architecture.


<Center>

![Screenshot 2026-02-02 135603](https://hackmd.io/_uploads/HkV5L7WDbx.png)


    
Figure 8. PCB layout
    
</Center>

This PCB layout illustrates the integrated placement and routing of the Li-Po charging circuit, buck-boost regulator, battery protection stage (LTC4412 with AON7403), LED indicators, and regulated 5-V and 3.3-V output distribution. High-current paths around the charger IC, inductor, ideal-diode MOSFET, and battery connector are kept short and wide to minimize resistive loss and voltage drop, while sensitive nodes are routed with controlled spacing to reduce noise coupling from switching regions.

Decoupling capacitors are positioned tightly around their respective ICs to minimize loop area and improve transient response. The 0-Ω option resistors placed after the ETA9740 5-V output and the TPS63031 3.3-V output allow future substitution of ferrite beads if additional filtering is required. The output header is placed along the bottom edge for straightforward connection to the system’s downstream electronics.

Four M3 mounting holes are included to provide mechanical stability within the visor enclosure, and fiducial markers are placed near the board corners to support automated assembly alignment. The layout reflects a functional first-revision prototype optimized for electrical integrity, manufacturability, and ease of testing.



<Center>


![Screenshot 2026-02-02 140054](https://hackmd.io/_uploads/SJCnIQZPWl.png)



    
Figure 9. 3D PCB Rendering
    
</Center>

The 3D rendering in Figure 9 provides a visual representation of the assembled power subsystem PCB, illustrating the placement and orientation of major components including the Li-Po battery connector, charger IC, buck-boost converter, protection circuitry, LED indicators, and output header. This view highlights mechanical clearances, connector accessibility, and overall board organization, helping verify that component spacing, switch positions, and mounting-hole locations align with the mechanical requirements of the system enclosure. While electrically equivalent to the layout shown previously, the 3D model offers a clearer perspective on real-world form and fit prior to fabrication.




---


## Flowchart

<p align="center">
  <img src="https://hackmd.io/_uploads/HJyZVhzb-g.png" style="width:99%; border:1px #eee; padding:1px; margin:20px;">
  <br>
    <Center>
  <strong>Figure 10: Power Subsystem Flowchart</strong>
</p>
    
</Center>

The flowchart in Figure 10 provides a high-level overview of the complete power subsystem architecture. USB-C input power is first delivered to the CC/CV charging unit, where the ETA9740 manages safe Li-ion charging and communicates charge status through its indicator outputs. Charging protection is reinforced by the battery’s onboard BMS, which monitors over-voltage, under-voltage, over-current, and over-temperature conditions. The single-cell Li-Po battery supplies a variable 2.75–4.2 V rail, which is converted to a regulated 3.3-V supply through the TPS63031 buck-boost converter. This regulated rail passes through an inline fuse before reaching the system’s main power switch, where the user or microcontroller can select between powering downstream loads or turning the system off entirely. The regulated output then supplies the ESP32 microcontroller, the IMU sensor, and the OLED display, each with their respective maximum current requirements. This flowchart summarizes how input power is conditioned, stored, regulated, protected, and distributed before the detailed circuitry in the following sections expands upon each functional block.




## BOM

As the schematic is presently defined, the selected components and overall architecture fully satisfy the established shall statements and functional requirements for the Power Subsystem. The design provides regulated 3.3-V power delivery with appropriate current capacity, incorporates integrated Li-Po charging and protection features, includes fault-tolerant elements such as ideal-diode ORing and resettable fusing, and maintains compatibility with all system-level electrical interfaces. The chosen components meet or exceed the electrical, thermal, and safety constraints identified during the conceptual and preliminary design phases. Although the design is currently complete and fully capable of supporting subsystem operation, testing, and integration, it remains subject to change as the project progresses; certain portions of the circuit—such as the optional 5-V rail and other “just-in-case” elements—may be removed, simplified, or replaced in later revisions depending on component availability, updated requirements, or insights gained during prototyping.

<Center>
    Table II. Simplified PCB Bill of Materials

</Center>

| **Comment / Value** | **Designator(s)** | **LCSC Part #** | **Description** | **Order Qty** | **Total Cost** |
|---|---|---|---|---:|---:|
| SUD50P06-15 | Q3 | C7472883 | -55℃~+150℃ 1 P-Channel 16mΩ@4.5V 336pF 4.707nF 46nC 49A 60V 73W TO-252 MOSFETs ROHS | 5 | $3.501 |
| 1k惟 | R3,R4,R7,R10 | C11702 | -55℃~+155℃ 1kΩ 50V 62.5mW Thick Film Resistor ±1% ±100ppm/℃ 0402 Chip Resistor - Surface Mount ROHS | 30 | $0.021 |
| SMAJ5.0CA | D9 | C19077524 | -55℃~+150℃ 1 400W@10/1000us 43.5A@10/1000us 5V 7V 800uA 9.2V Bidirectional IEC 61000-4-2 TVS DO-214AC(SMA) ESD and Surge Protection (TVS/ESD) ROHS | 7 | $0.2709 |
| GREEN | D2,D3,D4,D5,D7 | C965793 | -40℃~+85℃ 120° 249mcd 25mA 3V 516nm~531nm 531nm 80mW Discrete Diode Emerald Green Top-mount Water Clear 0402 LED Indication - Discrete ROHS | 37 | $0.2886 |
| 10uF | C3,C4,C7,C8,C9 | C440198 | 10uF 50V X5R ±10% 0805 Multilayer Ceramic Capacitors MLCC - SMD/SMT ROHS | 29 | $1.8937 |
| 1.5uH | L2 | C5120865 | 1.46A 1.5uH 2.1A 85mΩ Magnetic Shielded Inductor ±30% SMD,4x4mm Power Inductors ROHS | 7 | $0.3745 |
| TPS63031DSK | U5 | C15516 | -40℃~+125℃@(TJ) 1 1.8V~5.5V 2.4MHz 25uA 3.3V 900mA Buck-Boost Buck-Boost Built-in Fixed Yes SON-10-EP(2.5x2.5) DC-DC Converters ROHS | 5 | $4.952 |
| ETA9740 | U1 | C7465512 | -40℃~+85℃ 1 3.2V 3A 4.21V 4.5V~5.5V 80uA Charging IC Lithium Battery ESOP8 Battery Management ROHS | 5 | $1.3565 |
| LTC4412xS6 | U2 | C514442 | -40℃~+85℃ 1 110us 11uA 13us 2.5V~28V 350mV 635mV MOSFET TSOT-23-6 Gate Drivers ROHS | 5 | $15.234 |
| SW_SPDT | SW2 | C221841 | -20℃~+85℃ 6V PC Pin Rectangular Columnar SPDT Surface Mount, Vertical SMD Slide Switches ROHS | 5 | $5.835 |
| 1uH | L1 | C167953 | 16mΩ 1uH 5A 8A Magnetic Shielded Inductor ±20% SMD,5x5mm Power Inductors ROHS | 9 | $0.5031 |
| JST 2mm | J1 | C157932 | -25℃~+85℃ 100V 1x2P 2A 2mm PH Right Angle Tin UL94V-0 Wire-to-Board Connector ROHS | 5 | $0.952 |
| USB-TYPE-C-16P | J2 | C5178539 | -30℃~+80℃ 16P 20V 3A Right Angle USB 3.1 Type-C SMD Connector ROHS | 5 | $0.397 |
| 100nF | C1,C2,C11,C13,C16 | C1525 | 100nF 16V X7R ±10% 0402 Multilayer Ceramic Capacitors MLCC - SMD/SMT ROHS | 35 | $0.0455 |
| 10uF | C5,C6,C12,C14,C15 | C15525 | 10uF 6.3V X5R ±20% 0402 Multilayer Ceramic Capacitors MLCC - SMD/SMT ROHS | 35 | $0.196 |
| 3A | F2 | C2843508 | -40℃~+85℃ 1.2W 20mΩ 3A 0805 Resettable Fuses ROHS | 7 | $0.7469 |
| MMSZ4689-E3-08 | D6 | C21509 | -65℃~+150℃ 1 Independent 10uA@3V 350mW 4.85V~5.36V 5.1V SOD-123 Zener Diodes ROHS | 15 | $0.3795 |
| 56k惟 | R5 | C25796 | -55℃~+155℃ 50V 56kΩ 62.5mW Thick Film Resistor ±1% ±100ppm/℃ 0402 Chip Resistor - Surface Mount ROHS | 20 | $0.014 |
| 2N7002PW | Q2 | C5224236 | -55℃~+150℃ 1 N-channel 1.6V 1.85Ω@10V 1.8nC@4.5V 11pF 28pF 300mA 350mW 4pF 60V N-Channel SOT-323 MOSFETs ROHS | 10 | $0.248 |
| 100k惟 | R8,R9 | C25741 | -55℃~+155℃ 100kΩ 50V 62.5mW Thick Film Resistor ±1% ±100ppm/℃ 0402 Chip Resistor - Surface Mount ROHS | 20 | $0.016 |
| 5.1k惟 | R1,R2 | C25905 | -55℃~+155℃ 5.1kΩ 50V 62.5mW Thick Film Resistor ±1% ±100ppm/℃ 0402 Chip Resistor - Surface Mount ROHS | 20 | $0.016 |
| AON7403 | Q1 | C5310961 | -55℃~+150℃ 1 P-Channel 1.2V 3.448nF 30V 30nC@15V 38W 421pF 50A 8mΩ@10V PDFN-8 MOSFETs ROHS | 5 | $0.9135 |
| 10nF | C10 | C15195 | 10nF 50V X7R ±10% 0402 Multilayer Ceramic Capacitors MLCC - SMD/SMT ROHS | 20 | $0.028 |
| 10k惟 | R6 | C25744 | -55℃~+155℃ 10kΩ 50V 62.5mW Thick Film Resistor ±1% ±100ppm/℃ 0402 Chip Resistor - Surface Mount ROHS | 20 | $0.016 |
| 0惟 | R11,R12 | C17477 | -55℃~+155℃ 0Ω 125mW 150V Thick Film Resistor ±1% ±800ppm/℃ 0805 Chip Resistor - Surface Mount ROHS | 20 | $0.042 |



<Center>Table III. Shopping BOM – Off-Board Components</Center>


| **Components** | **Manufacturer** | **Part Number** | **Distributor** | **Distributor Part Number** | **Quantity** | **Price** | **Website URL** |
|----------------|------------------|------------------|------------------|------------------------------|--------------|-----------|------------------|
| SPDT Slide Switch | E-Switch | RR511D1121 | DigiKey | EG4777-ND | 1 | $1.22 | https://www.digikey.com/en/products/detail/e-switch/RR511D1121/2116256 |
| LiPo Charger + Boost Board | Seeed Studio | SKU 106990290 | Seeed Studio | N/A | 1 | $4.99 | https://www.seeedstudio.com/Lipo-Rider-Plus-p-4204.html |
| 3.7V 4000 mAh Li-Po Battery | Qimoo | N/A | Amazon | B0CNLNY597 | 1 | $13.49 | https://www.amazon.com/dp/B0CNLNY597 |
| Hook-Up Wire (30 AWG) | Tensility International Corp | 30-00695 | DigiKey | 839-30-00695-DS-ND | 1 roll | $1.93 | https://www.digikey.com/en/products/detail/tensility-international-corp/30-00695/24671317 |
| 700 mA Glass Fuse | OptiFuse | MSC-700MA | DigiKey | 2298-MSC-700MA-ND | 2 | $1.38 | https://www.digikey.com/en/products/detail/optifuse/MSC-700MA/12090270 |
| Inline Fuse Holder (Waterproof) | YOUCHENG | N/A | Amazon | B0C61DZ9CM | 1 pack |$7.99 | https://www.amazon.com/dp/B0C61DZ9CM |

---

## Analysis

### Power Consumption 
The purpose of this section is to estimate both the peak current consumption and the nominal (average) operating current of the Power Subsystem. Peak current determines the fuse rating and worst-case regulator requirements, while nominal current determines battery runtime. The ESP32-S3 draws the largest dynamic load in the system. According to its datasheet, it can consume up to 340 mA at 3.3 V during Wi-Fi transmissions at maximum output power [12].

The SSD1309 OLED requires a separate high-voltage VCC rail between 7–16 V for panel driving [13]. However, unlike some OLED modules, this particular breakout board does not include a charge pump inside the SSD1309 itself. Instead, the voltage boosting is performed on the breakout PCB using a dedicated low-noise RT9193 LDO regulator (visible in the breakout schematic and confirmed in the datasheet) [14] [15]. The RT9193 accepts 2.5–5.5 V input and produces a regulated higher-voltage output (commonly 7.0–8.0 V for OLED VCC). Because the OLED’s panel-driving current all flows through this onboard regulator, the power ultimately comes from the visor’s 3.3-V system rail, meaning the entire display load still reflects back onto the 3.3-V supply through the booster stage.

As a result, the OLED’s worst-case current draw of 40 mA can be added directly to the 3.3-V budget. The ICM-20948 IMU requires only 0.76 mA [16], and the battery-indicator LED module consumes 5–15 mA, depending on which segments are active.


**Peak Operating Current**

**Initial calculated peak:**
- 340 mA (ESP32-S3 peak Wi-Fi)
- 40 mA (OLED worst case)
- 1 mA (IMU)
- 15 mA (battery indicator)

**Total peak (raw):**  
**396 mA → rounded to 400 mA**

**Add 10% design margin:**  
400 mA × 1.10 = **440 mA**

**Final peak-design current: ~440 mA**

---

 **Nominal Operating Current**

**Initial calculated nominal:**
- 180–220 mA (ESP32-S3 typical RF load)
- 20 mA (OLED typical)
- 1 mA (IMU)
- 5 mA (indicator)

**Total nominal (raw):**  
**206–246 mA → rounded to 230–250 mA**

**Add 10% design margin:**  
230–250 mA × 1.10 = **253–275 mA**

**Final nominal-design current: ~260–275 mA**


These values provide sufficient margin for regulator sizing, fuse selection, thermal evaluation, and battery-life calculations, ensuring compliance with the subsystem’s performance and safety requirements.

---

### Fuse Selection  

Following the shall-statement requirement, the inline fuse must be rated at least **1.5× the maximum steady-state current**. The 1.5× factor is a standard derating guideline in low-voltage DC systems to ensure that:

- The fuse does not trip during normal operation, including short bursts of higher current (e.g., ESP32-S3 Wi-Fi transmissions).
- Transient inrush currents from regulator startup, display loading, or battery-indicator switching do not cause nuisance blows.
- Thermal variations inside the visor enclosure do not push the fuse into its blow curve during normal use.
- The fuse still opens reliably under true overcurrent faults, such as short circuits or regulator failure.

From the power budget, the **raw peak load** on the 3.3 V rail is approximately **400 mA**. Applying the 10% design overshoot gives **440 mA**.

The minimum fuse rating is then:

$$
I_{\text{fuse}} \ge 1.5 \times 0.44\,\text{A} = 0.66\,\text{A}
$$

A **0.70–0.75 A fuse** is therefore appropriate, satisfying the 1.5× derating requirement while providing adequate margin for expected transient events. This rating protects the 3.3 V rail and downstream wiring without nuisance trips during normal wireless activity or OLED operation.


To physically incorporate this protection into the system, the selected **700 mA OptiFuse MSC-700MA glass fuse [8]** will be installed using an **inline screw-type 5×20 mm fuse holder** (PNGKNYOCN brand). This holder includes **22 AWG pre-terminated leads** and a **flame-retardant nylon housing rated for up to 5 A**, providing mechanical robustness and safe handling. The fuse holder will be **soldered directly to the 3.3 V output header on the PCB**, with one lead connected to the regulated 3.3 V rail and the other routed to the system’s main power switch. This wiring configuration places the fuse **in series with all downstream loads**, ensuring that any overcurrent fault on the ESP32, OLED, or IMU will cause the fuse to open before damage propagates. The screw-cap style holder also allows **rapid fuse replacement** during testing or field use while maintaining secure electrical contact and strain relief.


---

### Reverse Polarity Protection  

A MOSFET configured as an ideal diode is used instead of a standard series diode to avoid the 0.6–0.7 V forward drop typical of silicon rectifiers. When the MOSFET gate is driven fully on, its low Rds(on) produces only a small voltage drop—typically on the order of a few tens of millivolts at the design current—preserving regulator headroom, maintaining output regulation during Wi-Fi bursts, and improving overall efficiency. 

Figure 11 below from [17] illustrates the typical relationship between MOSFET gate-source voltage and Rds(on). At the design gate drive, the selected MOSFET operates in its low-resistance region, resulting in only tens of millivolts of drop while the drain current is small such as the 0.44 A peak, compared to ~0.6–0.7 V for a silicon diode.

![Screenshot 2025-11-23 212437](https://hackmd.io/_uploads/BJINgLWW-g.png)
<Center>
Figure 11. Drain to Source On Resistance vs Gate
Voltage and Drain Current
    
</Center>




In addition to reverse-polarity blocking, transient suppression is implemented at the USB-C power entry and on the regulated 5-V output rail. A TVS diode (SMAJ5.0CA) [18] is placed on the USB-C VBUS input to clamp fast ESD and cable-insertion transients before they reach the charger IC and downstream regulators. The SMAJ-series device is rated for 400 W peak pulse power (10/1000 µs) and is specified for IEC 61000-4-2 ESD immunity up to 30 kV (air/contact), making it suitable for real-world plug/unplug handling events. For the 5.0-V device option, the datasheet specifies a 5 V standoff voltage (VRWM = 5 V), with breakdown and clamping behavior defined in the electrical characteristics table. This ensures that normal 5-V operation is unaffected while high-energy transients are safely diverted to ground.

A 5.1-V Zener diode (MMSZ4689) [19] is also placed on the regulated 5-V output rail to provide a secondary clamp layer protecting downstream electronics from abnormal overvoltage conditions. Such conditions could include regulator fault behavior, ringing caused by low-ESR ceramic capacitors, or external voltage injection through the 5-V output header. The MMSZ4689 specifies a nominal Zener voltage of 5.1 V, with defined minimum and maximum limits in its electrical characteristics table, making it appropriate as a compact “last line of defense” clamp for a 5-V rail.

While a TVS diode does not prevent reverse-battery connection and typically adds more capacitance and leakage than desired in a small wearable device [20], it is highly effective for high-speed transient suppression. The MOSFET-based ideal-diode configuration directly blocks reverse-polarity faults with minimal series loss, while the TVS and Zener devices provide layered transient and overvoltage protection. This coordinated approach aligns with safety and robustness requirements by preventing accidental battery-reversal damage while still protecting against abnormal surge and ESD events.




---

### Regulator Efficiency and Runtime Margin  
The earlier runtime calculations assumed ideal conversion, but in practice the ETA9740 and the 3.3-V linear/buck stage introduce losses. To demonstrate compliance with the 6-hour operational requirement under worst-case conditions, a conservative overall efficiency of **85%** is assumed.

$$
\eta = 0.85
$$

Peak load on the 3.3-V rail is:

$$
I_{\text{peak}} = 0.40\,\text{A}
$$

Peak output power:

$$
P_{\text{out}} = 3.3\,\text{V} \times 0.40\,\text{A} = 1.32\,\text{W}
$$

Required battery-side input power:

$$
P_{\text{in}} = \frac{1.32\,\text{W}}{0.85} \approx 1.55\,\text{W}
$$

A 4000-mAh (4-Ah) Li-Po cell stores:

$$
E = 3.7\,\text{V} \times 4\,\text{Ah} = 14.8\,\text{Wh}
$$

Worst-case continuous runtime:

$$
t_{\text{peak}} = \frac{14.8\,\text{Wh}}{1.55\,\text{W}} \approx 9.55\,\text{hours}
$$

Even at **full peak load**, and using a conservative efficiency assumption, the design achieves over **9.5 hours** of operation—far exceeding the six-hour shall requirement. In realistic scenarios, ESP32-S3 transmit power, OLED activity, and indicator load all reduce significantly, providing even greater runtime.

---

### Thermal Performance  

To evaluate worst-case heating, the power dissipated in the 3.3-V regulator is computed using the **maximum peak design current** rather than the nominal load. Using the 10%-overshoot peak current of **0.44 A**, the regulator dissipation is:

$$
P = (3.7 - 3.3)\,\text{V} \times 0.44\,\text{A}
$$

$$
P = 0.4\,\text{V} \times 0.44\,\text{A} = 0.176\,\text{W}
$$

With a regulator thermal resistance of approximately 60 degrees C/W, the worst-case temperature rise is:

$$
\Delta T = 0.176\,\text{W} \times 60\,^\circ\text{C/W}
= 10.6^\circ\text{C}
$$

Assuming a 25 °C internal ambient, the regulator surface temperature becomes:

$$
T_{\text{surface}} = 25^\circ\text{C} + 10.6^\circ\text{C}
\approx 35.6^\circ\text{C}
$$

This remains comfortably below the **41 °C limit** defined for wearable-device user-touchable surfaces, even under peak current conditions. Therefore, the regulator satisfies the thermal shall requirement with adequate margin.

---

### Wire Gauge & PCB Traces  
Peak currents of ~400 mA are easily supported by AWG 26 silicone-insulated conductors, which can carry well over 1 A in chassis wiring applications. This provides compliance with NEC/NESC requirements for insulation, ampacity, and conductor safety [2], [21]. In particular, the wire gauges in this design are selected directly from the expected peak and continuous current in each path, rather than from generic layout rules. Combined with the fuse and strain-relief routing, the wiring meets both electrical and mechanical safety expectations.

On the PCB, the main 3.3 V and battery traces are sized using industry-standard trace-ampacity guidelines for **1 oz/ft² copper** outer layers. For the expected **240–440 mA** operating range and a target temperature rise of **≤ 10 °C**, these guidelines indicate required trace widths on the order of **5–10 mil**, as verified using an online IPC-2221 trace-width calculator [22], [23]. Thus, the PCB trace widths are explicitly back-calculated from the anticipated current flow and the allowable temperature rise, rather than chosen arbitrarily. These tools determine the minimum width needed for a given current, allowable temperature rise, and copper thickness.

To ensure full compliance even under worst-case conditions, higher-current portions of the design were also evaluated. The USB-C input path and battery-charging path may experience currents approaching **3 A** during fast-charge operation. Using IPC-2221 equations with **1 oz copper**, **10 °C temperature rise**, **1-inch trace length**, and **room-temperature ambient** [23], the required trace widths are:

- **Internal layers:** 3.56 mm  
- **External layers:** 1.37 mm  


The external-layer requirement is significantly smaller because exposed copper dissipates heat more effectively through convection and radiation. These computed widths establish the minimum safe geometry for the high-current charging path to prevent overheating or copper fatigue during continuous use.

For the regulated **3.3 V output rail**, the worst-case current is inherently limited by the inline **700 mA fuse**, which intentionally opens before PCB traces are thermally overstressed. Additionally, the TPS63031 buck-boost converter used in this design can supply a maximum of **800 mA**, which represents the absolute upper bound the rail could ever see. Re-evaluating the trace-ampacity requirements under the same parameters but with **I = 0.8 A** yields:

- **Internal layers:** 0.574 mm  
- **External layers:** 0.221 mm  

Here again, the trace geometries are sized directly from the maximum possible 3.3 V rail current and the chosen 10 °C temperature-rise limit.

These dimensions are readily achievable on standard 2-layer and 4-layer PCB stackups and provide substantial thermal and electrical margin. Designing traces such that the fuse—not the PCB—fails under a fault condition ensures compliance with NEC/NESC overcurrent-protection expectations and prevents localized PCB hot spots or conductor burnout.


## Battery Management System (BMS) Operation Analysis

A 1-cell lithium-polymer battery typically includes an integrated protection circuit module (PCM), commonly referred to as the BMS. This small circuit provides several essential safety functions by combining a dedicated protection IC with back-to-back MOSFET switches and a current-sense resistor. In a typical 1S Li-Po pack, the protection IC (such as the DW01 or Seiko S-8261 family [24]) continuously monitors the cell voltage and battery current while controlling the gate of the MOSFET pair. These MOSFETs act as an electronic switch, allowing the BMS to instantly disconnect the battery from the load or charger whenever a fault condition is detected. The sense resistor (R<sub>sense</sub>) produces a millivolt-scale signal proportional to battery current, enabling the protection IC to compare this voltage against programmed OCP thresholds.

During **over-voltage protection (OV)**, the protection IC disconnects the charge MOSFET once the cell approaches approximately 4.25–4.30 V, preventing lithium plating and unsafe charging conditions. After the voltage drops slightly due to hysteresis, the MOSFETs automatically reconnect. In **under-voltage protection (UV)**, the BMS disables the discharge MOSFET whenever the cell voltage falls to roughly 2.5–2.7 V. This prevents deep discharge, copper dissolution, and permanent damage to the battery; the pack remains disconnected until an external charger restores the cell to a safe voltage.

For **over-current protection (OCP)** and short-circuit events, the BMS monitors the voltage across R<sub>sense</sub>. If this voltage exceeds an internal threshold—typically corresponding to 3–5 A for a 1S Li-Po pack—the protection IC rapidly opens the MOSFETs within microseconds, isolating the battery from the fault. Some protection PCMs also include **over-temperature protection (OTP)** using an NTC thermistor embedded in the pack. If the cell becomes too cold (below about 0 °C) or too hot (above ~60 °C), charging and/or discharging is disabled to keep the cell within its safe operating limits.

In the context of this subsystem, the BMS ensures that the Li-Po cell cannot be overcharged, over-discharged, subjected to excessive current, or operated outside safe thermal limits. Since the visor’s peak design current of approximately 0.44 A is far below the BMS’s OCP threshold, normal operation will never trigger protective shutdown. The ETA9740 charger performs CC/CV charging and power conversion, while the BMS enforces cell-level electrical safety. Together, these protections support compliance with IEC 62133-2 requirements and provide a reliable hardware safety foundation for the Power Subsystem [1].



---

### Compliance with Power Subsystem Shall Statements  
Rather than treating the shall requirements as a separate analysis, each of the quantitative and qualitative results above directly corresponds to a specific subsystem requirement. The use of a **4000-mAh 1S Li-Po battery**, combined with the measured power budget and efficiency analysis, demonstrates compliance with the **≥6-hour operation requirement**. The regulated **3.3-V ±5% output** is maintained throughout both nominal and peak load conditions. NEC/NESC grounding, conductor sizing, and overcurrent protection requirements are satisfied through the AWG 26 wiring selection, fused output rail, and MOSFET-based reverse polarity protection.

Battery protection requirements (OV, UV, OCP, OTP) defined by IEC 62133-2 are inherently satisfied by the **ETA9740’s integrated BMS features** and the BMS in the LiPo battery housing. The CC/CV charging behavior required for USB-C input is also executed by the ETA9740, ensuring safe charging profiles, adherence to C-rate limits, and temperature-dependent charge inhibition. Furthermore, the thermal and efficiency analyses show that surface temperatures remain well below **41 °C**, the converters exceed their **≥90% efficiency** targets under many operating points, and conservative margin calculations validate that the design maintains safe, reliable performance with appropriate derating.

Collectively, these analyses confirm that the Power Subsystem fully satisfies all defined shall statements and meets the functional, safety, and performance requirements of the Smart Golf Visor [25].


---

## References


[1] “IEC 62133: Safety Testing for lithium ion batteries,” Intertek, duplicate listing used for safety context. [Online]. Available: https://www.intertek.com/batteries/iec-62133/ (accessed Oct. 14, 2025). 

[2] W. T. W. Guy, “The Ultimate Guide to Wire Ampacity and NEC Basics,” Wire & Cable Your Way Blog, https://blog.wireandcableyourway.com/the-ultimate-guide-to-wire-ampacity-and-nec-basics (accessed Oct. 27, 2025). 

[3] “IEC 62301:2011 – Household electrical appliances – Measurement of standby power,” IEC. [Online]. Available: https://webstore.iec.ch/en/publication/6789 (accessed Oct. 24, 2025). 

[4] Eta Semiconductor, “ETA9740 Charger/Booster Circuit,” Hardware Design Guide, Version 1.1. 

[5] Texas Instruments, “TPS63031: High-Efficiency Buck-Boost Converter,” Datasheet SLVS696D, Apr. 2020.

[6] “CC and CV charging of Lithium Polymer Battery,” Lithium_Polymer_Battery_net, https://www.lithium-polymer-battery.net/cc-and-cv-charging-of-lithium-polymer-battery/ (accessed Oct. 17, 2025). 

[7] E-Switch, “RR5 Series Rocker Switch Specifications,” Datasheet, Nov. 16, 2021. 

[8] MSC Corporation, “700 mA Fast-Acting Fuse – Product Specification,” Component Datasheet. 

[9] “ESD protection basics with TVS Diodes,” Altium, https://resources.altium.com/p/esd-protection-basics-tvs-diodes (accessed Dec. 2, 2025). 

[10] Linear Technology, “LTC4412 Low Loss PowerPath Controller in ThinSOT,” Datasheet. 

[11] Alpha and Omega Semiconductor, “AON7403 30V P-Channel MOSFET,” Datasheet. 

[12] Espressif Systems, “ESP32-S3 Series Datasheet v2.0,” Jul. 2023. 

[13] Solomon Systech, “SSD1309 OLED/PLED Segment/Common Driver with Controller,” Datasheet, 2010. 

[14] Waveshare Electronics, “1.51-inch OLED Transparent Driver Board – Schematic,” Version 1.0. 

[15] Richtek Technology Corporation, “RT9193: 300 mA Ultra-Low-Noise LDO Regulator,” Datasheet DS9193-18, Jun. 2022. 

[16] TDK InvenSense, “ICM-20948: World’s Lowest Power 9-Axis MEMS MotionTracking Device,” Datasheet DS-000189, Rev. 1.3, Jun. 2017. 

[17] ON Semiconductor, “FDD24AN06L-F085 N-Channel Power MOSFET,” Datasheet, 2018. 

[18] Zhuhai Hongjiacheng Technology Co., Ltd., “SMAJ5.0CA Surface Mount Transient Voltage Suppressor,” Datasheet, Rev. 1.1, Feb. 18, 2023. 

[19] Vishay, “MMSZ4689-E3 5.1 V Zener Diode (SOD-123),” Datasheet. 

[20] A. Godbole and D. Jain, “Going TVS-less in Automotive Reverse Battery Protection Designs,” Ti, https://www.ti.com/lit/an/slyt818/slyt818.pdf?ts=1751611828493 (accessed Dec. 3, 2025). 

[21] Consolidated Electronic Wire & Cable, “26 AWG Wire – Product Specification #30-00695,” Technical Data Sheet. 

[22] “Trace width calculator,” AdvancedPCB, https://www.advancedpcb.com/en-us/tools/trace-width-calculator/ (accessed Dec. 2, 2025). 

[23] “IPC-2221: The standard for printed circuit board design,” NextPCB, https://www.nextpcb.com/blog/ipc-2221 (accessed Dec. 2, 2025). 

[24] Xysemi, “DW01 Lithium-Ion/Polymer Battery Protection IC,” Reference Datasheet. 

[25] IEEE, “IEEE code of ethics | IEEE,” IEEE Advancing Technology for Humanity, https://www.ieee.org/about/corporate/governance/p7-8 (accessed Sep. 18, 2025).  
