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



<p align="center">
  <strong>Table I</strong><br>
  Unified Constraints, Standards/Guidance, and Design Impact Table
</p>


| **Constraint** | **Standard** | **Relevance to Design** | **Design Rat** |
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

NEC/NESC guidelines require that conductors be selected based on expected current loads, insulation type, and environmental factors. The visor electronics draw up to 400–500 mA during ESP32-S3 peak wireless transmission. According to NEC 310.16, AWG 26 conductors have more than sufficient current-carrying capability for this application.  

To ensure mechanical robustness inside the wearable enclosure, flexible silicone-insulated wiring is used for the battery and regulator outputs. Proper insulation and strain relief prevent conductor damage due to repeated movement or flexing of the visor. The inline fuse on the regulated rail protects the wiring from overheating during abnormal conditions, and it will be implemented in an enclosed, insulated fuse holder (or fully heat-shrunk inline assembly) located inside the visor housing. This keeps all live conductors mechanically protected and shielded from sweat, moisture, and incidental contact, supporting compliance with NEC/NESC fault-protection and environmental-protection expectations.


---

### Efficiency — IEC 62301

IEC 62301 emphasizes minimizing standby losses, which are the small but continuous power drains that occur even when a device is not performing active work [4]. In battery-powered electronics, standby losses arise from quiescent current, background fuel-gauge operation, trickle-charge behavior, and any residual converter activity when the load is light. Managing these losses is essential because they directly reduce runtime and generate avoidable heat within the wearable device.

The ETA9740 minimizes standby losses through its low quiescent current of approximately 80 µA at VBAT = 3.6 V [5], combined with an efficient automatic mode-switching architecture. This low idle draw reduces the trickle drain on the battery while preserving nearly full capacity for active operation. The ETA9740 also exhibits stable switching behavior across its operating modes, and because it uses a synchronous architecture with controlled switching transitions, it does not introduce significant on/off switching noise or transient spikes that could interfere with the downstream 3.3-V regulator or sensitive IMU electronics. This ensures predictable regulation performance during transitions between light-load, standby, and active states.

The high conversion efficiency—reaching up to 96% in boost mode according to its datasheet—reduces heat generation inside the visor during normal operation. Minimizing self-heating is critical in a wearable device, because user-touchable surfaces must remain below 41 °C after 60 minutes of typical use to meet the subsystem’s defined thermal safety requirement. Lower standby losses therefore directly support both runtime and thermal margins, helping the design maintain safe temperatures when worn on the user’s head.

The reduced idle drain also improves alignment with the six-hour minimum operational requirement by ensuring that off-cycle and low-activity periods do not prematurely deplete the battery.

In addition to the charger, the TPS63031 buck-boost converter also supports IEC 62301 efficiency expectations by offering exceptionally low quiescent current—only **35 µA typical**, as shown in its datasheet (page 5) [6]. This is critical because the converter remains connected to the battery at all times, even when the rest of the system is idle. Its automatic power-save mode further reduces switching activity under light-load conditions, preventing unnecessary conversion losses while maintaining a stable 3.3-V rail. When active, the TPS63031 operates with up to **94% efficiency in buck mode and 90%+ in boost mode**, minimizing heat generation within the visor enclosure and supplementing the thermal benefits provided by the ETA9740. Together, the low standby drain and high operating efficiency of the TPS63031 contribute directly to extended battery life, reduced waste heat, and compliance with the subsystem’s runtime and thermal-safety requirements.

---

## Overview of Proposed Solution



The Power Subsystem is centered on the ETA9740 charger/booster, which manages both the charging of the Li-Po battery through a USB-C port and the conversion of battery voltage to downstream rails when the visor is in use. When connected to USB-C, the device charges the battery in a constant-current/constant-voltage profile, ensuring safe and efficient energy replenishment [7]. When disconnected, it switches to discharge mode and provides a stable voltage for subsequent regulation.

A dedicated 3.3-V regulator supplies the system logic, providing clean power to the ESP32-S3, the OLED driver, and the IMU. This regulation is handled by the TPS63031 buck-boost converter, which maintains a fixed 3.3-V output even as the battery voltage varies from 4.2 V down to its 2.75 V cutoff. This capability ensures uninterrupted operation during heavy load events and prevents brownout conditions as the battery discharges.

An inline fuse protects this regulated rail, while a MOSFET-based reverse-polarity protection stage ensures that the battery cannot be accidentally connected backwards. The ideal-diode IC used in this stage blocks reverse polarity with minimal voltage drop, improving efficiency while safeguarding both the charger and the buck-boost converter.

This architecture minimizes power loss, lowers thermal output, and ensures consistent operation under high-current conditions such as wireless transmissions and rapid OLED refresh cycles.

---

## Interface with Other Subsystems



The Power Subsystem provides a regulated 3.3-V power rail to the Communications Subsystem, supplying the ESP32-S3 microcontroller through its dedicated 3.3-V input pin and ground reference. It also powers the Heads-Up Display Subsystem, including both the IMU and the OLED display. Because these components draw dynamic and burst-type current loads—particularly the ESP32-S3 during wireless activity—the regulator is selected and configured to maintain output stability with sufficient transient response to support these fluctuations. The TPS63031 buck-boost converter further assists in this role by holding the rail at 3.3 V even as battery voltage changes, ensuring consistent operation throughout the discharge cycle.

The subsystem also integrates a user-controlled power input through the RR551D1123 [8] rocker switch and provides battery-level feedback through dedicated LED indicator's on the PCB. Mechanical integration information—including battery footprint, PCB outline, and switch dimensions—is provided to the Visor Design Subsystem to ensure proper placement and secure mounting inside the visor housing.

The regulated power path is routed from the 3.3-V header output pin to the inline fuse holder (containing the 700 mA protection fuse [9]), then to the main power switch, and finally to the input power nodes of both the Communications Subsystem and the Heads-Up Display Subsystem. This ensures that all downstream electronics receive protected, switched 3.3-V power from a single controlled distribution point.

---

## Buildable Schematic 

    
The overall schematic, as shown in Figures 1–7, fully satisfies the established shall statements and functional requirements for the Power Subsystem at this stage of the design. The charging-circuit schematic provides compliant Li-Po charging, protection, and safe power-path management, while the buck-boost converter schematic delivers the regulated 3.3-V rail required by the subsystem loads. The reverse-polarity protection circuit ensures safe battery connection, and the LED battery-level indicator schematic provides basic state-of-charge visibility for user feedback. Additionally, the output header-pin interface supports proper connectivity to downstream subsystems, and the optional 5-V regulation stage is included to preserve flexibility for future system needs. While the present schematic meets all electrical, safety, and interface requirements and is fully capable of supporting prototyping and integration, it remains subject to revision as the project progresses; optional circuits may be removed or simplified if later testing or design updates indicate they are unnecessary, redundant, or non-optimal for the final implementation.   
<p align="center">

  <img src="https://hackmd.io/_uploads/BJFAukh--g.png" alt="Screenshot 2025-11-30 214201">

  <br>
  Figure 1. Overall system schematic

</p>


Figure 1 presents the complete power subsystem schematic for the device, integrating USB-C input handling, battery charging, protection, voltage regulation, and output power distribution into a unified design. The circuit accepts 5-V power from a standard USB-C source and conditions it through protection elements before routing it into the ETA9740 switching charger, which manages safe Li-Po charging and provides real-time charge-level indication. A dedicated ideal-diode controller protects the battery connection, while a TPS63031 buck-boost converter generates a stable 3.3-V rail for system electronics. The design also includes a switchable 5-V regulation stage that allows the user or system to select between OFF, 5-V, or 3.3-V output modes. All regulated rails and control lines are then routed to a centralized output header for integration with the broader system. Each functional block of the schematic is described in more detail in the following sections.

    
<p align="center">

  <img src="https://hackmd.io/_uploads/SJkoF13-Ze.png" alt="Screenshot 2025-12-01 211341">

  <br>
  Figure 2. Charging-circuit schematic

</p>


The charging schematic shown in Figure 2 implements the complete Li-ion charging front end for the system using the ETA9740 single-inductor switching charger. USB power is accepted through the USB-C connector, where 5.1 kΩ pull-down resistors on the CC1 and CC2 pins identify the device as a power sink and enable a standard 5-V supply from any compliant USB-C source. The incoming VBUS rail is routed through a 3-A fuse for over-current protection, followed by an ESD (Electrostatic Discharge) suppression diode, which clamps high-voltage ESD events that commonly occur during cable insertion and protects sensitive downstream components [10]. This diode works together with the bank of high-frequency and bulk capacitors to filter transient spikes and stabilize the input rail. The conditioned 5-V supply then feeds the ETA9740, which uses a 1-µH inductor and the surrounding bypass capacitors to operate as a high-efficiency buck converter, allowing the device to safely perform pre-charge, fast-charge, and constant-voltage regulation of a 1-cell Li-Po battery [5]. The charge current is set by the 56-kΩ ISET resistor, corresponding to a 3-A fast-charge mode per the datasheet specifications. The BAT pin distributes controlled charging current to the battery while the integrated fuel-gauge hardware drives the LED indicators to display approximate charge level. An LM66100 ideal-diode controller is included in series with the battery output to prevent reverse current flow and maintain an efficient, low-loss power-path handoff between USB input and battery output. Collectively, these elements form a protected, standards-compliant, high-efficiency charging subsystem suitable for powering downstream 3.3-V regulators and system loads.

    

<p align="center">

  <img src="https://hackmd.io/_uploads/rkj3YJn--e.png" alt="Screenshot 2025-12-01 210103">

  <br>
  Figure 3. Buck-boost converter schematic

</p>

The buck-boost schematic shown in Figure 3 implements the TPS63031 synchronous regulator, which provides a regulated 3.3-V output even as the Li-Po battery voltage rises above or falls below the 3.3-V rail. The battery input is filtered through C10 (10 µF) and C11 (100 nF), which supply instantaneous current during switching transitions and reduce high-frequency noise at the VIN and VINA pins. The 1.5-µH inductor (L2) serves as the main energy-storage element that enables the device to automatically transition between buck and boost operation depending on battery voltage, as described in the TPS63031 operating principles [6]. On the output side, C14 (22 µF) and the high-frequency bypass capacitor C12 (100 nF) stabilize the 3.3-V rail, reduce ripple, and maintain regulation during sudden load changes. Together, these components allow the converter to deliver a stable 3.3-V supply for downstream digital electronics while maintaining high efficiency across the battery’s full discharge range.

    
<p align="center">

  <img src="https://hackmd.io/_uploads/Sk5k91nW-l.png" alt="Screenshot 2025-12-01 210201">

  <br>
  Figure 4. Reverse-polarity protection for battery connection

</p>
 
    
The reverse-polarity protection schematic in Figure 4 uses the LM66100 ideal-diode device in series with the battery connector J4 and the system power path. The positive terminal of the battery feeds the VIN pin of the device, while the protected system voltage is taken from VOUT. This ensures that current is allowed to flow only when the battery is connected with the correct orientation and when VIN is higher than VOUT. Internally, the LM66100 uses a low-resistance MOSFET and fast comparator circuitry to block current if the battery is accidentally reversed or if the downstream rail becomes higher than the battery voltage, thereby preventing damage to the cell and upstream components while maintaining a very small forward voltage drop during normal operation [11]. With the enable pin tied for continuous operation and the status pin left unused, the device functions as an efficient solid-state alternative to a diode, providing reliable reverse-polarity and reverse-current protection for the battery input.

    
<p align="center">

  <img src="https://hackmd.io/_uploads/ry-f912W-l.png" alt="Screenshot 2025-12-01 211138">

  <br>
  Figure 5. LED battery-level indicator schematic

</p>

    
This portion of the schematic implements the LED battery-level indicator driven by the ETA9740’s fuel-gauge outputs. Each LED pin from the charger (LED1, LED2, LED3) switches its corresponding LED pair on or off to display the battery’s approximate charge state. The LEDs are arranged so that different combinations illuminate as the battery voltage increases, providing a simple visual indication of 25%, 50%, 75%, and full charge levels. This circuit does not regulate current itself—the ETA9740 internally controls the LED drive, making this section a straightforward visual status indicator for the charging subsystem.

    
<p align="center">

  <img src="https://hackmd.io/_uploads/SJfS5JnWWx.png" alt="Screenshot 2025-12-01 210638">

  <br>
  Figure 6. Output header-pin interface

</p>

    
This schematic block defines the main output header used to distribute power and control signals to the rest of the system. The header provides access to key rails generated or managed by the power subsystem, including the raw USB input (VUSB), battery voltage (BAT), regulated 5-V and 3.3-V outputs, as well as multiple ground references for stable current return paths. An enable (EN) pin is also included to allow the connected device to control power delivery or activate downstream circuitry. This header serves as the central interface point between the power board and the rest of the system’s electronics.

    
<p align="center">

  <img src="https://hackmd.io/_uploads/r1lPqkhbWx.png" alt="Screenshot 2025-12-01 211517">

  <br>
  Figure 7. 5-V power-regulation stage &amp; switch

</p>

The 5-V power-regulation and switching stage shown in Figure 7 provides controlled distribution of regulated supply rails to the rest of the system. The 5-V input passes through ferrite-bead filtering and local bulk capacitance to stabilize the rail and suppress high-frequency noise, while a transient-suppression diode protects against voltage spikes. A high-side P-channel MOSFET (Q1) acts as the main load switch, and an N-channel MOSFET (Q2) provides gate control, allowing the system to selectively enable one of three states: fully off, 5-V output, or 3.3-V output depending on the position of the selector switch. The RC network around the MOSFET gates provides controlled slew-rate limiting to reduce inrush current when a rail is enabled, and an indicator LED illuminates when the selected output rail is active. Although the original design included a USB fast-charging controller, this portion of the circuitry is no longer used, and the stage now functions solely as a clean, protected, and switchable power-routing block for the regulated rails presented on the output header.

---

## Printed Circuit Board Layout


The current PCB layout, shown in Figures 8 and 9, is implemented as a four-layer board to support clear routing of the charging circuitry, buck-boost converter, protection stages, and subsystem interfaces during early development. This configuration ensures clean power distribution, controlled return paths, and adequate separation between sensitive analog and high-current traces. However, the layout is expected to transition to a more compact and cost-efficient two-layer design in later revisions once the schematic is finalized and non-essential circuitry—such as the optional 5-V regulation stage and other contingency components—has been validated or deemed unnecessary. As the design progresses, certain nets may be consolidated, routing simplified, and components reduced to streamline manufacturability and minimize board area. While the present revision fully satisfies the subsystem’s electrical and mechanical requirements and is suitable for prototyping, it remains subject to iterative refinement as testing, integration feedback, and updated system needs inform the final board architecture.


<p align="center">

  <img src="https://hackmd.io/_uploads/HyQyz23b-x.png" alt="Screenshot 2025-12-02 103610">

  <br>
  Figure 8. PCB layout

</p>


This PCB layout illustrates the integrated placement and routing of the Li-Po charging circuit, buck-boost regulator, battery protection stage, LED indicators, and the 5-V/3.3-V power-selection switch. High-current paths around the charger IC, inductor, and battery connector are kept short and wide, while sensitive nodes are routed with controlled spacing to reduce noise coupling. Decoupling capacitors are positioned tightly around their respective ICs, and the output header is placed along the bottom edge for straightforward connection to the system’s downstream electronics. The layout reflects a functional first-revision prototype optimized for electrical integrity, clarity, and ease of testing.



<p align="center">

  <img src="https://hackmd.io/_uploads/SypEzh2ZWx.png" alt="Screenshot 2025-12-02 103627">

  <br>
  Figure 9. 3D PCB Rendering

</p>


The 3D rendering in Figure 9 provides a visual representation of the assembled power subsystem PCB, illustrating the placement and orientation of major components including the Li-Po battery connector, charger IC, buck-boost converter, protection circuitry, LED indicators, and output header. This view highlights mechanical clearances, connector accessibility, and overall board organization, helping verify that component spacing, switch positions, and mounting-hole locations align with the mechanical requirements of the system enclosure. While electrically equivalent to the layout shown previously, the 3D model offers a clearer perspective on real-world form and fit prior to fabrication.




---


## Flowchart

<p align="center">
  <img src="https://hackmd.io/_uploads/HJyZVhzb-g.png" style="width:99%; border:1px #eee; padding:1px; margin:20px;">
  <br>
  <strong>Figure 10. Power Subsystem Flowchart</strong>
</p>


The flowchart in Figure 10 provides a high-level overview of the complete power subsystem architecture. USB-C input power is first delivered to the CC/CV charging unit, where the ETA9740 manages safe Li-ion charging and communicates charge status through its indicator outputs. Charging protection is reinforced by the battery’s onboard BMS, which monitors over-voltage, under-voltage, over-current, and over-temperature conditions. The single-cell Li-Po battery supplies a variable 2.75–4.2 V rail, which is converted to a regulated 3.3-V supply through the TPS63031 buck-boost converter. This regulated rail passes through an inline fuse before reaching the system’s main power switch, where the user or microcontroller can select between powering downstream loads or turning the system off entirely. The regulated output then supplies the ESP32 microcontroller, the IMU sensor, and the OLED display, each with their respective maximum current requirements. This flowchart summarizes how input power is conditioned, stored, regulated, protected, and distributed before the detailed circuitry in the following sections expands upon each functional block.




## BOM

As the schematic is presently defined, the selected components and overall architecture fully satisfy the established shall statements and functional requirements for the Power Subsystem. The design provides regulated 3.3-V power delivery with appropriate current capacity, incorporates integrated Li-Po charging and protection features, includes fault-tolerant elements such as ideal-diode ORing and resettable fusing, and maintains compatibility with all system-level electrical interfaces. The chosen components meet or exceed the electrical, thermal, and safety constraints identified during the conceptual and preliminary design phases. Although the design is currently complete and fully capable of supporting subsystem operation, testing, and integration, it remains subject to change as the project progresses; certain portions of the circuit—such as the optional 5-V rail and other “just-in-case” elements—may be removed, simplified, or replaced in later revisions depending on component availability, updated requirements, or insights gained during prototyping.

<p align="center">
  <strong>Table II. Simplified PCB Bill of Materials</strong>
</p>

| **Comment / Value** | **Designator(s)** | **LCSC Part #** | **Description** | **Order Qty** | **Total Cost** |
|---------------------|-------------------|------------------|------------------|----------------|----------------|
| 1kΩ | R2, R3, R9, R48 | C11702 | 1kΩ 1% resistor | 20 | $0.0120 |
| 10nF | C42 | C15195 | 10nF, 50V X7R capacitor | 20 | $0.0280 |
| 100nF | C1, C2, C12, C13, C43 | C1525 | 100nF, 16V X7R capacitor | 22 | $0.0264 |
| 100nF | C11 | C1525 | 100nF, 16V X7R | — | — |
| TPS63031DSK | U5 | C15516 | 3.3V 900mA buck-boost converter | 2 | $1.6800 |
| 10uF | C10 | C15525 | 10uF, 6.3V X5R | 20 | $0.0900 |
| 10uF (25V) | C5–C9 | C15850 | 10uF, 25V X5R | 20 | $0.1960 |
| 0Ω | R5 | C17168 | Jumper resistor | — | — |
| 0Ω | R12 | C17168 | Jumper resistor | — | — |
| 10uF | C3, C4 | C19702 | 10uF, 10V X5R | 20 | $0.1120 |
| 3A Fuse | F1 | C20810 | 3A resettable fuse | 6 | $0.4536 |
| 1.5A Fuse | FB1, FB2 | C20979 | 1.5A resettable fuse | 8 | $0.6448 |
| 1uH Inductor | L1 | C22447812 | Wirewound inductor | 6 | $0.3918 |
| 22Ω | R6, R10 | C25092 | 22Ω resistor | 20 | $0.0120 |
| 100kΩ | R32, R46 | C25741 | 100kΩ resistor | 20 | $0.0120 |
| 10kΩ | R47 | C25744 | 10kΩ resistor | 20 | $0.0120 |
| 56kΩ | R1 | C25796 | 56kΩ resistor | 20 | $0.0120 |
| 2.2kΩ | R11 | C25879 | 2.2kΩ resistor | 20 | $0.0120 |
| 5.1kΩ | R7, R8 | C25905 | 5.1kΩ resistor | 20 | $0.0100 |
| LM66100 | U4 | C2869734 | Ideal-diode controller | 5 | $0.6925 |
| 1.5uH | L2 | C356917 | 1.5uH Inductor | 20 | $0.1240 |
| Slide Switch | SW1 | C42377847 | SPDT slide switch | 7 | $1.0829 |
| PH-2.0 Connector | J4 | C47647 | 2-pin PH battery connector | 2 | $0.2850 |
| 2N7002PW | Q2 | C5224236 | N-MOSFET | 10 | $0.2220 |
| AON7403 | Q1 | C5310961 | Power MOSFET | 5 | $0.8685 |
| 22uF | C14 | C59461 | 22uF, 6.3V capacitor | 20 | $0.1740 |
| ESD Diode | D1 | C7496591 | 5V ESD protection | 20 | $0.3040 |
| FP6601Q | U2 | C86198 | USB controller (unused) | 5 | $0.5215 |
| Green LED | D2, D3, D4, D5, D7 | C965793 | Green indicator LED | 22 | $0.1474 |
| 5.1V Zener | D6 | C968648 | 5.1V Zener diode | 6 | $0.3750 |


<p align="center">
  <strong>Table III. Shopping BOM – Off-Board Components</strong>
</p>



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


To physically incorporate this protection into the system, the selected **700 mA OptiFuse MSC-700MA glass fuse [9]** will be installed using an **inline screw-type 5×20 mm fuse holder** (PNGKNYOCN brand). This holder includes **22 AWG pre-terminated leads** and a **flame-retardant nylon housing rated for up to 5 A**, providing mechanical robustness and safe handling. The fuse holder will be **soldered directly to the 3.3 V output header on the PCB**, with one lead connected to the regulated 3.3 V rail and the other routed to the system’s main power switch. This wiring configuration places the fuse **in series with all downstream loads**, ensuring that any overcurrent fault on the ESP32, OLED, or IMU will cause the fuse to open before damage propagates. The screw-cap style holder also allows **rapid fuse replacement** during testing or field use while maintaining secure electrical contact and strain relief.


---

### Reverse Polarity Protection  

A MOSFET configured as an ideal diode is used instead of a standard series diode to avoid the 0.6–0.7 V forward drop typical of silicon rectifiers. When the MOSFET gate is driven fully on, its low Rds(on) produces only a small voltage drop—typically on the order of a few tens of millivolts at the design current—preserving regulator headroom, maintaining output regulation during Wi-Fi bursts, and improving overall efficiency. 

Figure 11 below from [17] illustrates the typical relationship between MOSFET gate-source voltage and Rds(on). At the design gate drive, the selected MOSFET operates in its low-resistance region, resulting in only tens of millivolts of drop while the drain current is small such as the 0.44 A peak, compared to ~0.6–0.7 V for a silicon diode.

<p align="center">
  <img src="https://hackmd.io/_uploads/BJINgLWW-g.png" alt="Screenshot 2025-11-23 212437">
  <br>
  Figure 11. Drain-to-source on-resistance vs. gate voltage and drain current
</p>





For comparison, a transient-voltage-suppression (TVS) diode could be added in parallel with the input rails to clamp fast surge or ESD events, but a TVS device does not prevent reverse-battery connection and usually adds more capacitance and leakage than desired in a small wearable [18]. The MOSFET-based ideal-diode configuration directly blocks reverse-polarity faults with minimal series loss, while an optional TVS diode can be reserved for additional surge/ESD robustness if testing shows it is necessary. This combination aligns with safety requirements by preventing accidental battery-reversal damage while still protecting against abnormal transient events.




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
Peak currents of ~400 mA are easily supported by AWS 26 silicone-insulated conductors, which can carry well over 1 A in chassis wiring applications. This provides compliance with NEC/NESC requirements for insulation, ampacity, and conductor safety [2], [19]. In particular, the wire gauges in this design are selected directly from the expected peak and continuous current in each path, rather than from generic layout rules. Combined with the fuse and strain-relief routing, the wiring meets both electrical and mechanical safety expectations.

On the PCB, the main 3.3 V and battery traces are sized using industry-standard trace-ampacity guidelines for **1 oz/ft² copper** outer layers. For the expected **240–440 mA** operating range and a target temperature rise of **≤ 10 °C**, these guidelines indicate required trace widths on the order of **5–10 mil**, as verified using an online IPC-2221 trace-width calculator [20], [21]. Thus, the PCB trace widths are explicitly back-calculated from the anticipated current flow and the allowable temperature rise, rather than chosen arbitrarily. These tools determine the minimum width needed for a given current, allowable temperature rise, and copper thickness.

To ensure full compliance even under worst-case conditions, higher-current portions of the design were also evaluated. The USB-C input path and battery-charging path may experience currents approaching **3 A** during fast-charge operation. Using IPC-2221 equations with **1 oz copper**, **10 °C temperature rise**, **1-inch trace length**, and **room-temperature ambient** [21], the required trace widths are:

- **Internal layers:** 3.56 mm  
- **External layers:** 1.37 mm  


The external-layer requirement is significantly smaller because exposed copper dissipates heat more effectively through convection and radiation. These computed widths establish the minimum safe geometry for the high-current charging path to prevent overheating or copper fatigue during continuous use.

For the regulated **3.3 V output rail**, the worst-case current is inherently limited by the inline **700 mA fuse**, which intentionally opens before PCB traces are thermally overstressed. Additionally, the TPS63031 buck-boost converter used in this design can supply a maximum of **800 mA**, which represents the absolute upper bound the rail could ever see. Re-evaluating the trace-ampacity requirements under the same parameters but with **I = 0.8 A** yields:

- **Internal layers:** 0.574 mm  
- **External layers:** 0.221 mm  

Here again, the trace geometries are sized directly from the maximum possible 3.3 V rail current and the chosen 10 °C temperature-rise limit.

These dimensions are readily achievable on standard 2-layer and 4-layer PCB stackups and provide substantial thermal and electrical margin. Designing traces such that the fuse—not the PCB—fails under a fault condition ensures compliance with NEC/NESC overcurrent-protection expectations and prevents localized PCB hot spots or conductor burnout.


## Battery Management System (BMS) Operation Analysis

A 1-cell lithium-polymer battery typically includes an integrated protection circuit module (PCM), commonly referred to as the BMS. This small circuit provides several essential safety functions by combining a dedicated protection IC with back-to-back MOSFET switches and a current-sense resistor. In a typical 1S Li-Po pack, the protection IC (such as the DW01 or Seiko S-8261 family [22]) continuously monitors the cell voltage and battery current while controlling the gate of the MOSFET pair. These MOSFETs act as an electronic switch, allowing the BMS to instantly disconnect the battery from the load or charger whenever a fault condition is detected. The sense resistor (R<sub>sense</sub>) produces a millivolt-scale signal proportional to battery current, enabling the protection IC to compare this voltage against programmed OCP thresholds.

During **over-voltage protection (OV)**, the protection IC disconnects the charge MOSFET once the cell approaches approximately 4.25–4.30 V, preventing lithium plating and unsafe charging conditions. After the voltage drops slightly due to hysteresis, the MOSFETs automatically reconnect. In **under-voltage protection (UV)**, the BMS disables the discharge MOSFET whenever the cell voltage falls to roughly 2.5–2.7 V. This prevents deep discharge, copper dissolution, and permanent damage to the battery; the pack remains disconnected until an external charger restores the cell to a safe voltage.

For **over-current protection (OCP)** and short-circuit events, the BMS monitors the voltage across R<sub>sense</sub>. If this voltage exceeds an internal threshold—typically corresponding to 3–5 A for a 1S Li-Po pack—the protection IC rapidly opens the MOSFETs within microseconds, isolating the battery from the fault. Some protection PCMs also include **over-temperature protection (OTP)** using an NTC thermistor embedded in the pack. If the cell becomes too cold (below about 0 °C) or too hot (above ~60 °C), charging and/or discharging is disabled to keep the cell within its safe operating limits.

In the context of this subsystem, the BMS ensures that the Li-Po cell cannot be overcharged, over-discharged, subjected to excessive current, or operated outside safe thermal limits. Since the visor’s peak design current of approximately 0.44 A is far below the BMS’s OCP threshold, normal operation will never trigger protective shutdown. The ETA9740 charger performs CC/CV charging and power conversion, while the BMS enforces cell-level electrical safety. Together, these protections support compliance with IEC 62133-2 requirements and provide a reliable hardware safety foundation for the Power Subsystem [1].



---

### Compliance with Power Subsystem Shall Statements  
Rather than treating the shall requirements as a separate analysis, each of the quantitative and qualitative results above directly corresponds to a specific subsystem requirement. The use of a **4000-mAh 1S Li-Po battery**, combined with the measured power budget and efficiency analysis, demonstrates compliance with the **≥6-hour operation requirement**. The regulated **3.3-V ±5% output** is maintained throughout both nominal and peak load conditions. NEC/NESC grounding, conductor sizing, and overcurrent protection requirements are satisfied through the AWG 26 wiring selection, fused output rail, and MOSFET-based reverse polarity protection.

Battery protection requirements (OV, UV, OCP, OTP) defined by IEC 62133-2 are inherently satisfied by the **ETA9740’s integrated BMS features** and the BMS in the LiPo battery housing. The CC/CV charging behavior required for USB-C input is also executed by the ETA9740, ensuring safe charging profiles, adherence to C-rate limits, and temperature-dependent charge inhibition. Furthermore, the thermal and efficiency analyses show that surface temperatures remain well below **41 °C**, the converters exceed their **≥90% efficiency** targets under many operating points, and conservative margin calculations validate that the design maintains safe, reliable performance with appropriate derating.

Collectively, these analyses confirm that the Power Subsystem fully satisfies all defined shall statements and meets the functional, safety, and performance requirements of the Smart Golf Visor [23].


---

## References


[1] “IEC 62133: Safety Testing for lithium ion batteries,” Intertek, duplicate listing used for safety context. [Online]. Available: https://www.intertek.com/batteries/iec-62133/ (accessed Oct. 14, 2025). 

[2] W. T. W. Guy, “The Ultimate Guide to Wire Ampacity and NEC Basics,” Wire & Cable Your Way Blog, https://blog.wireandcableyourway.com/the-ultimate-guide-to-wire-ampacity-and-nec-basics (accessed Oct. 27, 2025). 

[3] “IEC 62301:2011 – Household electrical appliances – Measurement of standby power,” IEC. [Online]. Available: https://webstore.iec.ch/en/publication/6789 (accessed Oct. 24, 2025). 

[4] “IEC 62301:2011,” IEC, https://webstore.iec.ch/en/publication/6789 (accessed Oct. 24, 2025). 

[5] Eta Semiconductor, “ETA9740 Charger/Booster Circuit,” Hardware Design Guide, Version 1.1. 

[6] Texas Instruments, “TPS63031: High-Efficiency Buck-Boost Converter,” Datasheet SLVS696D, Apr. 2020.

[7] “CC and CV charging of Lithium Polymer Battery,” Lithium_Polymer_Battery_net, https://www.lithium-polymer-battery.net/cc-and-cv-charging-of-lithium-polymer-battery/ (accessed Oct. 17, 2025). 

[8] E-Switch, “RR5 Series Rocker Switch Specifications,” Datasheet, Nov. 16, 2021. 

[9] MSC Corporation, “700 mA Fast-Acting Fuse – Product Specification,” Component Datasheet. 

[10] “ESD protection basics with TVS Diodes,” Altium, https://resources.altium.com/p/esd-protection-basics-tvs-diodes (accessed Dec. 2, 2025). 

[11] Texas Instruments, “LM66100: 5.5-V, 1.5-A Ideal Diode with Input Polarity Protection,” Datasheet SLVSEZ8A, Jun. 2019. 

[12] Espressif Systems, “ESP32-S3 Series Datasheet v2.0,” Jul. 2023. 

[13] Solomon Systech, “SSD1309 OLED/PLED Segment/Common Driver with Controller,” Datasheet, 2010. 

[14] Waveshare Electronics, “1.51-inch OLED Transparent Driver Board – Schematic,” Version 1.0. 

[15] Richtek Technology Corporation, “RT9193: 300 mA Ultra-Low-Noise LDO Regulator,” Datasheet DS9193-18, Jun. 2022. 

[16] TDK InvenSense, “ICM-20948: World’s Lowest Power 9-Axis MEMS MotionTracking Device,” Datasheet DS-000189, Rev. 1.3, Jun. 2017. 

[17] ON Semiconductor, “FDD24AN06L-F085 N-Channel Power MOSFET,” Datasheet, 2018. 

[18] A. Godbole and D. Jain, “Going TVS-less in Automotive Reverse Battery Protection Designs,” Ti, https://www.ti.com/lit/an/slyt818/slyt818.pdf?ts=1751611828493 (accessed Dec. 3, 2025). 

[19] Consolidated Electronic Wire & Cable, “26 AWG Wire – Product Specification #30-00695,” Technical Data Sheet. 

[20] “Trace width calculator,” AdvancedPCB, https://www.advancedpcb.com/en-us/tools/trace-width-calculator/ (accessed Dec. 2, 2025). 

[21] “IPC-2221: The standard for printed circuit board design,” NextPCB, https://www.nextpcb.com/blog/ipc-2221 (accessed Dec. 2, 2025). 

[22] Xysemi, “DW01 Lithium-Ion/Polymer Battery Protection IC,” Reference Datasheet. 

[23] IEEE, “IEEE code of ethics | IEEE,” IEEE Advancing Technology for Humanity, https://www.ieee.org/about/corporate/governance/p7-8 (accessed Sep. 18, 2025). 
