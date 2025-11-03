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

This section evaluates multiple design pathways for each of the major subsystems of the Smart Golf Visor, which include the Launch Monitor, Communications, Power, Visor Design, App/Software, and Heads-Up Display. For each subsystem, an overview is provided to discuss its functional significance and the key factors influencing the design selection. Potential solutions are then identified and compared, followed by a trade study that analyzes relevant performance metrics and existing products related to each design pathway. Each subsection concludes with a justified final design decision based on the comparative analysis.

#### Trade Study Rating Scale
Each design option was rated on a simple 1–5 scale to keep the comparisons consistent and easy to understand. The criteria for the scores are described below:
- A score of **1** means the option performs poorly or doesn’t meet the core design goals
- A score of **3** means is a workable option that meets expectations,  
- A score of **5** means it fully exceeds expectations.  
- Scores in between reflect how close each option comes to hitting the project’s key targets based on actual data, testing, or vendor specs.  

Using this scale makes it easier to compare options that differ in complexity, cost, or performance while keeping the trade study objective and transparent.

### Launch Monitor

##### Overview
The launch monitor subsystem provides the primary sensing for the wearable golf visor system. It must capture per-shot ball kinematics and make those data available to the data-acquisition and visor head-up display (HUD) subsystems with low latency and a stable message format.


##### Possible Solutions
The following are possible options: 
1) A **Do-It-Yourself (DIY) Photometric PiTrac-style monitor** built on a Raspberry Pi with dual Mobile Industry Processor Interface (MIPI) global-shutter cameras and infrared (IR) strobing, which publishes shot results locally over JavaScript Object Notation (JSON) or WebSocket
2) **Garmin Approach R10**
 
The **DIY PiTrac-style photometric monitor** uses the PiTrac design that is built on a Raspberry Pi with dual Mobile Industry Processor Interface (MIPI) global-shutter cameras and infrared (IR) strobing that exposes results through a local web server or Golf Simulator Pro (GSPro) Open Connect JSON over Transmission Control Protocol (TCP). This allows the project to terminate the data directly in its own bridge, normalize fields, and forward to the visor HUD without subscriptions or vendor cloud services. It also enables inspection of intermediate data for calibration and test.

<p align="center">
  <img src="https://hackmd.io/_uploads/ByqSZQHy-g.jpg" alt="PiTrac DIY Launch Monitor">
  <br>
  <strong>Figure 1: DIY PiTrac Launch Monitor</strong>
</p>


The second option is the **Garmin Approach R10** which is a commercial Doppler-radar monitor. It offers a mature, portable device that measures ball and club metrics for indoor nets and outdoor ranges. However, Garmin documents usage through the Garmin Golf application and simulator integrations and does not publish a free, public shot-telemetry API. Some simulator and virtual-play functions are tied to a Garmin Golf membership.

<p align="center">
  <img src="https://hackmd.io/_uploads/HyILfXBJbe.jpg" alt="Garmin Approach R10">
  <br>
  <strong>Figure 2: Garmin Approach R10</strong>
</p>

###### **Trade Study: Launch Monitor Selection**
The weighting rationale for this trade study prioritizes practical integration, accessibility, and cost-effectiveness for the Smart Golf Visor launch monitor subsystem:

- **Data/API Accessibility (25 %)** emphasizes local access to shot data and support for JSON or GSPro Open Connect formats.
- **Integration with Visor HUD (20 %)** accounts for low latency and flexible data schemas to ensure smooth real-time display.
- **Cost (15 %)** ensures the chosen solution fits within the $1,100 system budget.
- **Build Effort / Calibration Required (10 %)** reflects the time and complexity needed to assemble, calibrate, and maintain the system.
- **Simulator / Third-Party Path (10 %)** evaluates compatibility with existing golf simulators or other external tools.
- **Vendor Support / Maturity (10 %)** measures product reliability and availability of technical support.

<p align="center">
  <strong>Table I</strong>
  <br>
  Launch Monitor Trade Study
</p>

| **Criterion** | **Weight (%)** | **DIY PiTrac-Style (Photometric)** | **Garmin Approach R10 (Radar)** |
|:--------------|:--------------:|:----------------------------------:|:--------------------------------:|
| Data/API Accessibility | 25 | 5 | 2 |
| Integration with Visor HUD | 20 | 5 | 3 |
| Cost  | 15 | 5 | 3 |
| Build Effort / Calibration Required | 10 | 3 | 5 |
| Portability / Field Robustness | 10 | 4 | 5 |
| Simulator / Third-Party Path | 10 | 5 | 3 |
| Vendor Support  | 10 | 3 | 5 |
| **Weighted Total** | **100** | **4.45** | **3.55** |

##### Decision
The project will use the **DIY PiTrac-style photometric launch monitor** as the primary sensing source. This choice is justified because it gives the team full local access to per-shot data, allows the data to be normalized and time-stamped inside the project’s own Data Acquisition and Interface subsystem, avoids recurring subscription costs, and aligns with the existing budget (~$436 for the sensing bill of materials). The **Garmin Approach R10** remains a viable comparison or backup device due to its proven field performance and portability, but it will not be the primary integration target because its data path is mediated by the Garmin Golf application and not openly documented for third-party ingestion.

### Communications

##### Overview
The Communications Subsystem is responsible for reliable data transfer throughout the entire system via wireless and wired connections. The wireless connection allows the app and visor to access the shot metrics captured by the custom-built launch monitor (ball speed, ball distance, launch angle, spin rate, and smash factor), increasing its mobility and easy setup. The wired connections will consist of serial communication protocols that will support the HUD display within the visor. 

##### Possible Solutions
###### Wireless Communication
The selection of wireless communication protocol depends primarily on the bandwidth, range, power consumption and network complexity. The two possible wireless options to consider are:
1) **Wifi**
2) **Bluetooth**

**Wifi** (2.4 GHz / 5 GHz) offers large bandwidth (54–600 Mbps) with typical ranges around 30-100 meters, making it a great for long-range devices transferring data at high speeds. However, this requires a router for managing device communication through assigning IP addresses to connected devices and handling data packet routing. leading to higher power consumption. This is due to continuous radio activity for maintaining connections, the processing required by the full network stack (IP, TCP/UDP, and encryption protocols), and the high transmission power needed for reliable communication. Instead of requiring a physical router, **Wifi Direct** protocol uses one of the connected devices as a "soft access point" lowering the power consumption. However, it still relies on IP addressing and network protocol handling, making it more complex compared to the following option. 

**Bluetooth** (2.4 GHz)provides moderate bandwidth, typically 1–3 Mbps with operational ranges of approximately 10–30 meters. Unlike **Wi-Fi**, **Bluetooth** does not require a router but instead, devices establish direct connections using a master–slave architecture which simplifies the network's configuration. The power consumption is reduced through limited radio activity, lightweight protocol stacks, and adaptive frequency hopping to mitigate interference. **Bluetooth Low Energy** (BLE) (2.4 GHz) only has a bandwidth up to 2 Mbps but further enhances energy efficiency by allowing devices to enter sleep states between transmissions and minimizing protocol overhead. 

###### Wired Communication
The selection of wired communication protocol depends on bandwidth, latency, and connection complexity. A few options to consider are:

1) **UART**
2) **SPI**
3) **I²C**
4) **MIPI**

**UART (Universal Asynchronous Receiver/Transmitter)** provides moderate bandwidth capable of 1-3 Mbps, with moderate latency around 10 µs. It consists of a simple two wire connection (TX and RX) for point-to-point communication, requiring minimal pins on the microcontroller. **UART** is robust and energy-efficient for short-range connections. However, **UART** transmits data asychronously creating a higher latency compared to the other options.

**SPI (Serial Peripheral Interface)** provides high bandwidth, capable of greater than 10 Mbps, with very low latency due the sychronous clock. The connection required between devices consists of multiple wires (MISO, MOSI, SCK, and a separate chip-select line per device), increasing the wiring complexity and microcontroller pin usage. **SPI** offers highly reliable, low-latency communication and is suitable for scenarios where multiple sensors require fast, real-time updates. While it consumes slightly more power than **UART** or **I²C** due to continuous clocking and more active pins, it is ideal for high-speed, time-critical data transfer like HUD updates in the visor.

**I²C (Inter-Integrated Circuit)** operates at low to moderate speeds, typically 100 kHz to 1 MHz, with slightly higher latency than **SPI** as it can't transmit and receive data simutaneously. The connection requires a two-wire bus (SDA and SCL) that allows multiple devices to share the same connection via unique addresses, reducing wiring complexity. This makes **I²C** convenient if the visor includes multiple sensors or peripheral devices. Pull-up resistors are required, and the protocol can be sensitive to long wires or electrical noise, which may affect reliability for real-time data.

**MIPI DSI (Display Serial Interface)** is a high-speed, low-latency interface commonly used for connecting displays in mobile and embedded devices. It supports very high data rates (hundreds of Mbps to multiple Gbps) over differential pairs, making it suitable for high-resolution HUDs. **MIPI DSI** is designed for short-range, on-board connections with low power consumption per bit transmitted. Its complexity is higher than **UART**, **I²C**, or **SPI**, requiring specialized hardware support and careful signal routing. **MIPI** could be considered if future designs involve high-resolution displays.

###### Microcontroller  
For the visor system to support wireless connectivity, it shall require a microcontroller. This selection depends on processing speed, communcation capabilities, software complexity and pre-built wireless module. The following are possible options to consider: 
1) **Raspberry Pi Zero W** 
2) **ESP32-S3**
3) **ESP32-P4** 

The **Raspberry Pi Zero W** offers moderate performance with a single-core ARM11 processor, computing at speeds of 1 GHz. It includes built-in Bluetooth 4.1 (speeds up to 1 Mbps), serial communications (**SPI**, **I²C**, and **UART**), and **MIPI** interfacing. However, it requires a full operating system (Linux), which increases software complexity.

<p align="center">
  <img src="https://hackmd.io/_uploads/SkxSyOzJWg.png" alt="Raspberry Pi Zero W">
  <br>
  <strong>Figure 3: Raspberry Pi Zero W</strong>
</p>


The **ESP32-S3** has high performance featuring a dual-core 32-bit Xtensa processor capable of handling speeds up to 240 MHz. It includes serial communication (**SPI**, **I2C**, and **UART**) and built-in Bluetooth 5 Low Energy, allowing for faster speeds (up to 2 Mbps) and lower power consumption. However, the **ESP32-S3** has no **MIPI** support which would require two TC358778X MIPI-to-Parallel Bridge ICs to interface with the displays. Additionally, the ESP32-S3 can run bare-metal firmware or a real-time operating system, eliminating the need for a full operating system. 

<p align="center">
  <img src="https://hackmd.io/_uploads/Bk4t1dz1Ze.png" alt="ESP32-S3">
  <br>
  <strong>Figure 4: ESP32-S3</strong>
</p>


The **ESP32-P4** has the highest performance capability of the three options due to its Dual-core 32-bit RISC-V processor capable for speeds up to 400 MHz. It has **MIPI** and serial communications (**SPI**, **I2C**, and **UART**) to interact with the display hardware but does not include built-in Bluetooth. The **ESP32-P4-WIFI6** is a development board that provides the pre-built BLE module for the **ESP32-P4**.

<p align="center">
  <img src="https://hackmd.io/_uploads/rkWox_G1bx.png" alt="ESP32-P4">
  <br>
  <strong>Figure 5: ESP32-P4</strong>
</p>
<br>


###### Trade Study : Microcontroller
The weighting rationale prioritizes ergonomic performance and ease of integration for a wearable augmented-reality device. 
- **Processing Performance (30 %)**  ensures real-time responsiveness for AR functions.
- **Communication Capability (25 %)** supports reliable wireless data exchange.
- **Power Efficiency (20 %)** maximizes battery life for extended outdoor use.
- **Software/OS Complexity (15 %)** accounts for ease of integration and reduced development effort. 
- **Built-in Wireless Module (10 %)** provides convenient onboard connectivity without heavily impacting overall performance.

<p align="center">
  <strong>Table II</strong>
  <br>
  Microcontroller Trade Study
</p>

| Criterion                     | Weight (%) | Raspberry Pi Zero W | ESP32-S3 | ESP32-P4 |
|-------------------------------|:----------:|:-----------------:|:--------:|:--------:|
| Processing Performance        | 30         | 3                | 4        | 5        |
| Communication Capability      | 25         | 4                | 4        | 5        |
| Power Efficiency              | 20         | 2                | 4        | 4        |
| Software / OS Complexity      | 15         | 2                | 4        | 4        |
| Built-in Wireless Module      | 10         | 4                | 5        | 3        |
| **Weighted Total (100%)**     | **100**    | **3.05**         | **4.25** | **4.45** |


##### Decision

Based on the evaluation of wireless protocols, wired communication interfaces, and microcontroller options and trade study, the following are the desired choices:
1) **Bluetooth Wireless Communication Protocol**
2) ** SPI Serial Communication and MIPI DSI Protocol**
3) **ESP32-P4**

**Bluetooth Wireless Communication Protocol** provides simplified device networking and lower power consumption. The low range and bandwidth fit within the smart golf visor functionality.

**SPI serial communication** provides the high speed bandwidth and lower latency to match the HUD display's peripheral. This is crucial for the time-sensitive HUD updates. **MIPI DSI** protocol was chosen for the display interface to support high-speed, low-power transmission of graphics data

The **ESP32-P4** was selected as the primary controller for the visor system. Its dual-core RISC-V processor operating at up to 400 MHz provides sufficient computational capability for real-time data processing, while its native support for **MIPI** enables direct interfacing with the OLED display without the need for additional bridge ICs. With the **ESP32-P4-WIFI6** development board, it also provides built-in bluetooth module to recieve the shot metrics transmitted by the app.
### Power

##### Overview
The Power Subsystem provides regulated electrical energy for all electronic components within the Smart Golf Visor, including the microcontroller, IMU sensor, and display.
It must supply clean, efficient, and protected DC power rails derived from a single rechargeable battery source while maintaining safe operation under outdoor and portable conditions.
This subsystem is critical to overall system reliability, as voltage fluctuations or inefficiency could result in unstable display performance or reduced runtime.
To ensure uninterrupted operation throughout a typical round of golf, the power system is designed to sustain continuous functionality for at least 18 holes, including margin for colder environments and extended use.
This subsystem is critical to system reliability since voltage fluctuations or inefficiency could result in unstable display performance or reduced runtime.  
Therefore, the subsystem must:
- Support multiple voltage rails (3.3 V logic, 1.8 V display logic, 5.0–5.5 V analog).  
- Include battery protection and conversion circuitry meeting IEC 6213 and NEC low-voltage safety requirements.  
- Remain lightweight, compact, and efficient enough for use in a wearable visor design.


##### Possible Solutions

###### **Battery Chemistry Comparison**

Two main rechargeable battery chemistries were considered for this subsystem: 
1) **Lithium-Polymer (Li-Po)** 
2) **Lithium-Ion (Li-ion)** 

The **Li-Po pouch cell** exhibits an energy density between 150–200 Wh/kg, offering a very light and thin form factor ideal for compact wearable applications. While it requires a Battery Management System (BMS) for safe operation, it remains thermally stable and reliable, with a cycle life of approximately 300–600 cycles and moderate cost. Its flat, flexible shape makes it especially advantageous for the visor, as it can integrate flush with the rear-mounted housing.

The **Li-ion cylindrical cells** (such as 18650 or 21700 formats) offer slightly higher energy density (180–250 Wh/kg) and longer life (500–800 cycles) with high discharge capability. However, their cylindrical structure adds bulk and weight, making them less practical for integration into a curved visor design.  
LiFePO₄ cells provide exceptional safety and long life (1000+ cycles) but lower energy density (90–140 Wh/kg) and nominal voltage (~3.2 V), which complicates converter design.  
NiMH batteries, while very safe, are heavy and have the lowest energy density (60–120 Wh/kg), leading to shorter runtimes and poor compactness for wearable use.

<p align="center">
  <img src="https://hackmd.io/_uploads/H1zW3EEJZg.jpg" alt="Common battery formats — 18650 Li-ion vs flat Li-Po pouch">
  <br>
  <strong>Figure 6: Common battery formats — 18650 Li-ion (left) vs flat Li-Po pouch (right)</strong>
</p>


**Result:**  
The **Li-Po battery** provides the best balance of weight, capacity, and integration flexibility. Its rectangular pouch design enables compact placement within the visor’s rear housing while maintaining safe operation between **0–60 °C**. This makes Li-Po the optimal chemistry for the Smart Golf Visor’s power subsystem.

###### **DC–DC Converter Options**
The selection is based around output range, efficiency, and application. To convert the Li-Po pack’s 3.7 V nominal output into stable voltages for each subsystem, the following are possible options : 
1) **Linear LDO regulators**
2) **Buck (step-down) converters**
3) **Boost (step-up) converters**
4) **Inverting converters**
5) **Buck-Boost converter**

**Linear LDO regulators** deliver fixed voltages such as 1.8 V or 3.3 V, providing simple implementation and low noise at the expense of efficiency (50–70 %), making them best suited for low-current rails.  
**Buck (step-down) converters** achieve 85–95 % efficiency when the input voltage exceeds the desired output. However, they are less effective when the Li-Po voltage drops close to 3.0 V.  
**Boost (step-up) converters** reach 88–93 % efficiency and are essential for maintaining a stable 5.0–5.5 V output for the OLED analog rail (AVDD), even under battery discharge conditions.  
**Inverting converters** provide around 85–90 % efficiency and are required to generate the OLED’s −5 V analog rail (AVEE) for proper display operation.  
Finally, a **buck-boost converter** efficiently regulates 3.3 V output (90–93 %) across the full Li-Po discharge range (2.9–4.2 V), ensuring stable power to the ESP32-P4 and IMU regardless of battery condition.

<p align="center">
  <img src="https://hackmd.io/_uploads/Hk1g0V4kbx.png" alt="Typical buck-boost converter topology">
  <br>
  <strong>Figure 7: Typical buck-Typical buck-boost converter topology used for MCU and logic rails</strong>
</p>


**Recommended Configuration:**  
- **3.3 V Buck-Boost Converter:** Powers the ESP32-P4 and IMU logic rail.  
- **1.8 V LDO:** Supplies the OLED’s digital logic rail (low-current).  
- **5.4 V Boost Converter:** Drives the OLED’s AVDD analog rail.  
- **−5.4 V Inverting Converter:** Provides the OLED’s AVEE analog rail.  

This configuration ensures high efficiency, low ripple, and isolation between analog and digital domains, optimizing performance while minimizing heat and noise.

###### **Trade Study: Battery & Converter Integration**
The weighting rationale prioritizes runtime efficiency and system stability for a wearable, battery-powered device.

- **Energy Density (25 %)** ensures long operation per charge, supporting a full 18-hole round.  
- **Weight / Form Factor (20 %)** minimizes visor weight for user comfort.  
- **Efficiency (20 %)** focuses on maximizing usable energy while minimizing conversion losses.  
- **Safety (15 %)** emphasizes compliance with IEC 62133 and NEC 480 standards for portable Li-ion systems.  
- **Complexity (10 %)** accounts for ease of integration and circuit management.  
- **Cost (10 %)** considers affordability within the project’s material budget.

<p align="center">
  <strong>Table III</strong>
  <br>
  Battery and Converter Integration Trade Study
</p>

| **Criterion** | **Weight (%)** | **Li-Po + Multi-Converter (Chosen)** | **Li-ion + Single Buck-Boost** | **LiFePO₄ + Boost** |
|:--|:--:|:--:|:--:|:--:|
| Energy Density | 25 | 5 | 4 | 3 |
| Weight / Form Factor | 20 | 5 | 3 | 3 |
| Efficiency | 20 | 5 | 4 | 4 |
| Safety (IEC 62133 / NEC 480)** | 15 | 4 | 4 | 5 |
| Complexity | 10 | 3 | 4 | 3 |
| Cost | 10 | 4 | 4 | 3 |
| **Weighted Total** | **100** | **4.65** | **3.95** | **3.70** |


##### Decision
The final power solution will include:
1) **Single-cell (1S) Li-Po pack** rated **5 000–6 000 mAh** 
2) **Multi-converter system** implementing:  
A.  **3.3 V buck-boost** for compute and sensors  
B.  **1.8 V LDO** for display logic  
C.  **+5.4 V boost** and **−5.4 V inverter** for OLED analog rails  

This configuration satisfies all identified electrical and mechanical constraints while maintaining high efficiency (target > 90 %) and compliance with IEC 62133 and NEC 480 standards governing portable battery systems.  

Final performance values (including efficiency, thermal margin, and runtime) will be confirmed during detailed design and may be adjusted based on final component selections, particularly the verified battery capacity and converter specifications.

### Visor Design  

##### Overview  
The Visor Design subsystem serves as the structural and ergonomic foundation for the Smart Golf Visor. It supports the optical engine, electronics sleeve, and cabling while ensuring the heads-up display remains aligned within the golfer’s natural line of sight. The visor must be lightweight, stable during movement, and comfortable for extended outdoor wear. Because this subsystem directly affects user safety and usability, its design prioritizes balance, durability, and compliance with ISO 15004-2 optical safety standards.  

##### Possible Solutions  
The following are possible options:
1) **Existing Sports-Glasses Frame**
2) **Custom 3D-Printed Frame**
3) **Commercial AR-Glasses Base**

The **Existing Sports-Glasses Frame** uses a commercially available sports-glasses platform as the base frame. The frame offers high-impact polycarbonate construction, UV protection, and built-in ergonomic shaping designed for outdoor athletics. It provides immediate wearability, minimal design effort, and proven comfort. Minor modifications—such as adding a clamp-mounted HUD pod, rear electronics sleeve, and cable routing—can be performed without affecting the eyewear’s integrity or balance.

<p align="center">
  <img src="https://hackmd.io/_uploads/ry0ZcSr1Zg.jpg" alt="Ready-made sports-glasses frame">
  <br>
  <strong>Figure 8: Ready-made sports-glasses frame (e.g., Uvex Hypershock) used as the visor’s base structure</strong>
</p>
<br>


A fully **Custom 3D-Printed Frame** produced from ABS/PC or CF-Nylon allows total design freedom, with integrated optical mounts and internal wiring channels. While this yields optimized geometry, it introduces longer prototyping times, tighter tolerance demands for optical alignment, and potential surface-finish issues that can affect comfort.  

<p align="center">
  <img src="https://hackmd.io/_uploads/HJM6qrSyZl.jpg" alt="Custom 3D-printed Visor Frame" style="border:1px #eee; padding:1px; margin:20px;">
  <br>
  <strong>Figure 9: Custom 3D-printed Visor Frame options used for prototype development</strong>
</p>


Repurposing an **existing augmented-reality eyewear** product (such as Vufine V2 or Rokid Max) could provide ready-made optics and housings, but these devices limit access to internal hardware, firmware, and mechanical modification. Integration with custom electronics is highly constrained, and costs are substantially higher. 

<p align="center">
  <img src="https://hackmd.io/_uploads/SJ9HjSBy-g.jpg" alt="Commercial AR-glasses platform">
  <br>
  <strong>Figure 10: Example of a commercial AR-glasses platform (Rokid Max)</strong>
</p>
<br>



#### Trade Study: Visor Frame  
The weighting rationale prioritizes ergonomic performance and ease of integration for a wearable augmented-reality device.  

- **Ergonomic Comfort (30 %)** ensures the visor remains stable and comfortable during use.  
- **Integration Flexibility (25 %)** accounts for space and accessibility for the HUD, electronics, and cabling.  
- **Weight and Balance (25 %)** minimizes strain during motion.  
- **Manufacturability (10 %)** reflects ease of modification and production cost.  
- **Durability (10 %)** measures long-term resilience under outdoor conditions.  

<p align="center">
  <strong>Table IV</strong>
  <br>
  Visor Frame Trade Study
</p>

| **Criterion** | **Weight (%)** | **Sports-Glasses Frame** | **Custom 3D Frame** | **Commercial AR Base** |
|:--|:--:|:--:|:--:|:--:|
| Ergonomic Comfort | 30 | 5 | 4 | 3 |
| Integration Flexibility | 25 | 4 | 5 | 2 |
| Weight & Balance | 25 | 5 | 4 | 3 |
| Manufacturability | 10 | 5 | 3 | 2 |
| Durability | 10 | 5 | 4 | 4 |
| **Weighted Total (100%)** | **100** | **4.75** | **4.35** | **2.95** |


##### Decision  
The **existing sports-glasses frame** (specifically the Uvex Hypershock or equivalent) is selected as the base for the Smart Golf Visor.  This solution provides the best combination of ergonomic comfort, low weight, durability, and manufacturability while requiring minimal fabrication effort. The proven sports-frame geometry already conforms to human-factor safety and comfort standards, allowing the team to focus design resources on optical and electronic integration.  

Minor mechanical adaptations—such as a clamp-mounted HUD pod, lightweight rear electronics sleeve, and guided cable channels—will transform the frame into a functional smart visor without compromising the existing form factor. This approach accelerates development, reduces cost, and ensures immediate field usability, aligning directly with stakeholder goals for practicality and reliability.  


### App/Software
##### Overview
The Smart Golf Visor system requires an application capable of collecting data from a launch monitor, processing performance metrics, generating coaching insights, and transmitting these results to a Bluetooth-enabled visor display. The software platform must be intuitive, portable, and easily maintainable, allowing golfers to use it on the course without specialized hardware.  

Several potential implementation approaches were evaluated:  
1. **Native mobile application (Android/iOS)**  
2. **Desktop application**  
3. **Web-based application (browser-based Progressive Web App)**  

Each approach offers distinct tradeoffs in development complexity, hardware integration, cost, and user accessibility.  

##### Possible Solutions

###### Native Mobile Application
A **native app** would be developed specifically for Android or iOS platforms using environments such as Android Studio (Java/Kotlin) or Xcode (Swift).  

**Native apps** provide direct access to Bluetooth, device sensors, and local storage. They can maintain persistent connections with the launch monitor and ESP-32 visor, enabling low-latency data streaming.

The **native mobile application** offers several advantages, including full access to Bluetooth Classic and BLE APIs, high performance with low-latency communication, and the ability to function offline while integrating with native notifications or background tasks. However, it also has notable disadvantages: separate development paths are required for Android and iOS, it involves higher maintenance and deployment overhead due to app store updates and platform fees, users must download and install the app manually, and overall development cost and time are increased because of platform-specific design and testing requirements.

**Native apps** are well suited for commercial-grade systems with large budgets but are less ideal for early-stage prototypes or research systems.

###### Desktop Application
A **desktop application** (e.g., built with Python, Electron, or .NET) could serve as a local control center for data acquisition and analysis. It would offer the greatest computing resources and could directly interface with Bluetooth or USB devices.

The advantages of a **desktop application** include simplified debugging and local file access, the potential for powerful analytics and visualization tools, and suitability for lab environments or coaching centers with stationary setups. However, it also has significant disadvantages: poor portability for golfers on the course, the requirement of a laptop or PC with compatible Bluetooth hardware, lack of optimization for quick, touch-based interactions, and reduced convenience for real-time use during practice sessions.

While technically capable, this option lacks the flexibility and mobility needed for outdoor golf applications.

###### Web-Based Application
A **web-based application** built with modern browser APIs represents a hybrid solution that balances accessibility and functionality. By leveraging Web Bluetooth, cloud data storage, and responsive web design, the web app can operate directly from a smartphone browser without installation. It acts as a unified interface between the launch monitor, visor hardware, and cloud database.

The **web-based application** offers several advantages, including cross-platform compatibility, allowing it to run on any device with a supported browser, no installation requirements since users can access it via a secure HTTPS link, and automatic updates for all users simultaneously. It also supports BLE communication through the Web Bluetooth API (in Chrome and Edge), simplifies cloud integration via REST or WebSocket protocols, and provides low development cost with easy scalability. However, it has some disadvantages, including limited Bluetooth support on iOS Safari due to BLE restrictions, the requirement for HTTPS and explicit user permissions for device access, and slightly less control over low-level Bluetooth operations compared to **native applications**.

#### Trade Study: Application

The weightings for the trade study criteria were chosen to reflect the relative importance of each factor in delivering a practical, user-friendly solution for the Smart Golf Visor system. 
- **User accessibility (35%)** was assigned the highest weight because ease of use, portability, and quick deployment are critical for golfers in real-world environments. 
- **Development complexity (25%)** emphasizes the need for an efficient and maintainable system that minimizes development time and cost while allowing for future updates. 
- **Cost (20%)** accounts for both initial development expenses and long-term maintenance considerations, ensuring the solution remains economically viable. Finally, 
- **Performance and Latency (20%)** reflects the importance of responsive, real-time data transfer to support coaching insights and the visor display. 


<p align="center">
  <strong>Table V</strong>
  <br>
  Application Trade Study
</p>

| **Criterion** | **Weight (%)** | **Native Mobile App** | **Desktop App** | **Web-Based App (PWA)** |
|----------------|:--------------:|:-------------------:|:---------------:|:-----------------------:|
| **Development Complexity** | 25% | 2 | 3 | 4 |
| **User Accessibility & Portability** | 35% | 4 | 2 | 5 |
| **Cost** | 20% | 2 | 3 | 5 |
| **Performance & Latency** | 20% | 5 | 5 | 4 |
| **Weighted Total** | **100%** | **3.30** | **3.05** | **4.55** |


##### Decision
After evaluating the alternatives, the **web-based application** is selected as the best overall solution for the Smart Golf Visor system.  

A **web-based application** offers several advantages that make it the most practical and effective solution for this project. It provides cost efficiency by eliminating the need for separate Android and iOS builds, reducing both development and maintenance expenses. Its accessibility and ease of deployment allow users to launch the app instantly from any modern smartphone or laptop without the need for downloads or installations—an ideal feature for field environments like driving ranges where quick setup and minimal friction are essential. Through the Web Bluetooth API, the app can directly connect to BLE-enabled launch monitors and the ESP-32 visor without requiring a native bridge, enabling real-time data processing and immediate transmission to the visor display. The system’s cloud connectivity ensures seamless integration with remote databases and analytics services via HTTPS, supporting session storage, history tracking, and personalized coaching feedback accessible from any device. Additionally, its scalability and maintainability stem from centralized server updates, guaranteeing that all users operate on the latest version without manual intervention or app store approvals. Finally, by leveraging modern web technologies such as React, Tailwind, and Web Bluetooth, the platform delivers an intuitive, mobile-responsive user experience that captures the functionality of a native app while maintaining the simplicity and universality of a browser-based interface.

While native and desktop applications provide certain technical advantages, they introduce unnecessary complexity, platform dependency, and deployment challenges.  
A **web-based Progressive Web App** offers the most balanced solution — providing broad device compatibility, low cost, real-time Bluetooth communication, and portability within a single system.  


### Heads-Up Display

##### Overview
The Heads-Up Display (HUD) is responsible for generating and projecting a clear graphical interface presenting key data metrics, including ball speed, ball distance, launch angle, spin rate, smash factor, shot direction, and coaching suggestions. It integrates these elements into the user’s real-world view using a combination of hardware, including the display and tracking sensors, which ensure the HUD remains correctly aligned with the user’s line of sight, and supporting software that processes sensor data, manages visualization, and corrects for drift or misalignment.

##### Possible Solutions
###### Display
The selection of the display will depend on factors such as outdoor brightness and contrast for visibility, response time and refresh rate, as well as overall cost. The following are possible option for display types commonly used in augmented reality applications:
1) **Micro-OLED**
2) **Micro-LED**
3) **Micro-LCD** 

**Micro-OLED** displays offer exceptional contrast ratios and deep black lights, resulting in excellent visual clarity under moderate lighting conditions however, their brightness typically ranges from 1,000 to 5,000 nits, which can be limiting in direct sunlight. They offer extremely fast response times (under 1 ms) and support high refresh rates that range between 60 and 120 Hz making them ideal for displaying dynamic data metrics. In terms of cost, **Micro-OLEDs** typically range from $80 to $200 depending on the resolution and refresh rate, offering a solid balance between image quality, responsiveness and affordability for near-eye displays.

<p align="center">
  <img src="https://hackmd.io/_uploads/BJiQgi7yZx.png" alt="Micro-OLED Display" style="width:20%; border:1px #eee; padding:1px; margin:20px;">
  <br>
  <strong>Figure 11: Micro-OLED Display</strong>
</p>


**Micro-LED** displays deliver the highest brightess often exceeding 10,000 nits making them the best option for outdoor visibility and direct sunlight operation. Like **Micro-OLED**, **Micro-LEDs** feature under 1 ms response times and can provide refresh rates above 120 Hz providing superior visual stability. However, the cost is very high due to its complex manufacturing process where one display typically ranges from $500 to over $1,000. **Micro-LED** technology offers unmatched brightness and durability, but is evident it is less practical for cost-sensitive and developmental projects.

<p align="center">
  <img src="https://hackmd.io/_uploads/SJGlNs7kZe.png" alt="Micro-LED Display" style="width:30%; border:1px #eee; padding:1px; margin:20px;">
  <br>
  <strong>Figure 12: Micro-LED Display</strong>
</p>


**Micro-LCD** panels rely on a backlight and can achieve brightness levels between 2,000 and 8,000 nits making them an acceptable option for outdoor use under moderate sunlight. However, they offer lower contrast ratios and less vivid colors because the pixels do no generate their own light like for the other two displays. Response times are also slower where it typically ranges from 2-10 ms with refresh rates between 60 and 90 Hz, which can cause minor motion blur. They are the most cost-effective with prices ranging from $10 to $80 per module and are a widely available option. This makes them suitable for low-cost prototypes with low expected optimal performance.

<p align="center">
  <img src="https://hackmd.io/_uploads/H1ftUj71We.png" alt="Micro-LCD Display" style="width:50%; border:1px #eee; padding:1px; margin:20px;">
  <br>
  <strong>Figure 13: Micro-LCD Display</strong>
</p>

#### **Trade Study: Display**

The weighting rationale prioritizes ergonomic performance and ease of integration for a wearable augmented-reality device. 
- **Outdoor Brightness & Contrast (40 %)**  ensures clear visibility in direct sunlight, which is critical for maintaining display readability and a high-quality user experience on the course
- **Response Time & Refresh Rate (35 %)** provides smooth, real-time visuals with minimal latency during head and eye movements.
- **Cost (25 %)** balances performance and affordability, ensuring the chosen display is feasible for development while still delivering high-quality visual performance.
<p align="center">
  <strong>Table VI</strong>
  <br>
  Display Trade Study
</p>

| **Criterion**                 | **Weight (%)** | **Micro-OLED** | **Micro-LED** | **Micro-LCD** |
|-------------------------------|:-------------:|:--------------:|:-------------:|:-------------:|
| Outdoor Brightness & Contrast | 40            | 4              | 5             | 3             |
| Response Time & Refresh Rate  | 35            | 5              | 5             | 3             |
| Cost                          | 25            | 4              | 1             | 5             |
| **Weighted Total (100%)**     | **100**       | **4.40**       | **3.80**      | **3.40**      |


###### Tracking Sensor
The tracking sensor will be selected based on key factors such as accuracy responsiveness, and environmental robustness. The following are the two possible options:
1) **Inerital Measurement Units (IMUs)**
2) **Cameras**



**IMUs** are compact, lightweight sensors that combine accelerometers, gyroscopes and sometimes magnetometers to measure linear acceleration, angular velocity, and orientation. IMUs offer strong response times with latency typically being around 1 to 5ms making them highly responsive for real-time applications. Accuracy is determined using root mean square error (RMSE), which quantifies the average difference between the sensor's measurements and the true values. Typical **IMU** RMSE values are around 1.4 to 4.4 degress and positional errors average around 35 mm indicating small deviations fomr actual motion. **IMUs** are highly robust in outdoor environments because they do not rely on lighting or line-of-sight however, they can experience cumulative drift over time which may reduce long-term position accuracy without periodic calibration. Their combination of accuracy, responsiveness, and environmental robustness makes them a good options for real-time visor tacking in outdoor environments.

<p align="center">
  <img src="https://hackmd.io/_uploads/rydHth7ybe.png" alt="IMU Tracking Sensor" style="width:50%; border:1px #eee; padding:1px; margin:20px;">
  <br>
  <strong>Figure 14: IMU Tracking Sensor</strong>
</p>




**Camera tracking systems** determine the position and orientation by analyzing data from one or more cameras. They achieve high accuracy by detecting and tracking distinctive visual features in the environemnts, often using multiple cameras. This allows angular errors to be as low as 1 to 5 degress and positional errors under 10 mm in ideal conditions. However, camera systems are highly sensitive to outdoor environmental factors such as sunglight, glare, shadows, and occlusions which can significantly degrade both accuracy and responsiveness. The response also tend to be slower with it typically being around 10 to 50 ms. While camera systems excel in controlled indoor environments, their effectiveness in outdoor enviroments is limited making them a less reliable option.

<p align="center">
  <img src="https://hackmd.io/_uploads/HJRL9nXk-x.png" alt="High-speed Camera Tracking Sensor" style="width:50%; border:1px #eee; padding:1px; margin:20px;">
  <br>
  <strong>Figure 15: High-speed Camera Tracking Sensor</strong>
</p>


#### **Trade Study: Tracking Sensor**
The weighting rationale for this trade study is based on the performance requirements of an outdoor tracking system for augmented-reality applications:
- **Accuracy (45 %)**  ensures precise motion and orientation tracking, which is essential for maintaining alignment between the digital overlay and the user’s real-world view.
- **Responsiveness (40 %)** provides minimal latency and smooth tracking performance during fast head movements.
- **Environmental Robustness (15 %)** ensures reliable operation in varying outdoor lighting and weather conditions without performance degradation.
<p align="center">
  <strong>Table VII</strong>
  <br>
  Tracking Sensor Trade Study
</p>

| **Criterion**                 | **Weight (%)** | **IMU** | **Camera-Based Tracking** |
|:------------------------------|:-------------:|:-------:|:------------------------:|
| Accuracy                      | 45            | 4       | 5                        |
| Latency                       | 40            | 5       | 3                        |
| Environmental Robustness      | 15            | 5       | 2                        |
| **Weighted Total (100%)**     | **100**       | **4.55**| **3.85**                 |

##### Decision
After evaluating multiple displays and tracking sensor technologies, the following options were selected:
1) **Micro-OLED display**
2) **Intertial Measurement Unit (IMU) tracking sensor**

The **Micro-OLED display** provides the best overall balance of outdoor visibility, response time, refresh rate, and cost making it highly suitable for projecting real time performance metrics in an outdoor augmented reality environment. It delivers sharp visuals, fast refresh performance, and strong color contrast while maintaining reasonable affordability for a wearable prototype.

The **IMU** was chosen for the tracking sensor due to its low latency, accuracy, and environmental robustness. It provides reliable orientation and motion data without being affected by outdoor lighting or line-of-sight ensuring consistent tracking. Although **IMUs** can experience drift over time, this can be minimized through software calibration.

Together, the **Mirco-OLED display** and **IMU** form a lightweight, efficient, and cost-effective combination that supports responsive, high-quality visualization and accurate head tracking for the Smart Golf Visor

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
  <img src="https://hackmd.io/_uploads/rkTk9hSkZx.png" alt="Smart Golf Visor Hardware Block Diagram" style="width:90%; border:1px #eee; padding:1px; margin:20px;">
  <br>
  <strong>Figure 16: Smart Golf Visor Hardware Block Diagram</strong>
</p>

### Operational Flow Chart

<p align="center">
  <img src="https://hackmd.io/_uploads/B1eox-XRgx.png" alt="User Operational Flow Chart" style="width:90%; border:1px #eee; padding:1px; margin:20px;">
  <br>
  <strong>Figure 17: User Operational Flow Chart</strong>
</p>

## Atomic Subsystem Specifications

The Smart Golf Visor is composed of six primary subsystems: Launch Monitor, Communications, Visor Design, Visor Powering System, App/Software Development, and Heads-Up Display.

### Subsystem 1: Launch Monitor
#### Subsystem Overview 

The **Launch Monitor subsystem** acquires per-shot telemetry exclusively from the PiTrac do-it-yourself (DIY) launch monitor [19] and delivers a normalized Shot record to the application and the visor head-up display (HUD) with sub-second latency. PiTrac runs on a Raspberry Pi and uses two Mobile Industry Processor Interface (MIPI) global-shutter cameras with infrared (IR) strobing to capture the golf ball’s motion during the first frames of flight. By adopting PiTrac’s existing sensing pipeline and its published local interfaces, the subsystem does not redesign the measurement path; instead, it consumes PiTrac’s output and exposes a single, versioned message to all downstream components. Telemetry is ingested from the PiTrac web server over WebSocket (WS) or Representational State Transfer (REST), or from PiTrac’s Golf Simulator Pro (GSPro) Open Connect stream over Transmission Control Protocol (TCP). The bridge validates and time-orders the incoming messages, converts all values to International System of Units (SI), and publishes a stable Shot record to the HUD.

#### Subsystem Operation

At the start of a session, the bridge chooses how to listen to PiTrac (WebSocket/REST or GSPro Open Connect over TCP). It verifies the link and then enters an armed state. When PiTrac detects a shot, it sends one or more messages. The bridge parses those messages, checks the values, and converts every field to SI units. It assigns two timestamps: a monotonic timestamp for ordering and latency, and a Coordinated Universal Time (UTC) timestamp for logging. It also de-duplicates messages inside a short window so one Shot record represents the swing. That normalized Shot is sent over a local WebSocket to the HUD. The HUD draws a fixed, high-contrast layout on the visor within the latency budget. While the session is running, the subsystem watches link health, rotates logs, and reconnects automatically if the link drops. When the session ends, it closes connections and flushes the logs. The bridge can run as an app on the host (which also drives the wired display), or as a small embedded Linux service that listens for PiTrac traffic and republishes normalized shots to HUD clients on the local network. The subsystem must move telemetry over WS/REST or GSPro Open Connect, must produce exactly one Shot per swing, and must meet an impact-to-HUD time of 500 ms or less at the 95th percentile (about 50 ms for ingest/normalize and about 100 ms for publish-to-paint). It must also preserve ordering with a monotonic clock and must not freeze the user interface.

The launch monitor requires its own battery and power path so PiTrac can run untethered at the range for 4–6 hours. The load is about 10–12 W (Raspberry Pi 4, two MIPI global-shutter cameras, IR strobe/LED driver, and networking), so the design will use a single 3S (11.1 V nominal, 12.6 V full) Li-ion pack at 6 Ah (~90–100 Wh). A 3S BMS provides over-voltage, under-voltage, over-current, and over-temperature protection and disconnects the pack before low voltage can brown out the Pi. A high-efficiency 12 V → 5 V @ 4 A buck creates the main 5 V rail and is sized so strobe pulses do not pull the rail below 4.8 V; the strobe/COB LED can be isolated with a small buck or local capacitance. Power-up is BMS OK → 5 V enable → Pi boot → cameras/strobe enable. The pack is recharged through a 12.6 V CC/CV USB-C or RC-style charger. With ~90–100 Wh and 85–90% conversion efficiency, this supports ≥6 hours in warm conditions(68–86 °F) or 5 hours in cold conditions (32–50 °F ), which covers an 18-hole round with margin. 

#### Interfacing
<p align="center">
  <strong>Table VIII</strong>
  <br>
  Launch Monitor and Interfacing
</p>

| Interface       | Connected Subsystem  | Type                    | Dir.   | Protocol / Standard                   | Description                                                                                           |
|-----------------|-------------------|------------------------|--------|--------------------------------------|-------------------------------------------------------------------------------------------------------|
| App / Software  | Communications     | Wireless Communication | Output | WebSocket (WS) / REST (HTTP + JSON) | Receives real-time shot events over WS and emits normalized Shot records to the HUD/app             |



#### Subsystem “Shall” Statements

1. The subsystem **shall** acquire per-shot telemetry from PiTrac using WebSocket and REST, or GSPro Open Connect over TCP.
2. The subsystem **shall** normalize all inputs to a single Shot schema, shall convert all quantities to SI units, and shall assign both a monotonic timestamp and a UTC timestamp to each record.
3. The subsystem **shall** ensure one record per swing by de-duplicating near-identical messages within a bounded debounce window.
4. The subsystem **shall** publish normalized Shot records to the visor HUD through a local WebSocket using a versioned JSON contract.
5. The subsystem **shall** meet a ninety-fifth-percentile end-to-end latency of no more than 500 milliseconds from impact to on-visor render under nominal conditions.
6. The subsystem **shall** automatically detect link loss, shall reconnect with exponential back-off, and shall continue operation without user intervention.
7. The subsystem **shall** record rotating diagnostic logs of raw messages, normalized records, timing measurements, and reconnect events for testing and analysis.
8. The subsystem **shall** protect all stored credentials and keys using secure storage provided by the operating system.

<p align="center">
  <img src="https://hackmd.io/_uploads/B1Up58KRxe.png" alt="Data Acquisition and Interface Flowchart" style="border:1px #eee; padding:1px; margin:20px;">
  <br>
  <strong>Figure 18: Data Acquisition and Interface Flowchart</strong>
</p>

### Subsystem 2: Communications
#### Overview and Function 
The **Communication subsystem** transmits the measured data captured by the launch monitor to all relevant subsystems. It utilizes wireless bluetooth and wired communication to ensure reliable data transfer for accurate metric display and data storage for future analysis.

#### Subsystem Operation

At the start of a session, the golfer will need to establish a Bluetooth connection from their device with both the custom-built launch monitor and the visor. Following the measurement of the golfer’s swing, the application retrieves the corresponding data from the launch monitor via Bluetooth Low Energy (BLE) communication. Upon receiving the data, the application forwards it to the visor, effectively serving as a data relay. The visor’s embedded microcontroller integrates the received shot metrics with measurements from the onboard IMU (inertial measurement unit) and subsequently drives the HUD (head-up display) output on the mini OLED module.

#### Interfacing
<p align="center">
  <strong>Table IX</strong>
  <br>
  Interfaces and Signal Definition
</p>

| Interface                 | Connected Subsystem | Type               | Dir.   | Protocol / Standard | Description                                                                 |
|---------------------------|------------------|------------------|--------|-------------------|----------------------------------------------------------------------------|
| **ESP32-P4**              | Power System      | Power             | Input  | DC                 | Receives regulated 3.3 V to operate and up to 500 mA for wireless transmission. |
| **Selected Launch Monitor** | App             | Wireless Comm.    | Output | Bluetooth (2.4 GHz)| Sends selected data to the app.                                           |
| **ESP32-P4**              | HUD/Display       | Serial Comm.      | Output | MIPI DSI           | Sends HUD graphic to the Micro-OLED displays.                              |
| **ESP32-P4**              | HUD/Display       | Serial Comm.      | Input  | SPI                | Receives acceleration, angular velocity, and orientation data from IMU.    |


#### Summary of “Shall” Statements

1. The selected launch monitor, app, and visor  **shall** wireless communicate shot metrics via BLE (Bluetooth Low Energy) at 2.4 GHz.
2. The microcontroller **shall** wirelessly receive the shot metrics via BLE (Bluetooth Low Energy) at 2.4 GHz.
3. The microcontroller **shall** be supplied and operate with 3.3V power signal.
4. The IMU **shall** use SPI serial protocol to interface with the ESP32-P4.
5. The mini OLED display **shall** use 2-lane MIPI DSI interfacing with the ESP32-P4 due to board restrictions.
6. All hardware **shall** be designed to integrate with the Physical Visor Design Subsystem for structural compatibility.

<p align="center">
  <img src="https://hackmd.io/_uploads/HkoFKbi0xx.png" alt="Communication Flowchart" style="border:1px #eee; padding:1px; margin:20px;">
  <br>
  <strong>Figure 19: Communication Flowchart</strong>
</p>

### Subsystem 3: Visor Design
#### Overview and Function
The **Visor subsystem** provides a wearable heads-up display (HUD) that projects launch-monitor telemetry directly into the golfer’s field of view. It integrates optical, mechanical, and electronic components into a balanced, ergonomic platform that maintains safety, clarity, and comfort. The design ensures minimal visual obstruction, fast response time, and compliance with ISO 15004-2 optical safety standards.

##### Exploded Parts List (by Function)

Base Frame: Uvex Hypershock safety glasses — structural carrier for optics and mounts.

HUD Optical Engine: 0.49″ 1920×1080 Micro-OLED display, plano-convex collimating lens (20–25 mm focal length), and AR-coated beam-splitter prism (≈ 30R/70T) for bright, clear projection.

HUD Pod Housing: 3D-printed ABS/PC shell protecting optics and providing pitch/yaw adjustments.

Inertial Measurement Unit (IMU): 9-degree-of-freedom sensor for head-movement stabilization, interfaced via SPI with MCU.

Rear Electronics Sleeve: Houses Li-Po battery, BMS, MCU board, converters, and USB-C port; balances front optics.

Elastic Retention System: Dual-strap system distributing load evenly and ensuring stability.

Cabling and Guides: Strain-relieved, low-profile cables routed for safety and unobstructed vision.

Environmental Features: AR coatings, anti-glare film, and moisture-resistant padding.

#### Operation

When powered on, the visor initializes its 3.3 V and 1.8 V rails, activates the IMU, and lights the Micro-OLED display. The user adjusts tilt and eye relief (18–22 mm) for a clear, focused image with ≥ 80 % visibility. During use, shot data from the Communications subsystem is projected through the collimating lens and AR-coated combiner, overlaying ball metrics in real time. The IMU stabilizes the display to remain steady with head movement. Power and control electronics housed in the rear sleeve balance weight and manage heat, keeping external surfaces below 41 °C. On shutdown, the system sequentially disables the OLED, IMU, and communications links, then isolates the Li-Po battery for safe storage.

##### Fit and Ergonomics 

The visor provides an adjustable eye relief ranging from 18 to 22 mm, allowing users to achieve optimal focus and comfort.

The field of view spans approximately 14–18°, with no more than 10% central occlusion to maintain a clear sightline.

The eyebox measures at least 8 × 6 mm, ensuring consistent visuals across typical head movements.

The maximum surface temperature remains below 41 °C even after 60 minutes of continuous operation, preventing user discomfort.

The balanced Y-split straps distribute weight evenly across the head, improving comfort and stability during motion.

#### Interfacing  
<p align="center">
  <strong>Table X</strong>
  <br>
  Interfaces and Materials
</p>

| **Part**         | **Material**     | **Mass (g)** | **Interfaces**     |
|:-----------------|:----------------|:------------:|:-----------------|
| **Frame clamp**  | PA12 / CF-Nylon | ≤ 12         | Frame ↔ HUD       |
| **HUD pod**      | ABS / PC        | ≤ 25         | OLED, IMU         |
| **Rear sleeve**  | Softshell + ABS | ≤ 110        | USB-C, straps     |
| **Elastic straps** | Nylon elastic | ≤ 30         | Temples ↔ sleeve  |
| **Cable set**    | FFC / FPC       | ≤ 10         | Sleeve ↔ HUD      |


#### Visor design "shall" statements

1. The visor **shall** mount a monocular HUD on the right eye with adjustable eye relief and ≥ 80 % visibility.
2. The optical engine **shall** include a Micro-OLED, collimating lens, and AR-coated combiner.
3. The rear sleeve **shall** counterbalance the HUD and contain all electronics.
4. Cable routing **shall** avoid obstructing the golfer’s view.
5. External surfaces **shall** remain under 41 °C and be smooth and padded.
6. The visor **shall** meet IP5x water and dust resistance requirements.

Physical Layout Disclaimer
Designs and dimensions are conceptual and may be refined through physical prototyping and ergonomic validation.


### Subsystem 4: Visor Powering System
#### Overview and Function
The **Power Subsystem** converts energy from a single-cell LiPo (1S, 3.7 V nominal) into regulated rails for the ESP32-P4 (3.3 V), IMU (3.3 V), and OLED HUD. It provides full protection, safe charging per cited standards, and is designed to last beyond a full round of golf (≥ 18 holes with cold-weather margin) using a 5000–6000 mAh LiPo cell.

The subsystem outputs 3.3 V for logic and sensors, 1.8 V for display logic, 5.0 V for USB accessories or the 0.39″ OLED, and display-specific analog rails:  
- 0.39″ OLED: Vin 2.5–5.5 V, VDDI 1.65–1.95 V  
- 0.49″ OLED: AVDD 5.3–5.5 V, AVEE −4 to −5.5 V, VDDI 1.65–1.95 V; power ≈ 468 mW  

A 1S LiPo BMS provides Over-Voltage (OV), Under-Voltage (UV), Over-Current (OCP), and Over-Temperature (OTP) protections [2]. A USB-C charging path (5 V input) supports CC/CV (Constant Current / Constant Voltage) charging [3], holding constant current until 4.2 V, then constant voltage as current tapers for a safe full charge.  

An inline fuse adds overcurrent protection, and the design follows NEC and NESC safety standards for low-voltage, battery-powered systems.

#### Operation

At startup, the power subsystem activates when the user presses the power button or connects a USB-C charger. The Battery Management System (BMS) immediately verifies cell voltage and temperature before enabling the power rails. Once validated, the MCU sequentially powers the required domains — 3.3 V, 1.8 V, and the ± display analog rails — following the OLED vendor’s recommended timing for VDDI, AVDD, and AVEE initialization. This ensures stable startup conditions and prevents voltage overshoot or inrush that could damage the display or logic circuitry.

When operating in charging mode, the system uses a USB-C 5 V input to power a CC/CV (Constant Current / Constant Voltage) charging controller managed by the BMS. Charging occurs in two distinct phases: during the Constant Current (CC) phase, current remains fixed while the battery voltage gradually increases; once the voltage reaches approximately 4.2 V, the Constant Voltage (CV) phase begins, holding voltage steady while current tapers to a safe cutoff. The BMS also enforces a temperature gate that inhibits charging below 0 °C or above 45 °C, ensuring safe charging under varying ambient conditions. The charge rate is set between 0.5 C–1 C, as defined by the cell datasheet, corresponding to a full charge in 1–2 hours depending on battery capacity.

During normal operation, the subsystem continuously regulates and distributes power to all rails while monitoring for fault events. The BMS protects against Over-Voltage (OV), Under-Voltage (UV), Over-Current (OCP), and Over-Temperature (OTP) conditions, triggering shutdown or current limiting when thresholds are exceeded. Power delivery circuits remain active only when enabled by the MCU’s domain-control logic, allowing efficient energy use during low-power states or idle conditions. The user interface consists of a power button, USB-C charge port, and LED indicators showing charging, full-charge, and fault status. Together, these functions ensure safe and reliable energy delivery for a full 18-hole golf session, including performance margins for extended or cold-weather use.

##### Primary Functions

* Regulate and distribute 3.3 V, 1.8 V, 5 V, and OLED analog power rails.  
* Manage charging via CC/CV control and monitor OV, UV, OCP, and OTP fault conditions through the BMS.  
* Provide power-enable domain control to optimize runtime and reduce idle power consumption.  
* Maintain compliance with NEC and NESC standards for low-voltage and battery-powered systems.  
* Ensure stable operation and safe charging through controlled startup sequencing and temperature-gated protection.

#### Power & Efficiency Assumptions

Representative converter efficiencies (to be verified in detailed design):
- 3.3 V buck-boost from 1S: η ≈ 90–93 %.  
- 5.0 V boost from 1S: η ≈ 90–93 %.  
- +5.4 V boost (OLED AVDD):** η ≈ 88–92 %.  
- −5.4 V inverting (OLED AVEE): η ≈ 85–90 %.  
- 1.8 V regulator (VDDI):  
  - LDO from 3.3 V: η ≈ 55 % (acceptable if I is low).  
  - Buck from 1S: η ≈ 90–93 % (if I > 150 mA).

Display power:
- 0.49″ OLED: ≈ 468 mW at 90 Hz, 1800 nits (worst-case full white).  
- 0.39″ OLED: assume ≈ 300–450 mW (similar conditions, use ≈ 400 mW for budget).

IMU power:  
- The Inertial Measurement Unit (IMU) operates from the 3.3 V rail shared with the ESP32-P4.  
- Typical IMU current draw is 3–6 mA during full 6-axis sampling and up to 10 mA peak during data bursts.  
- This equates to approximately 15–33 mW average consumption, which is negligible compared to the compute and display loads but should still be included in total current budgeting on the 3.3 V line.  
- Efficiency through the 3.3 V buck-boost remains in the 90–93 % range, so IMU conversion losses are minimal (< 2–3 mW).  
- To minimize drift and noise, the IMU supply will use low-ripple 3.3 V filtering (LC or ferrite bead isolation) consistent with sensor manufacturer guidance.


**Pack energy:**  
5 000–6 000 mAh @ 3.7 V → 18.5–22.2 Wh nominal.  
Include ≥ 20 % reserve and cold-weather derate for reliable 18-hole operation.

#### Interfacing
<p align="center">
  <strong>Table XI</strong>
  <br>
  Interfaces and Signal Definition
</p>

| Interface                     | Connected Subsystem          | Type     | Dir.   | Protocol / Standard | Description                                                                                          |
|-------------------------------|-----------------------------|---------|--------|-------------------|------------------------------------------------------------------------------------------------------|
| **3.3 V Rail (buck-boost)**   | ESP32-P4, IMU, logic        | Power    | Output | DC                | Up to **600 mA** (headroom). Low ripple for RF/IMU.                                                 |
| **1.8 V Rail (LDO or buck)**  | OLED VDDI                   | Power    | Output | DC                | **150–300 mA** budget (LDO if low I; buck if ≥150 mA).                                             |
| **5.0 V Rail (boost)**        | USB accessory / OLED AVDD (0.39″) | Power | Output | DC                | Generated by dedicated boost.                                                                        |
| **AVDD (+5.3–5.5 V)**         | OLED analog (0.49″)        | Power    | Output | DC                | Generated by dedicated boost.                                                                        |
| **AVEE (−4 to −5.5 V)**       | OLED analog (0.49″)        | Power    | Output | DC                | Generated by inverting converter / charge-pump.                                                     |
| **USB-C Charge In (5 V)**     | External charger            | Power    | Input  | USB-C             | 5 V input up to **0.5 C – 1 C A** into 1S CC/CV charger.                                           |
| **Charge / Full Indication**  | BMS → MCU                  | Digital  | Output | GPIO              | Pack charging/full status.                                                                           |
| **Power-Enable Lines**        | MCU → rails                | Digital  | Input  | GPIO              | Enables per-domain rails for low-power.                                                             |
| **Protection / Fault**        | Power → MCU                | Digital  | Output | GPIO              | **OV (Over-Voltage), UV (Under-Voltage), OCP (Over-Current), OTP (Over-Temperature)** asserted → safe shutdown. |

### Power Subsystem “Shall” Statements

1. The power subsystem **shall** use a single-cell (1S) LiPo battery rated at 3.7 V nominal and 5 000–6 000 mAh, providing at least six hours of continuous operation.
2. The power subsystem **shall** generate regulated outputs of 3.3 V ±5 %, 1.8 V ±5 %, and OLED-specific analog rails as required.  
   - 0.39″ OLED: Vin 2.5–5.5 V, VDDI 1.65–1.95 V  
   - 0.49″ OLED: AVDD 5.3–5.5 V, AVEE −4 to −5.5 V, VDDI 1.65–1.95 V
3. The subsystem **shall** comply with NESC and NEC low-voltage standards for grounding, overcurrent protection, and insulation.
4. The subsystem **shall** include a BMS providing OV, UV, OCP, and OTP protection during charge and discharge.
5. The subsystem **shall** include an inline fuse rated ≥ 1.5× the maximum steady-state current to prevent overcurrent faults.
6. The subsystem **shall** inhibit charging below 0 °C or above 45 °C and disable discharge under under-voltage conditions.
7. User-touchable surfaces **shall** not exceed 41 °C after 60 minutes at 25 °C ambient temperature.
8. The subsystem **shall** meet NEC Article 480 and NESC Section 12 requirements for safe battery installation and short-circuit protection.
9. The subsystem **shall** accept USB-C 5 V input and perform CC/CV charging per manufacturer specs.  
   - In CC mode, current remains constant as voltage rises.  
   - In CV mode, voltage is held while current tapers to a safe cutoff near 4.2 V.
10. The subsystem **shall** limit charge current to the manufacturer’s rated C-rate (0.5–1 C), where 1 C = 6 000 mA for a 6 000 mAh cell.
11. The subsystem **shall** achieve ≥ 90 % efficiency for the 3.3 V and 5 V converters, ≥ 88 % for +5.4 V, ≥ 85 % for −5.4 V, and ≥ 90 % for 1.8 V regulation.
12. The subsystem **shall** include margin calculations for converter losses, battery derating, and safety headroom per NESC best practices.


---
### Power Subsystem Flowchart
<p align="center">
  <img src="https://hackmd.io/_uploads/Byrf7_nRgx.png" alt="Power Subsystem Flowchart" style="width:99%; border:1px #eee; padding:1px; margin:20px;">
  <br>
  <strong>Figure 20: Power Subsystem Flowchart</strong>
</p>





### Subsystem 5: App/Software

#### Overview and Function  
The **Application Subsystem** serves as the user interface and data management hub for the Smart Golf Visor system. It enables golfers to view real-time and historical performance data, receive automated coaching insights, and maintain connectivity between the launch monitor, visor hardware, and cloud-based data storage. The subsystem is designed for compatibility across mobile and desktop browsers, optimized for smartphones for use on the course. The ideal way to accomplish this is with a web application written in Python and hosted via Streamlit Cloud. Streamlit Cloud offers a Community Cloud, which allows users to host web applications from a Github repository. These applications can then be accessed from any internet-enabled mobile device. Streamlit apps use Hypertext Transfer Protocol Secure (HTTPS) for an encrypted connection between the web app and the user's browser. 

The web-based application shall provide an intuitive user interface to display the key performance metrics, including ball speed, carry distance, launch angle, spin rate, and smash factor. It shall integrate Bluetooth connectivity to communicate with both the launch monitor and the microcontroller-based visor module. The system shall process and store shot data in a secure cloud database to allow session tracking and post-round review. Additionally, it shall generate automated coaching insights using the processed shot data to suggest performance adjustments. Finally, the application shall transmit numerical metrics to the visor for real-time display.

#### Operation  
When a user accesses the **application**, they are presented with a **mobile-friendly dashboard interface** featuring key options. Users can **Start New Session** to begin receiving metrics from the **launch monitor** and sending them to the **visor**, or they can **View History**, which loads past session data and statistical summaries from the **cloud database**. Within the history view, **Performance Insights** are provided, displaying trend analysis and coaching tips derived from session metrics. During an active session, incoming data packets from the **launch monitor** are processed in real-time. The **app** parses and filters the metrics, which are then stored securely in the **cloud database** and streamed via **Bluetooth** to the **visor microcontroller** for HUD viewing. Upon completion of a session, users can later review session metrics and suggested practice focus areas.



#### Interfacing

<p align="center">
  <strong>Table XII</strong>
  <br>
  Application Interfaces with Other Subsystems
</p>

| Interface        | Connected Subsystems | Type                   | Dir.   | Protocol / Standard        | Description                                                                                                                                                                                        |
|-----------------|-------------------|-----------------------|--------|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Launch Monitor** | Communications   | Wireless Communications | Input  | Bluetooth Low Energy (BLE) | Receives real-time numerical shot data: ball speed, carry distance, launch angle, spin rate, and smash factor. Sends handshake to establish data streaming.                                        |
| **Smart Visor**    | Communications   | Wireless Communications | Output | Bluetooth Low Energy (BLE) | Sends numerical summary metrics for current swing: ball speed, carry distance, launch angle, spin rate, and smash factor. Receives status acknowledgment or readiness signal from microcontroller. |


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

#### Flowchart
<p align="center">
  <img src="https://hackmd.io/_uploads/B1UDWjoCxe.png" alt="App Flow Chart" style="width:700px; border:1px #eee; padding:1px; margin:20px;">
  <br>
  <strong>Figure 21: Flow Chart showing operation and user interaction with the application</strong>
</p>


### Subsystem 6: Heads-Up Display
#### Overview and Function
The **Heads-Up Display (HUD) subsystem** is responsible for clearly projecting both the real-world environment and the HUD graphic interface, which displays the following data metrics: ball speed, ball distance, launch angle, spin rate, and smash factor- along with shot direction and coaching suggestions. 

The HUD combines both hardare and software components to deliver real-time feedback with minimal delay, ensuring the user maintains visual clarity and responsiveness during operation. The HUD interfaces with the following subsystems: Power, Communications, and Physical Visor Design. 


#### Operation 
##### Hardware Operation
The HUD hardware consists of three primary components:  a microcontroller, a tracking sensor (IMU), and a micro-OLED display. Each component plays a critical role in ensuring smooth data processing and accurate, real-time visual output

##### *Microcontroller*
The microcontroller shall provide the processing power of the HUD. It shall process incoming data from both the IMU sensor and the app/software subsystem and drive the display at a high speed to ensure minimal delay. The microcontroller shall interface with the system through a regulated 3.3 V DC input power signal.

It shall also interface with the Communications subsystem by receiving input data via Bluetooth wireless communication from the app/software and receiving motion data via SPI serial communication from the IMU. The selected microcontroller, the ESP32-P4, provides superior processing speed of 400 MHz clock speed and large storage capacity of 768 KB of SRAM, and 16–32 MB of flash memory. Its high performance makes it an optimal choice to ensure smooth and efficient HUD operation with no perceptible delay

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

##### Software Operation
The HUD software shall be written in C and stored on the ESP32 based microcontroller. It shall use an microcontroller display library to generate the graphic intergface. The graphic interface shall display:
* Data metrics (ball speed, ball distance, launch angle, spin rate, smash factor)
* Shot direction
* Coaching suggestion based on processed performance data
Below is an example of the expected HUD layout that the Smart Golf Visor Team has come up.
<p align="center">
  <img src="https://hackmd.io/_uploads/r174n9Apxe.jpg" alt="HUD Graphic Interface Illustration" style="width:80%; border:1px #eee; padding:1px; margin:20px;">
  <br>
  <strong>Figure 22: HUD Graphic Interface Illustration</strong>
</p>

#### Interfacing

<p align="center">
  <strong>Table XIII</strong>
  <br>
  HUD Interfaces and Signal Definition
</p>

| Interface   | Connected Subsystem | Type       | Dir.  | Protocol / Standard | Description                                                                                       |
|------------|-------------------|-----------|-------|-------------------|---------------------------------------------------------------------------------------------------|
| **ESP32**  | Power             | Power     | Input | DC                | Receives regulated 3.3 V to operate.                                                             |
| **IMU**    | Power             | Power     | Input | DC                | Receives regulated 3.3 V to operate.                                                             |
| **IMU**    | Communications    | Serial    | Output| SPI               | Sends acceleration, angular velocity, and orientation data to ESP32.                             |
| **Micro-OLED** | Power          | Power     | Input | DC                | Receives 1.8 V to power digital logic.                                                          |
| **Micro-OLED** | Power          | Power     | Input | DC                | Receives 5 V or 5.3 to 5.5 V (AVDD) and -4 V to -5.5 V (AVEE) to operate depending on the display used. |
| **Micro-OLED** | Communications | Serial    | Input | MIPI-DSI          | Receives HUD graphics for display.                                                              |


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
<p align="center">
  <img src="https://hackmd.io/_uploads/rJC7dW70ge.png" alt="Heads-Up Display Subsystem Flowchart" style="width:80%; border:1px #eee; padding:1px; margin:20px;">
  <br>
  <strong>Figure 23: Heads-Up Display Subsystem Flowchart</strong>
</p>

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

<p align="center">
  <strong>Table XIV</strong>
  <br>
  Standards & How They Inform Design
</p>

| Area                       | Standard / Guidance                                               | Relevance to The Design                              | Design Impact                                                                                              |
|----------------------------|------------------------------------------------------------------|-----------------------------------------------------|-----------------------------------------------------------------------------------------------------------|
| **Bluetooth link**         | IEEE 802.15.1 [7]                                               | PAN connectivity to phone/monitor                  | RF band noted (2.4 GHz), coexistence testing, pairing UX constraints                                      |
| **Battery safety**         | IEC 62133-2 (rechargeable cells) [6]; **NEC Article 480**; **NESC Part 1, Section 12** | Safe cell selection, wiring, and pack behavior      | BMS w/ OV/UV/OCP/OTP; fuse on pack; charge temp window; wiring gauge per NEC; protective enclosure       |
| **Eye/optical safety**     | ISO 15004-2 (ophthalmic—light hazard) [18]                     | Near-eye luminance & spectral safety               | Luminance & exposure limits ≤ 2,000 nits; exposure under 10,000 lux                                      |
| **Ingress**                | IEC 60529 (IP rating) [6]                                       | Dust/sweat/light rain robustness                   | Target **IP54–IP67**; test dust & splash conditions                                                      |
| **Usability & accessibility** | ISO 9241-112 (ergonomics / informative) [5], WCAG principles (informative) [4] | Readability and inclusive design                   | Enhanced contrast, large text options, and intuitive navigation for all users                              |
| **Eye protection (not PPE)** | ANSI Z87.1 (informational only) [9]                             | Clarify non-safety eyewear classification          | Explicitly state the device is **not PPE** and not certified for impact resistance                        |
| **Wiring & power conductors** | **NEC/NESC low-voltage sections** [17]                           | Wire size, insulation, and fault protection       | Proper conductor sizing (AWG rating), insulation class, and inline fusing to prevent short circuits or thermal failure |
| **Converter efficiency**   | IEC 62301 [16]                                                  | Power-saving requirements for electronics          | Target ≥ 85% efficiency across converters to reduce standby losses and extend battery life               |


---

<p align="center">
  <strong>Table XV</strong>
  <br>
  Ethical-Risk Register & Mitigations We Implement
</p>

| Risk                        | Why It Matters                                  | Mitigation Built Into the Design                                                                 |
|------------------------------|------------------------------------------------|-------------------------------------------------------------------------------------------------|
| **Distraction while swinging** | Could reduce performance or lead to user accidents | Minimal overlay during pre-shot & swing; no full-screen alerts during swing                     |
| **Over-bright / hazardous optical output** | Prolonged exposure can cause eye strain or discomfort | Cap max perceived brightness; verify vs. ISO 15004-2 manufacturer specs; adhere to hazard group compliance |
| **Thermal hot spots on skin** | Could cause discomfort or minor injury        | Implement thermal budget; isolate pack; maintain surface ≤ 41 °C; add ventilation if needed     |
| **Battery misuse/fault**      | May cause fire or venting hazard              | Use protected 1S pack with BMS, inline fuse, NEC/NESC-compliant wiring, and enclosure relief    |
| **Data privacy**              | Risk of unwanted data capture or sharing      | Default local storage; encryption and deletion controls; transparent user consent interface    |




## Resources


### Budget

The budget for the wearable smart golf visor is a tentative estimate that will be refined as requirements, vendor quotes, and test results are finalized. It serves as an outline for the project and as a practical demonstration that the system can be built within a reasonable cost. The budget, shown in Figure [9], is organized by subsystems to clarify scope and expected cost for each area. Costs reflect single-unit procurement with no sales tax and no mandatory subscriptions. Where multiple part options existed, unit prices were averaged to produce a single representative figure per line item and then rolled up per subsystem.

**Subsystems covered:**

Launch monitor (primary sensing) — a portable photometric unit using a Raspberry Pi, dual Mobile Industry Processor Interface (MIPI) global-shutter cameras, and synchronized infrared (IR) strobing to measure per-shot ball speed, launch angles, and spin, publishing a normalized Shot record.

Visor / head-up display (HUD) — a head-worn near-eye display that acts as a Universal Serial Bus (USB)-High-Definition Multimedia Interface (HDMI) sink via the host device.

Communications — a microcontroller and Bluetooth Low Energy (BLE) radio that bridge sensor data to the application and HUD.

Power and I/O — the battery, charging and protection, power-delivery cabling and connectors, and the printed-circuit board (PCB).


**Launch Monitor — $436.00**

Launch Monitor subsystem — This subsystem implements the PiTrac-style photometric sensing pipeline: a Raspberry Pi 4 processes images from two Raspberry Pi–compatible global-shutter cameras with wide-angle lenses, while an infrared (IR) long-pass filter and chip-on-board (COB) LED strobe provide gated illumination to minimize motion blur. Supporting electronics include transistor–transistor logic (TTL) discretes and cabling for camera/strobe control, an LED power supply sized for the strobe duty cycle, and a printed/mechanical housing. For printed mounts and shields, PETG is preferred for the final build (strength, adhesion, heat tolerance), with PLA for quick jigs and ABS only near warm electronics. Because the unit must operate untethered at the range, the launch monitor also includes an onboard 3-series (3S) Li-ion battery pack (≈11.1 V nominal) sized to ~6 Ah (~100 Wh), a 3S battery-management system (BMS) for OV/UV/OCP/OTP protection, and a high-efficiency 12 V→5 V / 4 A buck that maintains a stable 5.1 V rail for the Pi and cameras. The strobe is isolated by a small dedicated stage (or bulk capacitance) so lighting pulses cannot pull the logic rail below 4.8 V. Charging is via a 12.6 V CC/CV input. This configuration supports ≳6 hours of field runtime (≥18 holes) with margin.

Line-item costs:
* Raspberry Pi 4: $55.00
* Global-shutter cameras (2 × $50): $100.00
* Wide-angle lenses (2 × $25): $50.00
* TTL-level discretes and cabling: $25.00
* IR long-pass filter: $15.00
* COB LED array (strobe): $30.00
* LED power supply: $36.00
* Chassis / project housing: $30.00
* 3D-printing material (PETG) — 2 × $20.00: $40.00
* 3S (11.1 V) 6 Ah Li-ion pack (~100 Wh): $55.00
* 3S BMS (OV/UV/OCP/OTP, 10–15 A): $12.00
* 12 V → 5 V / 4 A buck converter (5.1 V Pi rail): $14.00
* Strobe isolation stage (small buck or bulk caps): $8.00
* 12.6 V CC/CV charger (3S): $18.00
* Wiring, connectors, fuse, strain relief: $10.00

Launch Monitor (sensing only) Subtotal: $436.00
Battery/Power for PiTrac: $127.00
Combined Launch Monitor + Battery Subtotal: $563.00


#### Visor / HUD — $301.00

This subsystem is a head-mounted, near-eye display driven over a wired video link from the host so that the video path is bounded and repeatable and updates are frame-accurate. The bill of materials includes two full-high-definition micro-organic light-emitting diode (micro-OLED) panels for high-contrast, daylight-readable text, an inertial-measurement unit (IMU) for stable metric placement if head pose is used, and glasses frames to mount the optics safely.

* Micro-OLED displays (2 × $144 averaged): $288.00
* IMU sensor: $13.00
* Glasses frames / mounting hardware: $25.00

Visor / HUD Subtotal: $301.00

#### Communications — $25.00

This subsystem bridges the launch monitor and the HUD/application. It terminates sockets or serial links, normalizes messages, and forwards them. It also provides a Bluetooth Low Energy (BLE) path for control or future accessories.

* Microcontroller: $20.00
* BLE radio/module: $5.00

Communications Subtotal: $25.00

#### Power and I/O — $85.00

This subsystem supplies stable, protected power and the physical interconnects. It includes a lithium-polymer (Li-Po) cell, boost converters, a battery-management/protection circuit (BMS), a USB-C receptacle, a charger integrated circuit (IC) or module, miscellaneous passives/wiring, and a small PCB to consolidate power and connectors. Short, power-delivery (PD)–rated cables and strain relief preserve voltage margin and prevent disconnects during a swing.

* Li-Po cell: $13.00
* Boost converters: $5.50
* BMS / protection: $7.50
* USB-C receptacle: $4.50
* Charger IC / module: $4.50
* Misc. passives, wiring, LEDs, thermistors, fuses: $12.50
* PCB (incl. delivery): $37.50

Power and I/O Subtotal: $85.00

This total establishes the working baseline for design, procurement, and test planning. It reflects a complete, portable system that measures the ball’s motion, normalizes the resulting telemetry, and renders clear on-visor metrics without subscriptions or sales tax.

<p align="center"><strong>Summary</strong></p>

<p align="center">
  <img src="https://hackmd.io/_uploads/By3SKnHJWx.jpg" alt="Smart Golf Visor Budget" style="border:1px #eee; padding:1px; margin:20px;">
  <br>
  <strong>Figure 24: Smart Golf Visor Budget</strong>
</p>


### Division of Labor

Brandon Zerkovich has taken several computer science/ engineering classes (Introduction to Problem Solving & Computer Programming, Data Structures and Algorithms, and Microcomputer systems) where he has gained hands-on experience with microcontrollers and learned C and C++ coding languages. These skills are useful for both the Heads-Up Display (HUD) and app/software subsystems. The Smart Golf Visor team has decided to task Brandon with working on the HUD subsystem. He will take on the specifications for this subsytem and create a corresponding solution.

Landen Johnson has previous coursework in control system analysis, power system analysis, introduction to power systems, data structures, and mechatronics. He has also had prior internships in the utility sector, where he gained hands-on experience in testing, evaluating, and validating control and communication equipment under real operating conditions. While his direct experience in PCB design and deeper computer engineering is limited, that gap can be mitigated through targeted learning and research in the detailed power design and ensures the interfaces with other subsystems is robust and verifiable. The Smart Golf Visor team has decided to task Landen with working on the power subsystem. He will take on the specifications for this subsytem and create a corresponding solution.

Andrew Goostree has recent internship experience cleaning and structuring raw datasets, and his coursework like Introduction to Problem Solving and Computer Programming, Data Structures and Algorithms, and Microcomputer Systems directly supports converting raw launch monitor outputs into usable, time-stamped records. He is comfortable with Raspberry Pi setup, basic electronics and soldering, and 3D printing in polyethylene terephthalate glycol (PETG) for brackets and enclosures. He also has working knowledge of Transmission Control Protocol (TCP)/WebSocket, Bluetooth Low Energy (BLE), and camera bring-up, including focus and infrared (IR) filtering with web-based calibration. These skills align with the physical implementation of the PiTrac and the Data Acquisition subsystem for the Smart Golf Visor team.  The Smart Golf Visor team has decided to task Andrew with working on the launch monitor subsystem. He will take on the specifications for this subsytem and create a corresponding solution.

Ryan Zerkovich has gained experience working with microcontrollers and programming through coursework such as Computer Programming, Data Structures and Algorithms, and Microcomputer Systems. These courses provided him with a strong foundation in embedded programming and system-level problem solving, which are valuable skills for the Communication Subsystem. While his experience with specific communication protocols is currently limited, he is committed to closing this gap through targeted learning and research. The Smart Golf Visor team has decided to task Ryan with developing the Communication Subsystem, where he will apply his technical background to design a reliable and efficient method for data transmission between devices.

Michael Madigan has previous coursework/internship experience with programming languages, including Python, C, and C++. He also has hobbyist experience interfacing with microcontrollers. Michael intends to utilize that experience to close his knowledge gap in necessary Java and HTML environments to deliver a seamless end-user experience.The Smart Golf Visor team has decided to task Michael with developing the App/Software subsystem.

Bryce Hughes has prior experience in field engineering, system testing, and soldering that provides a practical foundation necessary to assemble, evaluate, and refine the physical prototype of the Smart Golf Visor. Bryce’s understanding of basic circuit integration and elementary coding through his previous coursework also enables him to bridge mechanical assembly with electrical interfacing during the early testing stages. The Smart Golf Visor team has decided to task Bryce with developing the Physical Visor Design subsystem.


### Timeline

<p align="center">
  <img src="https://hackmd.io/_uploads/Hyua7j9Clg.png" alt="Gantt Chart Fall 2025" style="width:700px; border:1px #eee; padding:1px; margin:20px;">
  <br>
  <strong>Figure 25: Updated Gantt Chart showing the Fall 2025 Semester</strong>
</p>

<p align="center">
  <img src="https://hackmd.io/_uploads/B1gyNiqRlg.png" alt="Gantt Chart Spring 2026" style="width:700px; border:1px #eee; padding:1px; margin:20px;">
  <br>
  <strong>Figure 26: Updated Gantt Chart showing the Spring 2026 Semester</strong>
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
