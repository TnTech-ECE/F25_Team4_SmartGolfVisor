# Conceptual Design
## Introduction

The Smart Golf Visor is a wearable device designed to deliver real-time feedback of key golf shot metrics (ball speed, ball distance, launch angle, spin rate, and smash factor) along with a visual indication of shot trajectory to golfers. The primary goal of the Smart Golf Visor is to help golfers of all skill levels improve their performance by allowing them to quickly assess their swing based on these data metrics. This information is displayed hands-free and in real-time, eliminating the need to check external devices after each shot and maintaining the pace of play. 

This report restates the fully formulated problem by outlining the overall objective and scope for this project, fully decomposes the conceptual solution developed by the Smart Golf Visor team, identifies any additional constraints found, provides specifications for each of the Smart Golf Visor's subsystems, and addresses how the team will accomplish their goals given the available resources.


## Restating the Fully Formulated Problem 

The Smart Golf Visor gives golfers a visual display of real-time shot metrics onto eyewear. This system includes a rechargeable powering system, a custom-built launch monitor, an app to store the shot metrics, and a hands-free interface that requires only the placement and repositioning of the launch monitor. To ensure the product meets functional and user-centered requirements, the following are a set of specifications and constraints based around our stakeholder Matt Manzaro, a TTU Golf Pro with teaching experience, and his interest in the following capabilities:

1) The data metrics that shall be displayed are ball speed, ball distance, launch angle, spin rate, and smash factor. These are the most important feedback for golfers to improve their game.
2) The data shall be processed using an analytics algorithm that evaluates each shot and provides suggestions for the golfer's improvement. After a round, the golfer will be able to review data from each shot.
3) The battery shall last for a minimum of 9 holes, but ideally more than 18 holes. On average, a 9-hole round takes around 2 hours to complete.
4) The system shall be portable to allow golfers to use for training or match play.
5) The launch monitor shall be able to wirelessly communicate shot metrics to the app.

In addition to these customer-defined specifications, the product must also abide by some the following standards:
1) Any Bluetooth-based wireless transfer of information shall comply with the IEEE 802.15.1 standard that defines Personal Area Network (PAN) devices. Class 2 devices are characterized by approximately a 10 meter radius and a maximum transmit power of +4dBm (approximately 2.5 mW).  Wireless transmission shall operate within a 2.402 GHz to 2.480 GHz frequency band.
2) The use of a Lithium Ion or Lithium Polymer cells as part of a battery unit shall comply with IEC 62133 safety regulations to ensure safe operation within extreme Conditions. The battery shall operate safely within 0–60 °C and include protection against electrical faults such as overcharge at 1.4× rated charge current, over-discharge below 2.5 V per cell, and short-circuit currents up to the rated maximum.
3) Any image projection onto the lenses or glass shall comply with the ISO 15004-2 eye safety standard that determines the group hazard level of optical radiation within wavelength range of 250 nm to 2500 nm. For continuous operation over the duration of a golf round (~4 hours), retinal and corneal irradiance shall remain below the following approximate limits:

    A. UV (250–400 nm): ≤ 0.021 mW/cm²

    B. Visible (400–700 nm): ≤ 0.069 mW/cm²

    C. Near-IR (700–1400 nm): ≤ 1.04 mW/cm²

    D. IR (1400–2500 nm): ≤ 3.47 mW/cm²

    These limits account for exposure duration, wavelength, and illuminated area of the eye, ensuring safe operation during normal use.
4) The electronic components within the visor shall be IP-67 Rated providing dirt protection for objects greater than 1mm diameter and water protection for depths of 15 centimeters to 1 meter.

In addition to the industry standards, the following constraints address the safety and usability:
1) The display shall not obstruct the view of the golfer to ensure safety and improved performance.
2) The display shall be capable of maintaining a clear and readable display.

## Comparative Analysis of Potential Solutions

This section evaluates multiple feasible solution paths, identifies design trade-offs, defines decision criteria, and justifies the final selection based on stakeholder needs, specifications, and constraints.

### 1. Solution Space Overview
The team assessed two key decision areas: the **data transmission path** (launch monitor → processing → display) and the **near-eye display architecture**(NDA). Both influence latency, power, and integration complexity.

#### End-to-End Data Path Architectures
**Option A — Visor-Only Link:** The visor’s onboard microcontroller unit (MCU) directly pairs with the launch monitor using Bluetooth Low Energy (BLE). It parses incoming data packets and renders heads-up display (HUD) metrics locally, reducing dependency on external devices but increasing visor power use.

**Option B — Smartphone Bridge (Selected):** A mobile app connects to the launch monitor (BLE or Transmission Control Protocol—TCP), normalizes shot metrics (ball speed, carry distance, launch angle, spin rate, and smash factor), and transmits only essential metrics to the visor. This reduces visor complexity, cost, and power draw while improving maintainability.

**Option C — Cloud Relay:** Data routes through the cloud before reaching the visor. While simplifying local pairing, this introduces latency and requires network access.

**Option D — Fully Embedded Vision System:** The visor integrates onboard sensing and processing. Though autonomous, this design carries higher power, weight, and development risk.

#### Near-Eye Display Approaches
**Option A — Off-the-Shelf Augmented Reality (AR) Glasses:** The app renders the HUD as video output to AR glasses (USB-C/HDMI). This option is proven and simple but limits customization and ergonomics.

**Option B — Custom Dual Micro Organic Light-Emitting Diode (Micro-OLED) + MCU (Selected):** Custom visor using Micro-OLED displays driven by an ESP32-class MCU, allowing precise optical control, minimal occlusion, and low latency.

**Option C — Waveguide Module:** Compact optics with excellent sunlight legibility but has high integration complexity, and a high pricing.

**Option D — See-Through Thin-Film Transistor/Liquid Crystal on Silicon (TFT/LCOS):** Heavier, higher power, and slower response, making it less suitable for the visor design.

### 2. Decision Criteria and Weights
1. End-to-end latency (25%) — HUD response ≤ 500 milliseconds 
2. Safety and view occlusion (15%) — unobstructed sightlines; ISO 15004-2 compliance.
3. Integration complexity and schedule risk (20%) — dependency on vendor interfaces.
4. Power/runtime (15%) — ≥ 9 holes (target 18).
5. Cost realism (15%) — within COTS budget.
6. Standards compliance (10%) — BLE PAN, IEC 62133.

Weighting Rationale
These percentages reflect each factor’s impact on performance, safety, and feasibility. End-to-end latency (25%) is weighted highest since fast HUD response is vital for real-time data display. Integration risk (20%) follows due to dependence on vendor interfaces. Safety and view occlusion (15%) ensure ISO 15004-2 compliance and clear sightlines. Power/runtime (15%) supports at least a 9-hole round. Cost realism (15%) keeps the design within the COTS budget, while standards compliance (10%) maintains adherence to BLE PAN and IEC 62133 requirements.

3. Architecture Trade Study
### 3. Architecture Trade Study  
| **Opt.** | **Lat.** | **Occl.** | **Compl.** | **Power** | **Cost** | **Std.** | **Total** |
|:--|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
| **A – Visor-only Link** | 4 | 4 | 2 | 2 | 3 | 4 | 3.15 |
| **B – Smartphone Bridge** | 4 | 4 | 4 | 4 | 4 | 4 | **4.00** |
| **C – Cloud Relay** | 2 | 4 | 3 | 3 | 3 | 4 | 3.00 |
| **D – Fully Embedded** | 5 | 3 | 1 | 1 | 2 | 4 | 2.75 |

Result: Option B — Smartphone Bridge ranks highest, balancing latency, efficiency, cost, and integration simplicity.

4. Display Trade Study (Qualitative Summary)

Table X. Display Options Evaluation (Numeric Rating 1–5)
Scale: 5 = Excellent / Very High / Optimized • 1 = Poor / Very Low / Not Preferred

| **Opt.** | **Daylight** | **Lat.** | **Risk** | **Comfort** | **Cost** | **Verdict** |
|:--|:--:|:--:|:--:|:--:|:--:|:--|
| **E – Off-Shelf AR** | 4 | 5 | 5 | 3 | 4 | **4 – Feasible, limited control** |
| **F – Custom OLED** | 5 | 5 | 3 | 5 | 4 | **5 – Selected** |
| **G – Waveguide** | 5 | 4 | 2 | 5 | 3 | **4 – Backup option** |
| **H – TFT/LCOS** | 2 | 4 | 3 | 2 | 2 | **2 – Not preferred** |

Result: Option F — Custom Micro-OLED Visor best supports clarity, latency, ergonomic comfort, and cost balance.

### 5. Key Design Considerations
- **Human Factors:** Minimal occlusion, adjustable brightness, and clean HUD layout to reduce distraction.
- **Latency:** Maintain total system latency below 500 ms.
- **Energy Efficiency:** Phone handles compute-heavy tasks to reduce visor power draw.
- **Compliance:** Meets IEEE 802.15.1, IEC 62133, ISO 15004-2.
- **Vendor Modularity:** Smartphone bridge supports future multi-monitor integration.

### 6. Chosen Solution and Justification
**Selected Solution:** *Option B + Option F — Smartphone Bridge with Custom Micro-OLED Visor.*
- **Metrics (Spec #1):** Normalizes and transmits key metrics for high-speed, crisp HUD display.
- **Data Review (Spec #2):** App supports post-round analysis and coaching.
- **Runtime (Spec #3):** Efficient OLEDs and offloaded computation meet runtime targets.
- **Portability (Spec #4):** Lightweight, compact visor with mobile app integration.
- **Wireless Communications (Spec #5):** BLE and TCP compliant with PAN standards.

**Risks and Mitigations:**

Vendor Protocol Changes: These could cause data ingestion failure; this risk is mitigated by maintaining adaptable API layers, performing regression testing, and ensuring backward compatibility.

Daylight Washout: This could reduce readability; it is mitigated by using a high-contrast HUD theme and adaptive brightness.

Cable Snags: These could cause safety hazards; they are mitigated by using magnetic breakaway connectors and strain-relieved routing.

Bluetooth Low Energy (BLE) Interference: This could cause packet loss or latency; it is mitigated through dynamic channel tuning and automatic reconnection.

Battery Aging: This could shorten runtime; it is mitigated by using high-capacity cells and replaceable battery options.

Summary: The smartphone bridge and custom OLED visor design offer optimal performance, usability, and safety while maintaining cost-efficiency.

---




## High-Level Solution

This section will discuss the high-level solution that the Smart Golf Visor team has created from the specifications given by their customer Matt Manzaro and adhering to all constraints .

The Smart Golf Visor system is designed to efficiently meet all specified requirements while maximizing user performance, safety, and usability. The system consists of two primary components: a pair of lightweight smart glasses and a compact, portable launch monitor. Together, these components form a mobile training solution that golfers can use during both practice sessions and match play.

The launch monitor captures critical shot data metrics—including ball speed, ball distance, launch angle, spin rate, and smash factor—in real time. These measurements are wirelessly transmitted through the monitor’s API using Bluetooth communication, ensuring compliance with the specification requiring wireless transmission of shot metrics to an app, as well as with the IEEE 802.15.1 standard that short-range data Wireless transmission shall operate within a 2.402 GHz to 2.480 GHz frequency band.

The web-based application receives and processes this data, enabling post-round review and analysis. It also generates personalized coaching suggestions derived from player performance trends, fulfilling the specification to provide improvement feedback and data review capabilities after each round.

Processed data and coaching feedback are then sent via Bluetooth to a microcontroller embedded in the visor. The microcontroller integrates this information into a Heads-Up Display (HUD) interface, which is projected onto a micro-OLED display inside the glasses. This allows the golfer to view real-time performance feedback without diverting attention from play. The display design maintains a clear and unobstructed field of view in compliance with ISO 15004-2 eye safety standards, an international standard that defines optical and radiance safety requirements for ophthalmic devices, ensuring safe and comfortable operation.

The visor system is powered by a rechargeable Lithium Polymer (Li-Po) battery, sized to last at least nine holes of play but ideally 18 holes, satisfying the battery life specification and adhering to IEC 62133 safety regulations, an international standard specifying safety requirements for rechargeable battery cells and packs. These include protections against overcharging, short-circuiting, overheating, and excessive current draw, as well as requirements for mechanical integrity and safe operation under normal and fault conditions.

All electronic components are housed within an IP67-rated enclosure, which provides complete protection against dust ingress and safeguards against water immersion up to 1 meter for 30 minutes. This ensures reliable outdoor performance and durability across various weather conditions.

Overall, the Smart Golf Visor design optimizes real-time data acquisition, processing efficiency, and power management to deliver a seamless and safe user experience. By adhering to relevant industry standards, maintaining a lightweight ergonomic design, and focusing on actionable feedback through intelligent data integration, the system effectively enhances usability, ensures safety, maximizes stakeholder value, and improves the quality of life (in terms of the game) when playing golf.


### Hardware Block Diagram
<p align="center">
 <img style="display:block;margin:20px right;padding:1px;border:1px #eee;width:900%;" src="https://hackmd.io/_uploads/rkgZRCP0Cxl.png">
</p> 

<p align= "center"> 
  Figure 1: Smart Golf Visor Hardware Block Diagram 
</p>

### Operational Flow Chart


<p align="center">
  <img style="display:block;margin:20px right;padding:1px;border:1px #eee;width:900%;" src="https://hackmd.io/_uploads/B1eox-XRgx.png" />
  </p> 

<p align="center">
  Figure 2: User Operational Flow Chart
  </p>





## Atomic Subsystem Specifications

The Smart Golf Visor is composed of six primary subsystems: Launch Monitor, Communications, Physical Visor Design, Visor Powering System, Heads-Up Display, and App/Software Development.

### Subsystem 1: Launch Monitor
**Subsystem Overview and Function**

This subsystem acquires per-shot telemetry from the PiTrac DIY launch monitor [19] and delivers a normalized shot record to the application and the visor heads-up display (HUD) with sub-second latency. The PiTrac launch monitor runs on a Raspberry Pi and uses two Mobile Industry Processor Interface (MIPI) global-shutter cameras with infrared (IR) strobing to capture the golf ball’s motion. Using an existing launch-monitor plan with accessible data, the system avoids redesigning. This will allow a sensing pipeline, and will rely on PiTrac’s published interfaces to expose a single, versioned message contract to downstream components. The bridge process can receive telemetry through the PiTrac web server’s WebSocket (WS) and Representational State Transfer (REST) interfaces, through a Transmission Control Protocol (TCP) socket that carries Golf Simulator Pro (GSPro) Open Connect JavaScript Object Notation (JSON) messages, or through an internal ActiveMQ message topic. The bridge validates and time-orders all incoming events, converts values to the International System of Units (SI) internally, and publishes a stable Shot record to the HUD.

**Subsystem Operation**

At the start of a session, the bridge selects an ingress method—WebSocket and REST, Open Connect over TCP, verifies the handshake, and transitions to an armed state. When a strike occurs, PiTrac emits one or more shot messages, which the bridge parses and validates. The bridge converts all values to SI units, assigns both a monotonic timestamp and a UTC timestamp, and collapses duplicates within a brief debounce window so that a single, deterministic Shot record represents the swing. The bridge then emits the Shot record on the local WebSocket, and the HUD renders a fixed, high-contrast layout on the visor within the latency budget. Throughout the session, the subsystem monitors heartbeats, records any drops, and reconnects automatically with back-off. At the end of the session, the bridge closes connections, flushes logs, and produces a concise summary for later review.
**Primary Functions**

The subsystem establishes and maintains a reliable data link to PiTrac using either WebSocket and REST, or GSPro Open Connect over TCP.

The subsystem parses each incoming message and normalizes the fields into a common Shot schema that includes, ball speed, ball distance, launch angle, spin rate, and smash factor along with a visual indication of shot trajectory to golfers.

The subsystem applies bounds checks, converts all values to SI units, and assigns two timestamps—one from a monotonic clock for ordering and latency measurement, and one in Coordinated Universal Time (UTC) for logging and correlation.

The subsystem guarantees that each swing produces exactly one Shot record by performing short-window de-duplication of near-identical messages.

The subsystem publishes normalized Shot records to the visor HUD over a local WebSocket using JSON, and it targets an end-to-end latency of no more than 500 milliseconds at the ninety-fifth percentile.

The subsystem monitors liveness through socket heartbeats, performs automatic reconnection with exponential back-off after faults, and writes rotating diagnostic logs without blocking the data path.

**Platform Options (Bridge Host)**

The bridge may run as an application that terminates the PiTrac stream, provides the local WebSocket to the HUD, and drives the augmented-reality glasses over a wired video link. Alternatively, the bridge may run as an embedded Linux service on a small computer that ingests WebSocket or TCP traffic and republishes normalized Shot records to any HUD client over the local network or an inter-process communication channel.

**Interfacing (Conceptual)**

The PiTrac system sends real-time shot events to the bridge over WebSocket, while REST endpoints provide configuration and history when needed. The GSPro Open Connect path allows the bridge to listen on a TCP port for JSON messages labeled “Ready,” “Shot,” and “Heartbeat,” which are identical to the messages a simulator would receive. After ingest, the bridge publishes a compact, versioned Shot record to the HUD over a local WebSocket. Heartbeats and reconnect logic maintain the link, and rotating log files record raw messages, normalized records, and timing metrics for later analysis.


**Performance Targets and Constraints**

The subsystem uses WebSocket and REST, GSPro Open Connect over TCP, to transport telemetry, and it produces one deterministic Shot record per swing. The time from impact to visible HUD update shall not exceed 500 milliseconds at the ninety-fifth percentile, with ingest and normalization completing within approximately 50 milliseconds and publication to paint on the visor completing within approximately 100 milliseconds under nominal conditions. The subsystem must automatically reconnect after link loss, must not freeze the user interface, and must maintain ordering using a monotonic clock.

**Subsystem “Shall” Statements**

1. The subsystem shall acquire per-shot telemetry from PiTrac using WebSocket and REST, or GSPro Open Connect over TCP.

2. The subsystem shall normalize all inputs to a single Shot schema, shall convert all quantities to SI units, and shall assign both a monotonic timestamp and a UTC timestamp to each record.

3. The subsystem shall ensure one record per swing by de-duplicating near-identical messages within a bounded debounce window.

4. The subsystem shall publish normalized Shot records to the visor HUD through a local WebSocket using a versioned JSON contract.

5. The subsystem shall meet a ninety-fifth-percentile end-to-end latency of no more than 500 milliseconds from impact to on-visor render under nominal conditions.

6. The subsystem shall automatically detect link loss, shall reconnect with exponential back-off, and shall continue operation without user intervention.

7. The subsystem shall record rotating diagnostic logs of raw messages, normalized records, timing measurements, and reconnect events for testing and analysis.

8. The subsystem shall protect all stored credentials and keys using secure storage provided by the operating system.

 <p align="center"> Table I </p>
<p align="center">Launch Monitor and Interfacing</p>


| Interface               | Connected Subsystem         | Type    | Direction | Protocol / Standard                    | Description                                                                                           |
|-------------------------|-----------------------------|---------|-----------|-----------------------------------------|-------------------------------------------------------------------------------------------------------|
| PiTrac WebSocket / REST | PiTrac ⇄ Bridge             | Data    | Input     | WebSocket (WS) / REST (HTTP + JSON)     | Receives real-time shot events over WS; uses REST endpoints for configuration and history as needed. |
| Open Connect Socket     | PiTrac / Connector ⇄ Bridge | Data    | Input     | TCP (GSPro Open Connect v1, JSON)       | Accepts JSON messages (`Ready`, `Shot`, `Heartbeat`) on a TCP port, identical to simulator feeds.     |
| Normalize & Commit      | Bridge (internal)           | Process | Output    | Vendor → Common Shot schema (SI units)  | Parses, validates, converts units, assigns monotonic/UTC timestamps, and de-duplicates per strike.    |
| Publish to HUD          | Bridge ⇄ App/HUD            | Data    | Output    | Local WebSocket (JSON)                  | Emits normalized Shot records to the HUD/app with a sub-second end-to-end latency target.            |
| HUD Render              | App/HUD ⇄ AR Glasses        | Video   | Output    | USB-C DisplayPort Alt-Mode or HDMI      | Renders a fixed, high-contrast HUD layout and outputs video to the AR glasses over a wired link.      |!




<p align="center">
  <img src="https://hackmd.io/_uploads/B1Up58KRxe.png" alt="subsystem flowchart">
</p>

<p align="center">Figure 3: Data Acquisition and Interface Flowchart </p>

### Subsystem 2: Communications
**Overview and Function**  
The communication subsystem transmits the measured data captured by the launch monitor to all relevant subsystems. It utilizes wireless bluetooth and serial communication to ensure reliable data transfer for accurate metric display and data storage for future analysis. This functionality **shall** require a microcontroller for proper communication between devices as well as additional hardware for each possible microcontroller.

**Subsystem Operation** 

**Hardware Overview**  
The communication hardware **shall** interact with the visor design as they will need to be placed in according to the other subsystem's hardware. 

**Launch Monitor**  
The launch monitor **shall** communicate with the app via a Bluetooth signal (2.4 GHz). If the selected model does not include built-in Bluetooth, an additional microcontroller with Bluetooth support, as listed below, shall be implemented to provide wireless connectivity

**Microcontroller**  
The following are possible microcontroller options: Raspberry Pi Zero W, ESP32-S3, ESP32-P4. 

The Raspberry Pi Zero W offers moderate performance with a single-core ARM11 processor, computing at speeds of 1 GHz. It includes built-in Bluetooth 4.1 (speeds up to 1 Mbps), SPI communication (SPI, I2C, and UART), and MIPI interfacing. However, it requires a full operating system (Linux), which increases software complexity and power consumption.

The ESP32-S3 has high performance featuring a dual-core 32-bit Xtensa processor capable of handling speeds up to 240 MHz. It includes serial communication (SPI, I2C, and UART) and built-in Bluetooth 5 Low Energy, allowing for faster speeds (up to 2 Mbps) and lower power consumption. However, the ESP32-S3 has no MIPI support which would require two TC358778X MIPI-to-Parallel Bridge ICs to interface with the displays. Additionally, the ESP32-S3 can run bare-metal firmware or a real-time operating system, eliminating the need for a full operating system. 

The ESP32-P4 has the highest performance capability of the three options due to its Dual-core 32-bit RISC-V processor capable for speeds up to 400 MHz. It has MIPI and serial communications to interact with the display hardware but does not include built-in Bluetooth. One option is the Raytac MDBT42 Bluetooth Low Energy (BLE) module interfacing with the ESP32-P4 via UART. UART is well-suited for this purpose, as BLE data rates are typically below 1 Mbps and can be reliably transmitted and processed using the ESP32-P4. However, the ESP32-P4-WIFI6 is a development board of that provides that BLE wireless capabilities which will reduce cost on extra hardware.

Based on the following considerations: voltage, Bluetooth version, peripheral support, power efficiency, and system integration complexity — the **ESP32-P4** is the most suitable choice for the communication subsystem. However, all provided microcontrollers are capable of being incorporated in the product.

<p align="center">Table II </p>
  <p align="center"> Interfaces and Signal Definition </p>

| Interface               | Connected Subsystem | Type                   | Direction | Protocol / Standard | Description                                                                      |
| ----------------------- | ------------------- | ---------------------- | --------- | ------------------- | -------------------------------------------------------------------------------- |
| **ESP32-P4**| Power System | Power| Input     | DC | Receives regulated 3.3 V to operate and up to 500 mA for wireless transmission.  |
| **Selected Launch Monitor** | App | Wireless Communication | Output | Bluetooth (2.4 GHz) | Sends selected data to the app.|
| **ESP32-P4**| HUD/Display| Serial Communication | Output | MIPI DSI | Sends HUD graphic to the Micro-OLED displays.|
| **ESP32-P4** | HUD/Display | Serial Communication | Input | SPI | Receives acceleration, angular velocity, and orientation data from IMU.|

#### Summary of “Shall” Statements

* The selected launch monitor, app, and visor  **shall** wireless communicate shot metrics via BLE (Bluetooth Low Energy) at 2.4 GHz.
* The microcontroller **shall** wirelessly receive the shot metrics via BLE (Bluetooth Low Energy) at 2.4 GHz.
* The microcontroller **shall** be supplied and operate with 3.3V power signal.
* The IMU **shall** use SPI serial protocol to interface with the selected microcontroller.
* The mini OLED display **shall** use MIPI DSI to interface with the selected microcontroller.
* All hardware **shall** be designed to integrate with the Physical Visor Design Subsystem for structural compatibility.

<p align="center">
  <img src="https://hackmd.io/_uploads/HkoFKbi0xx.png" alt="subsystem flowchart">
</p>

<p align="center">Figure 4: Communication Flowchart </p>

### Subsystem 3: Visor Design
 
Overview and Function
The visor subsystem provides a wearable heads-up display (HUD) that projects launch-monitor telemetry directly into the golfer’s field of view. It integrates optical, mechanical, and electronic components into a balanced, ergonomic platform that maintains safety, clarity, and comfort. The design ensures minimal visual obstruction, fast response time, and compliance with ISO 15004-2 optical safety standards.

Exploded Parts List (by Function)

Base Frame: Uvex Hypershock safety glasses — structural carrier for optics and mounts.

HUD Optical Engine: 0.49″ 1920×1080 Micro-OLED display, plano-convex collimating lens (20–25 mm focal length), and AR-coated beam-splitter prism (≈ 30R/70T) for bright, clear projection.

HUD Pod Housing: 3D-printed ABS/PC shell protecting optics and providing pitch/yaw adjustments.

Inertial Measurement Unit (IMU): 9-degree-of-freedom sensor for head-movement stabilization, interfaced via SPI with MCU.

Rear Electronics Sleeve: Houses Li-Po battery, BMS, MCU board, converters, and USB-C port; balances front optics.

Elastic Retention System: Dual-strap system distributing load evenly and ensuring stability.

Cabling and Guides: Strain-relieved, low-profile cables routed for safety and unobstructed vision.

Environmental Features: AR coatings, anti-glare film, and moisture-resistant padding.

### Mechanical / Interface Summary  

| **Part** | **Material** | **Mass (g)** | **Interfaces** |
|:--|:--|:--:|:--|
| **Frame clamp** | PA12 / CF-Nylon | ≤ 12 | Frame ↔ HUD |
| **HUD pod** | ABS / PC | ≤ 25 | OLED, IMU |
| **Rear sleeve** | Softshell + ABS | ≤ 110 | USB-C, straps |
| **Elastic straps** | Nylon elastic | ≤ 30 | Temples ↔ sleeve |
| **Cable set** | FFC / FPC | ≤ 10 | Sleeve ↔ HUD |

Fit and Ergonomics

The visor provides an adjustable eye relief ranging from 18 to 22 mm, allowing users to achieve optimal focus and comfort.

The field of view spans approximately 14–18°, with no more than 10% central occlusion to maintain a clear sightline.

The eyebox measures at least 8 × 6 mm, ensuring consistent visuals across typical head movements.

The maximum surface temperature remains below 41 °C even after 60 minutes of continuous operation, preventing user discomfort.

The balanced Y-split straps distribute weight evenly across the head, improving comfort and stability during motion.
Visor Design “Shall” Statements

The visor shall mount a monocular HUD on the right eye with adjustable eye relief and ≥ 80 % visibility.

The optical engine shall include a Micro-OLED, collimating lens, and AR-coated combiner.

The rear sleeve shall counterbalance the HUD and contain all electronics.

Cable routing shall avoid obstructing the golfer’s view.

External surfaces shall remain under 41 °C and be smooth and padded.

The visor shall meet IP5x water and dust resistance requirements.

Physical Layout Disclaimer
Designs and dimensions are conceptual and may be refined through physical prototyping and ergonomic validation.


### Subsystem 4: Visor Powering System


 #### Overview and Function
The **Power Subsystem** converts energy from a **single-cell LiPo (1S, 3.7 V nominal)** into clean, regulated rails for the **Compute/Bridge (ESP32-P4 @ 3.3 V)**, **IMU @ 3.3 V**, and the **OLED HUD**. It implements full protection and safe charging consistent with cited standards and is sized to overshoot worst-case field use (≥ 18 holes plus buffer, cold-weather derating per notes). Target cell capacity: **5 000–6 000 mAh**.

From the 1S pack, the subsystem generates:
- **3.3 V** (ESP32-P4, IMU, logic)
- **1.8 V** (display VDDI logic)
- **5.0 V** (USB accessories and/or 0.39″ OLED AVDD path)
- **Display analog rails** (option-dependent):
  - **0.39″ OLED:** **Vin  2.5 - 5.5V**, **VDDI = 1.65 - 1.95 V**
  - **0.49″ OLED:** **AVDD 5.3–5.5 V**, **AVEE −4 to −5.5 V**, **VDDI 1.65–1.95 V**; power ≈ **468 mW**

A **1S LiPo BMS / protection IC** manages **Over-Voltage (OV)**, **Under-Voltage (UV)**, **Over-Current (OCP)**, and **Over-Temperature (OTP)** Protections [2].  
A **USB-C charging path** (5 V input) provides **CC/CV (Constant Current/Constant Voltage)** charging of the 1S pack [3].  
This charging method is optimized for LiPo batteries and ensures that charging begins with a constant current until a set voltage is reached, after which the voltage is held constant while current tapers to safe cutoff levels.  
Power distribution includes an inline fuse to further prevent overcurrents.

**Primary functions:**
- Regulate and distribute **3.3 V**, **1.8 V**, **5 V**, and display analog rails.  
- Provide **charge management** (**CC/CV**) and **cell protection** (**OV, UV, OCP, OTP**).  
- Support **power-enable domains**.  
- Conform to **NEC (National Electrical Code)** and **NESC (National Electrical Safety Code)** standards for low-voltage and battery-powered electronics, including safe conductor sizing, insulation, and fault protection guidelines.  

*Note:* “1S” confirms the use of a **single LiPo cell in series**, ensuring all regulation is derived from the nominal 3.7 V pack.

---
#### Operation Description & Flow

1. **Startup / Power-On**  
   On button press or charger insertion, BMS validates cell voltage/temperature.  
   If OK, MCU enables required rails (3.3 V, 1.8 V; +/− display rails as needed).  
   *Note:* Follow OLED sequencing (VDDI/AVDD/AVEE timings) per vendor tables.

2. **Charging Mode (USB-C 5 V)**  
   1S charger applies **CC/CV (Constant Current / Constant Voltage)** with the BMS **Temprature gate** (charge inhibit < 0 °C or > 45 °C).  
   During the **Constant Current (CC)** phase, current is fixed while voltage rises gradually; once the battery reaches ~4.2 V, the **Constant Voltage (CV)** phase begins and current tapers until cutoff.  
   Charge current is set to cell **C-rate (0.5–1 C)** per datasheet, where “C” represents the charge/discharge rate relative to the battery capacity (e.g., 1 C = full charge in 1 hour, 0.5 C = full charge in 2 hours).

3. **User Interface**  
   - **Power button**  
   - **USB-C charge port**  
   - **LED indicators** for charging/full and fault status.

#### Power & Efficiency Assumptions

**Representative converter efficiencies (to be verified in detailed design):**
- **3.3 V buck-boost from 1S:** η ≈ 90–93 %.  
- **5.0 V boost from 1S:** η ≈ 90–93 %.  
- **+5.4 V boost (OLED AVDD):** η ≈ 88–92 %.  
- **−5.4 V inverting (OLED AVEE):** η ≈ 85–90 %.  
- **1.8 V regulator (VDDI):**  
  - **LDO from 3.3 V:** η ≈ 55 % (acceptable if I is low).  
  - **Buck from 1S:** η ≈ 90–93 % (if I > 150 mA).

**Display power:**
- **0.49″ OLED:** ≈ 468 mW at 90 Hz, 1800 nits (worst-case full white).  
- **0.39″ OLED:** assume ≈ 300–450 mW (similar conditions, use ≈ 400 mW for budget).

**IMU power:**  
- The **Inertial Measurement Unit (IMU)** operates from the **3.3 V rail** shared with the ESP32-P4.  
- Typical IMU current draw is **3–6 mA** during full 6-axis sampling and up to **10 mA peak** during data bursts.  
- This equates to approximately **15–33 mW** average consumption, which is negligible compared to the compute and display loads but should still be included in total current budgeting on the 3.3 V line.  
- Efficiency through the **3.3 V buck-boost** remains in the **90–93 % range**, so IMU conversion losses are minimal (< 2–3 mW).  
- To minimize drift and noise, the IMU supply will use **low-ripple 3.3 V filtering** (LC or ferrite bead isolation) consistent with sensor manufacturer guidance.


**Pack energy:**  
5 000–6 000 mAh @ 3.7 V → 18.5–22.2 Wh nominal.  
Include ≥ 20 % reserve and cold-weather derate for reliable 18-hole operation.


---
---
<p align="center">Table III </p>
   <p align="center"> Interfaces and Signal Definition </p>

| Interface | Connected Subsystem | Type | Direction | Protocol / Standard | Description |
|------------|--------------------|-------|------------|---------------------|--------------|
| **3.3 V Rail (buck-boost)** | ESP32-P4, IMU, logic | Power | Out | DC | Up to **600 mA** (headroom). Low ripple for RF/IMU. |
| **1.8 V Rail (LDO or buck)** | OLED VDDI | Power | Out | DC | **150–300 mA** budget (LDO if low I; buck if ≥150 mA). |
| **5.0 V Rail (boost)** | USB accessory / OLED AVDD (0.39″)| Power | Out | DC | Generated by dedicated boost. |
| **AVDD (+5.3–5.5 V)** | OLED analog (0.49″) | Power | Out | DC | Generated by dedicated boost. |
| **AVEE (−4 to −5.5 V)** | OLED analog (0.49″) | Power | Out | DC | Generated by inverting converter / charge-pump. |
| **USB-C Charge In (5 V)** | External charger | Power | In | USB-C | 5 V input up to **0.5 C – 1 C A** into 1S CC/CV charger. |
| **Charge / Full Indication** | BMS → MCU | Digital | Out | GPIO | Pack charging/full status. |
| **Power-Enable Lines** | MCU → rails | Digital | In | GPIO | Enables per-domain rails for low-power. |
| **Protection / Fault** | Power → MCU | Digital | Out | GPIO | **OV (Over-Voltage), UV (Under-Voltage), OCP (Over-Current), OTP (Over-Temperature)** asserted → safe shutdown. |

---




### Power Subsystem “Shall” Statements 

#### Architecture & Rails  
1. **Shall** use a single-cell (1S) LiPo, 3.7 V nominal, with a mAh capacity to last a minimum of 6 hours.  
2. **Shall** generate regulated rails of **3.3 V ±5 %**, **1.8 V ±5 %**, and the OLED analog rails:(Option Dependent)  
   - **0.39″:** Vin 2.5–5.5 V, VDDI 1.65–1.95 V.  
   - **0.49″:** AVDD 5.3–5.5 V, AVEE −4 to −5.5 V, VDDI 1.65–1.95 V.  
3. **Shall** comply with **NESC (National Electrical Safety Code)** and **NEC (National Electrical Code)** standards for low-voltage systems, grounding, overcurrent protection, and insulation.  
   - NESC ensures human/equipment safety for low-voltage DC installations.  
   - NEC ensures proper wiring, fusing, and component protection under rated current/temperature.

#### Protection & Safety  
4. Include **1S LiPo BMS** implementing **OV (Over-Voltage)**, **UV (Under-Voltage)**, **OCP (Over-Current)**, **OTP (Over-Temperature)**.  
5. Inline fuse ≥ 1.5× max steady-state current of expected maximum current.  
6. Inhibit charging below 0 °C / above 45 °C; shutdown discharge on UV.  
7. User-touchable surfaces shall not exceed **41 °C after 60 min @ 25 °C** ambient.  
8. **Shall** conform to **NEC Article 480 (Storage Batteries)** and relevant **NESC Part 1, Section 12 (Low-Voltage Systems)** guidelines for safe installation and protection against short-circuit or overheating hazards.

#### Charging & Input Power  
9. Accept **USB-C 5 V** input; perform **CC/CV (Constant Current / Constant Voltage)** charging per cell datasheet.  
10. Limit charge current to ≤ cell manufacturer’s recommended **C-rate (0.5–1 C)**, where **C** defines the rate of current relative to total capacity (e.g., 1 C = 6 000 mA for a 6 000 mAh cell).

#### Efficiency & Power Budgeting  
11. Converter efficiency targets: **3.3 V buck-boost ≥ 90 %**, **5 V boost ≥ 90 %**, **+5.4 V boost ≥ 88 %**, **−5.4 V inverting ≥ 85 %**, **1.8 V buck ≥ 90 %**.  
12. **Shall** include margin calculations accounting for converter inefficiency, battery derate, and safety headroom per **NESC** low-voltage best practices.

---
### Power Subsystem Flowchart
<p align="center"><img style="display:block;margin:20px right;padding:1px;border:1px #eee;width:99%;" src="https://hackmd.io/_uploads/Byrf7_nRgx.png)" /></p> 

<p align="center">Figure 6: Power Subsystem Flowchart</p>




### Subsystem 5: App/Software

#### Subsystem Overview  
The **Application Subsystem** serves as the user interface and data management hub for the Smart Golf Visor system. It enables golfers to view real-time and historical performance data, receive automated coaching insights, and maintain connectivity between the launch monitor, visor hardware, and cloud-based data storage. The subsystem is designed for compatibility across mobile and desktop browsers, optimized for smartphones for use on the course. The ideal way to accomplish this is with a web application written in Python and hosted via Streamlit Cloud. Streamlit Cloud offers a Community Cloud, which allows users to host web applications from a Github repository. These applications can then be accessed from any internet-enabled mobile device. Streamlit apps use Hypertext Transfer Protocol Secure (HTTPS) for an encrypted connection between the web app and the user's browser. 


#### Primary Functions  

1. **Provide an intuitive user interface** to display the key performance metrics of ball speed, carry distance, launch angle, spin rate, and smash factor.  
2. **Integrate Bluetooth connectivity** to communicate with both the launch monitor and the microcontroller-based visor module.  
3. **Process and store shot data** in a secure cloud database to allow session tracking and post-round review.  
4. **Generate automated coaching insights** using processed shot data to suggest performance adjustments.  
5. **Transmit numerical metrics** to the visor for real-time display.  

---

#### Subsystem Operation  
When a user accesses the application, they are presented with a mobile-friendly dashboard interface featuring key options:
- **Start New Session** – Begin receiving metrics from the launch monitor and sending to the visor.  
- **View History** – Loads past session data and statistical summaries from the cloud database.  
    - **Performance Insights** – Displays trend analysis and coaching tips derived from session metrics.  

During an active session, incoming data packets from the launch monitor are processed in real-time. The app parses and filters metrics, which are then both:
- Stored securely in the cloud database, and  
- Streamed via Bluetooth to the visor microcontroller for HUD viewing.  

Upon completion of a session, users can later review session metrics and suggested practice focus areas.

---

#### Interfacing

<p align="center">Table IV</p>  
<p align="center">Application Interfaces with Other Subsystems</p>

| Interface          | Connected Subsystems | Type                   | Dir.   | Protocol / Standard        | Description                                                                                                                                                                                        |
| ------------------ | ------------------- | ---------------------- | ------ | -------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Launch Monitor** | Launch Monitor -> Communications -> Application             | Wireless Communication | Input  | Bluetooth Low Energy (BLE) | Receives real-time numerical shot data: ball speed, carry distance, launch angle, spin rate, and smash factor. Sends handshake to establish data streaming.                                        |
| **Smart Visor**    | Application -> Communications -> Smart Visor| Wireless Communication | Output | Bluetooth Low Energy (BLE) | Sends numerical summary metrics for current swing: ball speed, carry distance, launch angle, spin rate, and smash factor. Receives status acknowledgment or readiness signal from microcontroller. |

---

#### Application Subsystem "Shall" Statements
1.  The application **shall** display five key performance metrics per shot: ball speed, carry distance, launch angle, spin rate, and smash factor.  
2.  The application **shall** provide visual performance metrics and coaching insights.  
3.  The application **shall** allow users to start and end a session manually through the user interface.  
4.  The application **shall** connect to the launch monitor and visor via Bluetooth Low Energy (BLE) using defined GATT services and characteristics.  
5.  The application **shall** be accessible via mobile devices and desktop computers.  
6.  The application **shall** parse and validate incoming shot data before performing any statistical computations.  
7.  The application **shall** deliver at least one derived coaching metric per shot.
8.  The application **shall** upload all processed data and metadata to a secure cloud database via HTTPS immediately following each shot or session.  
9.  The application **shall** use secure Bluetooth pairing methods compliant with current browser-supported Web Bluetooth security standards.  
10.  The application **shall** use HTTPS with TLS encryption for all cloud-based data transactions.  

---
#### Flowchart
<p align="center">
  <img src="https://hackmd.io/_uploads/B1UDWjoCxe.png" alt="AppFlowChart" width="700"></p>
  <p align="center">
Figure 6: Flow Chart showing operation and user interaction with the application
</p>

### Subsystem 6: Heads-Up Display
#### Subsystem Overview and Function
The Heads-Up Display (HUD) subsystem is responsible for clearly projecting both the real-world environment and the HUD graphic interface, which displays the following data metrics: ball speed, ball distance, launch angle, spin rate, and smash factor- along with shot direction and coaching suggestions. 

The HUD combines both hardare and software components to deliver real-time feedback with minimal delay, ensuring the user maintains visual clarity and responsiveness during operation. The HUD interfaces with the following subsystems: Power, Communications, and Physical Visor Design. 


#### Subsystem Operation 
##### Hardware Overview
The HUD hardware consists of three primary components:  a microcontroller, a tracking sensor (IMU), and a micro-OLED display. Each component plays a critical role in ensuring smooth data processing and accurate, real-time visual output

##### *Microcontroller*
The microcontroller shall provide the processing power of the HUD. It shall process incoming data from both the IMU sensor and the app/software subsystem and drive the display at a high speed to ensure minimal delay. The microcontroller shall interface with the 
system through a regulated 3.3 V DC input power signal.

It shall also interface with the Communications subsystem by receiving input data via Bluetooth wireless communication from the app/software and receiving motion data via SPI serial communication from the IMU. Possible microcontroller options include the ESP32-C3, ESP32-S3, and ESP32-P4 series.

* The ESP32-C3 features a 160 MHz clock speed, 4 MB of onboard flash memory, and 400 KB of SRAM.
* The ESP32-S3 offers a 240 MHz clock, 4–16 MB of flash, and 512 KB of SRAM.
* The ESP32-P4 provides the highest performance with a 400 MHz clock speed, 768 KB of SRAM, and 16–32 MB of flash memory.

Due to its superior processing speed and storage capacity, the ESP32-P4 is the optimal choice to ensure smooth and efficient HUD operation with no perceptible delay.

##### *Tracking Sensor*
The tracking sensor shall be an Inertial Measurement Unit (IMU) responsible for measuring the visor’s acceleration, angular velocity, and orientation. The IMU collects three-axis data from its onboard accelerometer, gyroscope, and magnetometer, corresponding to motion along the x, y, and z axes. The sensor’s data is read using an SPI interface by a function called SPIReadWord(x) that retrieves 16-bit values from the IMU’s data registers. This enables accurate tracking of the visor’s position and ensures that the HUD graphics remain properly aligned with the user’s field of view during movement. The IMU shall receive a regulated 3.3 V DC input power signal from the power subsystem and shall transmit motion and orientation data to the ESP32 microcontroller via an SPI serial communication interface.

Possible IMU options include the Adafruit ICM-20948 9DoF, Seeed Grove 9DoF IMU, and DFRobot 10DOF Module, each offering accelerometer, gyroscope, and magnetometer sensors totaling nine degrees of freedom.

##### *Display*
The display shall provide the projection of the HUD graphic interface. It shall maintain high clarity and fast response to minimize disorientation or lag between real-world motion and virtual graphics. To ensure this, the display shall have a minimum resolution of 1080p and a minimum refresh rate of 60 frames per second (fps).The display shall communicate with the microcontroller via a MIPI-DSI input serial communcation interface. 

The HUD shall utilize a micro-OLED display, known for its fast response time, high pixel resolution, and strong contrast ratio—ideal for augmented reality applications. There are two possibile methods for implementing this display: using a transparent display which is a physical screen that allows light to pass through it or using an optical combiner that blends virtual images onto the real world view by reflecting a projected image onto a transparent lens. While transparent displays can support augmented reality, they are typically used for larger-scale applications such as heads-up windshields or public information displays. Optical combiners, on the other hand, are optimized for compact, near-eye systems such as smart glasses. For this application, a display setup utilizing an optical combiner is the most suitable approach. Potential display options compatible with an optical combiner include:

* 0.39″ Micro-OLED Display (1920 × 1080, MIPI, I2C)
* 0.49″ Micro OLEDoS Display (1920 × 1080, 1800 nits, 90 Hz, MIPI)

Both displays require a 1.8 V DC input to power the VDDI I/O supply but differ in their main input voltage requirements. The 0.39″ Micro-OLED Display requires a 5 V DC input to power its internal analog circuitry, which generates the necessary voltages for the OLED panel. In contrast, the 0.49″ Micro OLEDoS Display does not integrate analog power circuitry internally. Therefore, it requires an external 5.3–5.5 V DC input to power AVDD (the positive supply voltage) and a -4 to -5.5 V DC input to power AVEE. These voltages shall be supplied by the power subsystem.

The visor shall include at least one micro-OLED display projecting onto one lens, with the option to implement a second display for dual-lens operation.

All hardware components shall also interface with the physical visor design subsystem. All measurements of dimensions and placement for the hardware components shall be provided to the physical visor design subsystem to ensure an optimal and functional visor model.

##### Software Overview
The HUD software shall be written in C and stored on the ESP32 based microcontroller. It shall use an microcontroller display library to generate the graphic intergface. The graphic interface shall display:
* Data metrics (ball speed, ball distance, launch angle, spin rate, smash factor)
* Shot direction
* Coaching suggestion based on processed performance data
Below is an example of the expected HUD layout that the Smart Golf Visor Team has come up.
  <p align="center">
    <img style="display:block;margin:20px right;padding:1px;border:1px #eee;width:80%;" src="https://hackmd.io/_uploads/r174n9Apxe.jpg" />
    </p> 

<p align="center">Figure 7: HUD Graphic Interface Illustration </p>

#### Interfacing (Conceptual)
<p align="center">Table V </p>
   <p align="center"> Interfaces and Signal Definition </p>

| Interface | Connected Subsystem | Type |Dir.| Protocol /Standard | Description |
|------------|--------------------|-------|------|-----------|--------------|
| **ESP32** | Power | Power | Input | DC | Receives regulated 3.3 V to operate. |
| **IMU** | Power | Power | Input | DC | Receives regulated 3.3 V to operate. |
| **IMU** | Communications | Serial | Output | SPI | Sends acceleration, angular velocity, and orientation data to ESP32. |
| **Micro-OLED** | Power | Power | Input | DC | Receives 1.8 V to power digital logic. |
| **Micro-OLED** | Power | Power | Input | DC | Receives 5 V or 5.3 to 5.5 V (AVDD) and -4 V to -5.5 V (AVEE) to operate depending on the display used. |
| **Micro-OLED** | Communications | Serial | Input | MIPI-DSI | Receives HUD graphics for display. |

#### Summary of “Shall” Statements

1. The HUD **shall** project real-time data metrics, shot direction, and coaching suggestions in the user’s field of view.
2. The microcontroller **shall** receive processed data via Bluetooth and motion data via SPI.
3. The HUD **shall** operate at a minimum of 60 fps with 1080p resolution to ensure smooth and clear visuals.
4. The IMU **shall** provide real-time motion tracking to maintain display alignment with head movement.
5. The HUD **shall** not obstruct the golfer’s natural line of sight or impair safety during use.
6. The HUD **shall** communicate and synchronize seamlessly with the Communications and Power subsystems.
7. All hardware **shall** be designed to integrate with the Physical Visor Design subsystem for structural compatibility.

#### Flowchart
The operation of the Heads-Up Display is illustrated in the flowchart below.
<p align="center"><img style="display:block;margin:20px right;padding:1px;border:1px #eee;width:80%;" src="https://hackmd.io/_uploads/rJC7dW70ge.png" /></p> 

<p align="center">Figure 8: Heads-Up Display Subsystem Flowchart</p>

 ## Ethical, Professional, and Standards Considerations


### Broader Impacts

- **Global / Economic Impacts:**  
  - The Smart Golf Visor has the potential to create an inclusive sports tool usable worldwide, giving athletes and recreational users alike access to real-time golf metrics regardless of location.  
  - It could serve as a **lower-cost training tool**, helping individuals with limited income improve their skills without needing expensive equipment.  
  - The design could also lead to **small business or startup opportunities** in the sports technology and assistive device markets.  
  - Because the software and display outputs are customizable, text can be produced in **any language**, and all metrics can be converted into **regionally appropriate units** (e.g., yards/meters, mph/km/h).  
  - Overall, this system supports economic inclusivity and the global sharing of sports analytics technology through affordable, scalable engineering solutions.

- **Environmental Impacts:**  
  - **Electronic waste concerns:** The visor contains batteries, displays, and sensors that may degrade or become obsolete over time. Responsible **recycling and recovery programs** should be emphasized at end-of-life.  
  - **Energy consumption:** Devices that draw high current degrade batteries faster and require more frequent charging. Efficient **buck/boost converters (≥ 85–93% efficiency)** minimize this effect.  
  - **Materials:** The sourcing and manufacturing of electronics have an environmental footprint. Choosing **durable, repairable, and recyclable components** and adhering to **NEC/NESC guidelines** for safe electrical design (fuses, wiring gauge, insulation) reduces impact and increases product longevity.  
  - **Thermal design:** Minimizing heat generation in converters and ensuring safe **surface temperatures (< 41 °C)** limits energy loss and prevents user discomfort.  

- **Societal Impacts:**  
  - **Accessibility & Inclusion:** The visor makes golf more accessible to **low-vision individuals**, allowing them to independently experience and enjoy the sport through **visual feedback**.  
  - **Education:** The project also promotes **STEM learning**, allowing engineering students and developers to gain hands-on experience in inclusive and human-centered technology design.  
  - **Job Security:** The product is intended to **act as a caddie** and possibly provide some feedback similar to golf instructors. While this may reduce demand for in-person coaching roles, it is positioned as a **training aid** designed to supplement but, not replace human guidance.  
  - **Social Responsibility:** The engineering team acknowledges the balance between innovation and social disruption, aiming to **support learning and accessibility** while maintaining respect for traditional roles within the sport.

- **Public Health & Safety:**  
  - Near-eye electronics introduce **optical, thermal, electrical, and distraction risks**.  
  - The design mitigates these through strict **eye-safety limits**, **thermal limits** for skin contact, **BMS protections**, **inline fusing**, and **strain-relieved cabling**.  
  - The **display luminance and contrast** will conform to international safety limits—maintaining **luminance below 2,000 nits** and **illuminance under 10,000 lux** for user comfort, consistent with **ISO 15004-2** optical exposure limits.  
  - Electrical components are protected to **≤ 5.4 V DC and ≤ 2 A continuous draw**, ensuring compliance with **NEC Article 480 (Battery Systems)** and **NESC low-voltage design requirements**.  

---

**Ethical & Privacy.**  
- Shot metrics and any optional video data must respect **data minimization**.  
- Default operation keeps data **local to the device**. 
- All user-generated data should be stored under **encryption** and designed for **deletion upon user request** to protect personal information and uphold ethical data standards.

---

<center>Table VI</center>  
<center>Standards & How They Inform Design</center>

| Area | Standard / Guidance | Relevance to The Design | Design Impact |
|---|---|---|---|
| **Bluetooth link** | IEEE 802.15.1 [7] | PAN connectivity to phone/monitor | RF band noted (2.4 GHz), coexistence testing, pairing UX constraints |
| **Battery safety** | IEC 62133-2 (rechargeable cells) [6]; **NEC Article 480**; **NESC Part 1, Section 12** | Safe cell selection, wiring, and pack behavior | BMS w/ OV/UV/OCP/OTP; fuse on pack; charge temp window; wiring gauge per NEC; protective enclosure |
| **Eye/optical safety** | ISO 15004-2 (ophthalmic—light hazard) [18] | Near-eye luminance & spectral safety | Luminance & exposure limits ≤ 2,000 nits; exposure under 10,000 lux |
| **Ingress** | IEC 60529 (IP rating) [6] | Dust/sweat/light rain robustness | Target **IP54–IP67**; test dust & splash conditions |
| **Usability & accessibility** | ISO 9241-112 (ergonomics / informative) [5], WCAG principles (informative) [4] | Readability and inclusive design | Enhanced contrast, large text options, and intuitive navigation for all users |
| **Eye protection (not PPE)** | ANSI Z87.1 (informational only) [9] | Clarify non-safety eyewear classification | Explicitly state the device is **not PPE** and not certified for impact resistance |
| **Wiring & power conductors** | **NEC/NESC low-voltage sections** [17] | Wire size, insulation, and fault protection | Proper conductor sizing (AWG rating), insulation class, and inline fusing to prevent short circuits or thermal failure |
| **Converter efficiency** | IEC 62301 [16] | Power-saving requirements for electronics | Target ≥ 85% efficiency across converters to reduce standby losses and extend battery life |

---

<center>Table VII</center>  
<center>Ethical-Risk Register & Mitigations We Implement</center>

| Risk | Why It Matters | Mitigation Built Into the Design |
|---|---|---|
| **Distraction while swinging** | Could reduce performance or lead to user accidents | Minimal overlay during pre-shot & swing; no full-screen alerts during swing |
| **Over-bright / hazardous optical output** | Prolonged exposure can cause eye strain or discomfort | Cap max perceived brightness; verify vs. ISO 15004-2 manufacturer specs; adhere to hazard group compliance |
| **Thermal hot spots on skin** | Could cause discomfort or minor injury | Implement thermal budget; isolate pack; maintain surface ≤ 41 °C; add ventilation if needed |
| **Battery misuse/fault** | May cause fire or venting hazard | Use protected 1S pack with BMS, inline fuse, NEC/NESC-compliant wiring, and enclosure relief |
| **Data privacy** | Risk of unwanted data capture or sharing | Default local storage; encryption and deletion controls; transparent user consent interface |



## Resources


### Budget

The budget for the wearable smart golf visor is a tentative estimate that will be refined as requirements, vendor quotes, and test results are finalized. It serves as an outline for the project and as a practical demonstration that the system can be built within a reasonable cost. The budget, shown in Figure [9], is organized by subsystems to clarify scope and expected cost for each area. Costs reflect single-unit procurement with no sales tax and no mandatory subscriptions. Where multiple part options existed, unit prices were averaged to produce a single representative figure per line item and then rolled up per subsystem.

**Subsystems covered:**

Launch monitor (primary sensing) — a portable photometric unit using a Raspberry Pi, dual Mobile Industry Processor Interface (MIPI) global-shutter cameras, and synchronized infrared (IR) strobing to measure per-shot ball speed, launch angles, and spin, publishing a normalized Shot record.

Visor / head-up display (HUD) — a head-worn near-eye display that acts as a Universal Serial Bus (USB)-High-Definition Multimedia Interface (HDMI) sink via the host device.

Communications — a microcontroller and Bluetooth Low Energy (BLE) radio that bridge sensor data to the application and HUD.

Power and I/O — the battery, charging and protection, power-delivery cabling and connectors, and the printed-circuit board (PCB).


**Launch Monitor — $436.00**

This subsystem implements the sensing pipeline for a do-it-yourself launch monitor modeled after the PiTrac approach. It uses two global-shutter Raspberry Pi–compatible cameras (2 × $50) and wide-angle lenses (2 × $25) to capture the ball’s motion with minimal motion blur. A Raspberry Pi 4 (1 × $55) executes the image pipeline and publishes shot results to the bridge. Supporting parts include transistor–transistor logic (TTL)–level discretes and cabling (1 × $25) for camera and strobe control, an infrared (IR) long-pass filter (1 × $15) to suppress ambient light, a chip-on-board (COB) light-emitting diode (LED) array (1 × $30) as the strobe source, and an LED power supply (1 × $36) sized for the strobe duty cycle. Mechanical items include a chassis/project housing (1 × $30) and 3D-printed mounts and shields produced in one of the following materials:

Polyethylene terephthalate glycol (PETG): Best overall durability and impact resistance for field use; good layer adhesion, low warpage, and better UV/moisture tolerance than polylactic acid. Typical print temps 230–250 °C; glass-transition temperature (Tg) ~80 °C; filament cost ~$20–$25/kg.

Polylactic acid (PLA): Easiest to print and dimensionally accurate; suitable for jigs, internal brackets, and non-sun-exposed parts. Lower heat resistance (Tg ~55–60 °C) and reduced toughness outdoors; filament cost ~$18–$22/kg.

Acrylonitrile–butadiene–styrene (ABS): Higher heat resistance (Tg ~95 °C) and good toughness; preferred near warm electronics or lighting. Requires an enclosure to limit warpage and emits fumes during printing; filament cost ~$20–$28/kg.

For outdoor range use and general robustness, PETG is recommended for primary brackets, shrouds, and cable guards; PLA is acceptable for non-structural internal fixtures; ABS is appropriate for high-temperature or solvent-bonded components. Together, these parts and materials provide the sensing, illumination, and mechanical robustness required to measure ball speed, launch angles, and spin while remaining portable.This subsystem implements the sensing pipeline for a do-it-yourself launch monitor modeled after the PiTrac approach. It uses two global-shutter Raspberry Pi–compatible cameras (2 × $50) and wide-angle lenses (2 × $25) to capture the ball’s motion with minimal motion blur. A Raspberry Pi 4 (1 × $55) executes the image pipeline and publishes shot results to the bridge. Supporting parts include transistor–transistor logic (TTL)–level discretes and cabling (1 × $25) for camera and strobe control, an infrared (IR) long-pass filter (1 × $15) to suppress ambient light, a chip-on-board (COB) light-emitting diode (LED) array (1 × $30) as the strobe source, and an LED power supply (1 × $36) sized for the strobe duty cycle. Mechanical items include a chassis / project housing (1 × $30) and polyethylene terephthalate glycol (PETG) filament for printed mounts and shields (2 × $20). Together these parts provide the sensing, illumination, and mechanical robustness required to measure ball speed, launch angles, and spin while remaining portable and low cost.

**Visor / HUD — $301.00**

The HUD is a head-mounted near-eye display driven over a wired video link from the host device, which provides a bounded, repeatable video path and frame-accurate updates. The bill of materials includes two full-high-definition micro-organic light-emitting diode (micro-OLED) panels (2 × $144 averaged) that provide high contrast and daylight-readable text, an inertial-measurement unit (IMU) (1 × $13) for stable placement of visual elements if the HUD software uses head pose, and glasses frames (1 × $25) to mount the optics safely and comfortably. Using a wired link reduces integration risk and eliminates recurring software fees.

**Communications — $25.00**

This subsystem bridges the launch monitor and the HUD/application. It uses a microcontroller (1 × $20) to terminate sockets or serial links and to render or forward messages, and a BLE radio module (1 × $5) for wireless control or future accessories. Separating communications clarifies the data path and avoids double counting in the HUD budget.

**Power and I/O — $85.00**

This subsystem supplies stable, protected power and the physical interconnects. It includes a lithium-polymer (Li-Po) cell (1 × $13), boost converters (1 × $5.50), a battery-management / protection circuit (BMS) (1 × $7.50), a USB-C receptacle (1 × $4.50), a charger integrated circuit (IC) or module (1 × $4.50), and miscellaneous passives and wiring such as LEDs, thermistors, fuses, and resistors (1 × $12.50). A small PCB including delivery (1 × $37.50) consolidates power and connectors. Short, power-delivery (PD)-rated cables and strain relief preserve voltage margin and prevent disconnects during a swing, while the BMS provides over-current and thermal protection.

This total establishes the working baseline for design, procurement, and test planning. It reflects a complete, portable system that measures the ball’s motion, normalizes the resulting telemetry, and renders clear on-visor metrics without subscriptions or sales tax.

<p align="center">Summary</p>


<p align="center">
  <img src="https://hackmd.io/_uploads/B11DhIY0el.jpg" alt="conceptual budget">
</p>
<p align="center">Figure 9: Smart Golf Visor Budget</p>




### Division of Labor

Brandon Zerkovich has taken several computer science/ engineering classes (Introduction to Problem Solving & Computer Programming, Data Structures and Algorithms, and Microcomputer systems) where he has gained hands-on experience with microcontrollers and learned C and C++ coding languages. These skills are useful for both the Heads-Up Display (HUD) and app/software subsystems. The Smart Golf Visor team has decided to task Brandon with working on the HUD subsystem. He will take on the specifications for this subsytem and create a corresponding solution.

Landen Johnson has previous coursework in control system analysis, power system analysis, introduction to power systems, data structures, and mechatronics. He has also had prior internships in the utility sector, where he gained hands-on experience in testing, evaluating, and validating control and communication equipment under real operating conditions. While his direct experience in PCB design and deeper computer engineering is limited, that gap can be mitigated through targeted learning and research in the detailed power design and ensures the interfaces with other subsystems is robust and verifiable. The Smart Golf Visor team has decided to task Landen with working on the power subsystem. He will take on the specifications for this subsytem and create a corresponding solution.

Andrew Goostree has recent internship experience cleaning and structuring raw datasets, and his coursework like Introduction to Problem Solving and Computer Programming, Data Structures and Algorithms, and Microcomputer Systems directly supports converting raw launch monitor outputs into usable, time-stamped records. He is comfortable with Raspberry Pi setup, basic electronics and soldering, and 3D printing in polyethylene terephthalate glycol (PETG) for brackets and enclosures. He also has working knowledge of Transmission Control Protocol (TCP)/WebSocket, Bluetooth Low Energy (BLE), and camera bring-up, including focus and infrared (IR) filtering with web-based calibration. These skills align with the physical implementation of the PiTrac and the Data Acquisition subsystem for the Smart Golf Visor team.  The Smart Golf Visor team has decided to task Andrew with working on the launch monitor subsystem. He will take on the specifications for this subsytem and create a corresponding solution.

Ryan Zerkovich has gained experience working with microcontrollers and programming through coursework such as Computer Programming, Data Structures and Algorithms, and Microcomputer Systems. These courses provided him with a strong foundation in embedded programming and system-level problem solving, which are valuable skills for the Communication Subsystem. While his experience with specific communication protocols is currently limited, he is committed to closing this gap through targeted learning and research. The Smart Golf Visor team has decided to task Ryan with developing the Communication Subsystem, where he will apply his technical background to design a reliable and efficient method for data transmission between devices.

Michael Madigan has previous coursework/internship experience with programming languages, including Python, C, and C++. He also has hobbyist experience interfacing with microcontrollers. Michael intends to utilize that experience to close his knowledge gap in necessary Java and HTML environments to deliver a seamless end-user experience.The Smart Golf Visor team has decided to task Michael with developing the App/Software subsystem.

Bryce Hughes has prior experience in field engineering, system testing, and soldering that provides a practical foundation necessary to assemble, evaluate, and refine the physical prototype of the Smart Golf Visor. Bryce’s understanding of basic circuit integration and elementary coding through his previous coursework also enables him to bridge mechanical assembly with electrical interfacing during the early testing stages. The Smart Golf Visor team has decided to task Bryce with developing the Physical Visor Design subsystem.


### Timeline

<p align="center">
  <img src="https://hackmd.io/_uploads/Hyua7j9Clg.png" alt="GanttChart_Sem1" width="700"> </p>
  <p align="center">Figure 9: Updated Gantt Chart showing the Fall 2025 Semester.</p>


<p align="center">
  <img src="https://hackmd.io/_uploads/B1gyNiqRlg.png" alt="GanttChart_Sem1" width="700">
</p>
<p align="center"> Figure 10: Updated Gantt Chart showing the Spring 2026 Semester.
</p>

## References


[1] IEEE, “IEEE code of ethics | IEEE,” IEEE Advancing Technology for Humanity, https://www.ieee.org/about/corporate/governance/p7-8 (accessed Sep. 18, 2025). 
[2] “Lithium Polymer Battery Protection Technology: How to prevent overheating and explosion risks,” myliontech, https://myliontech.com/en/lithium-polymer-battery-protection-technology-how-to-prevent-overheating-and-explosion-risks/ (accessed Oct. 18, 2025). 
[3] “CC and CV charging of Lithium Polymer Battery,” Lithium_Polymer_Battery_net, https://www.lithium-polymer-battery.net/cc-and-cv-charging-of-lithium-polymer-battery/ (accessed Oct. 17, 2025). 
[4] M. Cooper, “Web content accessibility guidelines (WCAG) 2.1,” W3C, https://www.w3.org/TR/WCAG21/ (accessed Oct. 17, 2025). 
[5] ISO, “Ergonomics of human-system interaction,” ISO, https://www.iso.org/obp/ui/#iso:std:iso:9241:-112:ed-2:v1:en (accessed Oct. 17, 2025). 
[6] “IEC 62133: Safety Testing for lithium ion batteries,” intertek, https://www.intertek.com/batteries/iec-62133/ (accessed Oct. 14, 2025). 
[7] “IEEE SA - IEEE Standard for Information technology-- local and metropolitan area networks-- specific requirements-- part 15.1A: Wireless Medium Access Control (MAC) and Physical Layer (PHY) specifications for Wireless Personal Area Networks (WPAN),” IEEE Standards Association, https://standards.ieee.org/ieee/802.15.1/3513/ (accessed Oct. 14, 2025). 
[8] “Ingress Protection (IP) ratings,” IEC, https://www.iec.ch/ip-ratings (accessed Oct. 14, 2025). 
[9] B. Kelechava, “ANSI/ISEA Z87.1-2020: Current standard for safety glasses - ANSI BLOG,” The ANSI Blog, https://blog.ansi.org/ansi/ansi-isea-z87-1-2020-safety-glasses-eye-protection/ (accessed Oct. 14, 2025). 
[10] Samainstage, “IEEE launches new standard to address ethical concerns during systems design,” IEEE Standards Association, https://standards.ieee.org/news/ieee-7000/ (accessed Sep. 24, 2025).
[11] ESP32-C3 series Datasheet version 2.2, https://www.espressif.com/sites/default/files/documentation/esp32-c3_datasheet_en.pdf (accessed Oct. 19, 2025). 
[12] ESP32-S3 series Datasheet version 2.0, https://www.espressif.com/sites/default/files/documentation/esp32-s3_datasheet_en.pdf (accessed Oct. 19, 2025). 
[13] Preliminary ESP32-P4 series Datasheet pre-release v0.5, https://www.espressif.com/sites/default/files/documentation/esp32-p4_datasheet_en.pdf (accessed Oct. 19, 2025). 
[14] Streamlit and Streamlit Cloud web application documentation,
https://docs.streamlit.io/ (accessed Oct. 20, 2025)
[15] Wangfred, “How to make a heads-up display glasses: A comprehensive DIY guide,” INAIRSPACE, https://inairspace.com/blogs/learn-with-inair/how-to-make-a-heads-up-display-glasses-a-comprehensive-diy-guide?srsltid=AfmBOooBdnwXlWmnyAdKJQRSVs1zUNqJjkFWid4B2v0YD2_p1Ywq6vZF (accessed Oct. 19, 2025). 
[16] “IEC 62301:2011,” IEC, https://webstore.iec.ch/en/publication/6789 (accessed Oct. 24, 2025). 
[17] W. T. W. Guy, “The Ultimate Guide to Wire Ampacity and NEC Basics,” Wire & Cable Your Way Blog, https://blog.wireandcableyourway.com/the-ultimate-guide-to-wire-ampacity-and-nec-basics (accessed Oct. 27, 2025). 
[18] “ISO 15004-2:2024,” ISO, https://www.iso.org/standard/79919.html (accessed Oct. 27, 2025). 
[19] “PiTrac: The DIY Golf Launch Monitor,” Hackaday.io, https://hackaday.io/project/195042-pitrac-the-diy-golf-launch-monitor
 (accessed Oct. 15, 2025).

## Statement of Contributions

Brandon Zerkovich contributed to creating the HUD subsystem under the atomic subsystem specifications section. He also wrote the introduction, high-level solution, and his section for the division of labor.

Landen Johnson contributed to creating the power subsystem under the atomic susbsystem specifications section. He also wrote the ethical and standard portion, explaining how they impact the design of the visor.

Andrew Goostree contributed to creating the Launch Monitor subsystem, wrote the section details, created the flowchart, created the budget, and his section for the division of labor. 

Ryan Zerkovich contributed to creating the communication subsystem under the atomic subsystem specificatins section. He also wrote the fully restating the problem section.

Michael Madigan contributed to creating the application/software subsystem. He also updated the Gannt Chart to reflect updated scheduling and included it in the Timeline section of this report.

Bryce Hughes contributed to creating the Physical Visor Design subsystem. He also wrote the comparative analysis of potential solutions section.
