## Function of the Subsystem

The Communication Subsystem is essential to the operation of the Smart Golf Visor as it serves to establish reliable and high-speed data pathways between devices, ensuring timely shot metric (ball speed, ball distance, launch angle, spin rate, and smash factor) displayed for the user. In the absence of these communication pathways, the remaining subsystems would be unable to synchronize their operation or exchange measurement and display data. This subsystem wirelessly links the three core subsystems, the launch monitor, mobile application, and visor, via BLE protocol (Bluetooth Low Energy) to enable easy setup and portability. Within the visor, wired SPI (Serial Peripheral Interfacing) communication protocol is used to interface with sensor and display, providing fast data transfer rates necessary for real-time feedback and visualization. 


## Specifications and Constraints


####  Specifications 
Listed below are the specification **shall** statements for the Communications Subsystem:
- The PiTrac DIY Launch Monitor, App, and Visor **shall** wirelessly communicate shot metrics (ball speed, ball distance, launch angle, spin rate, and smash factor) via BLE (Bluetooth Low Energy) operating within the 2.4 GHz ISM band.
- The system **shall** achieve an end-to-end wireless latency of ${\le 120 \text{ ms}}$ for ≤ 95% of all shot-metric packets when app is running in the foreground.
- The visor's microcontroller **shall** operate at a nominal supply voltage of 3.3V ± 5%.
- The mobile application **shall** support simultaneous BLE connections with multiple paired devices.
- The visor's microcontroller **shall** possess BLE connectivity. 
- The visor peripherals (IMU and display) **shall** communicate with the microcontroller using the SPI serial interface.
- All hardware **shall** be designed to integrate with the Visor Design Subsystem for structural compatibility.
 


####  Constraints 
Listed below are constraints the subsystem must abide by, including rationales describing how each relates to engineering requirements and regulatory compliance.


<div align="center">
  <strong>Table I</strong><br>
  Unified Constraints, Standards/Guidance, and Design Impact Table


| **Constraint** | **Standard** | **Relevance to Design** | **Design Rationale**|
| ------ | ----- | ----- | ----- |
| **BLE Operating Frequency**| IEEE 802.15.1 (Bluetooth Low Energy Physical Layer Specification) [1]    | Ensures the wireless subsystem operates strictly within the globally unlicensed 2.400–2.4835 GHz ISM band, maintaining BLE compatibility and avoiding illegal or out-of-band transmission           | RF front-end must be designed/tuned for 2.4 GHz ISM operation; antenna and layout must meet FCC emissions constraints; system must remain within BLE PHY requirements         |
| **BLE Transmission Range & Packet Reliability** | Bluetooth SIG Core Specification (PHY sensitivity & link budget guidance); FCC Part 15 radiated-power restrictions          | BLE is optimized for short-range, low-power communication; defining a ≤10 m operational range with ≤1% packet loss ensures responsive telemetry and predictable RF behavior in a wearable           | Requires low-power TX configuration; line-of-sight range verification; antenna placement optimized for head-worn device; packet-loss testing at ≤10 m to validate link margin |
| **SPI Logic-Level Compliance**                  | Component-level datasheet specifications (ESP32-S3, IMU, display driver); JEDEC JESD8 low-voltage digital logic conventions | Ensures digital communication between the microcontroller and peripherals remains within the allowable 3.3 V logic-level limits, preventing latch-up, device overstress, or logic-threshold failure | All SPI signals must be routed at 3.3 V I/O levels; no level shifters required; PCB trace impedance kept consistent; ensures reliable timing margins and electrical safety    |
</div>
 
## Overview of Proposed Solution

The Communication Subsystem supports both wired and wireless data transmission between the Smart Golf Visor’s launch monitor, mobile application, and visor. Taking into account all previously defined specifications and design constraints, the proposed solution is organized as follows:

#### Wireless

The chosen wireless protocol for the subsystem is **Bluetooth Low Energy (BLE)**, selected for its **low power consumption**, **simplified network topology**, and **ease of integration**. All major system devices—the launch monitor, visor, and mobile application—include built-in BLE capabilities. The launch monitor uses a Raspberry Pi 5 microcontroller with native Bluetooth support,  as its higher processing power, faster I/O, and storage support is capable of running the camera control, strobe sync, image processing, and the web interface required. The visor incorporates an ESP32-S3 microcontroller with integrated BLE, and the mobile application runs on a smartphone that also provides BLE functionality. Because BLE operates **exclusively** within the **2.4 GHz ISM** band, all wireless communication between these devices occurs within this frequency range.

Within the BLE network, the smartphone functions as the **central device** responsible for managing the visor and launch monitor as **peripheral devices**. This configuration allows the user to pair and manage connections directly through their phone’s Bluetooth settings, providing a straightforward user experience. Modern smartphones support **multiple** simultaneous BLE connections, enabling **stable, concurrent communication** with both the Raspberry Pi units in the launch monitor and the ESP32-S3 in the visor. 

Because the golfer, launch monitor, and smartphone naturally remain within a few meters of each other during normal operation, the system operates well inside the **10-meter BLE range limit**, ensuring reliable transmission with **negligible packet loss**. This close-range configuration maintains stable real-time communication throughout the golfer’s swing and data-collection process.

#### Wired
The chosen serial protocol is **SPI (Serial Peripheral Interface)**, selected for its **high data transfer rates** and suitability for real-time sensor acquisition and display updates. Both the selected IMU (Adafruit TDK InvenSense ICM-20948 9-DoF) and display module (1.51 inch Transparent OLED) support **SPI communication**, and the ESP32-32 includes multiple SPI peripheral interfaces, allowing each device to be placed on its own chip-select line. Since the IMU, display, and ESP32-S3 all operate at **3.3V logic**, **no level shifters** are required.

Additionally, the microcontroller will require a **USB-C port** to enable programming, debugging, and configuration from a host computer. Since the ESP32-S3-WROOM1 module does not operate at the same voltage level as the laptop, the ESP32's programming guide suggests implementing a **USB to UART bridge IC** to enable **safe communication** between the host and the microcontroller’s serial bootloader without needing an external level shifter [2]. The selected bridge shall operate at **3.3 V logic** and provides the required level translation from the USB domain, ensuring that all UART signals presented to the ESP32-S3 are safely within its I/O specifications. In this configuration, the bridge’s **DTR** and **RTS control lines** may be connected to the ESP32-S3’s **EN and GPIO0 pins** through the standard capacitive-coupling network, enabling automatic reset and boot-mode selection during firmware flashing.

A custom PCB will be designed to host the ESP32-S3 and the wired peripherals, ensuring **short, impedance-controlled signal traces** and a mechanically stable connection inside the visor. 

## Interface with Other Subsystems

### POWER
The Communication and Power subsystems interface through a regulated **3.3 V DC input signal** that supplies both the microcontroller and the CP2102N USB-to-UART bridge. The upstream buck boost converter regulates the voltage while the signal is conditioned through a **multi-stage LC filter**, shown in Figure 1, which **attenuates** high-frequency switching noise and **minimizes** ripple to **maintain** the power integrity required for reliable digital timing and communication performance, effectively acting as a low pass filter.

The current demand of both devices are consist of the CP2102N drawing approximately up to **14 mA** [3] during active data transfers, while the microcontroller draws peak currents near **350 mA** [4] when its wireless radio is transmitting at full power. The resulting worst-case load of roughly **364 mA** remains comfortably within the capability of the power subsystem, ensuring stable regulation under maximum communication activity.

### LAUNCH MONITOR

The Communication and Launch Monitor subsystems interface to form a section of the Smart Golf Visor’s wireless communication architecture. This interface includes **no physical inputs** or **outputs**; instead, the Communication subsystem receives a **wireless input** consisting of **packeted shot-metric data** transmitted by the Launch Monitor. **No wireless output** is generated, as the purpose of this interface is solely to acquire the measured shot metrics for subsequent storage, processing, and display within the app and visor. Because the Launch Monitor and the smartphone running the app operate in close proximity, Bluetooth Low Energy (BLE) is used to provide an energy-efficient, short-range communication link for transmitting the metric packets.

Inside the BLE network, the launch monitor will function as **peripheral device** within the network [5]. In this role, the launch monitor will accept connections from the BLE central (app) and provides the packeted shot metrics data as requested.

The shot metrics (ball speed, ball distance, launch angle, spin rate, club head speed) are currently outputted internally through JSON text by the PiTrac DIY Launch Monitor after each swing. For wireless transmission, the JSON text will be parsed and converted into a fixed-length binary format consisting of each metric represented by a 4-byte float and the timestamp represented by a 4-byte unsigned integer. This binary packet will be transmitted over BLE UART which is a custom **GATT (Generic Attribute) Profiles**. These profiles will allow the app to determine when the packet starts and ends as well as distinguishing what each piece of data is. **GATT profiles** consist of **services** and **characteristics**. A single BLE UART service will be created, containing all the shot metrics  and will be assigned a unique **16-bit UUID** (Universally Unique Identifier), allowing the **client (app)** to distinguish it during device scanning and data discovery [6] . Within the service, the TX characteristics will function as a serial data stream containing all shot metrics into a single packet. The TX characteristic includes descriptors specifying units for user interpretation and properties defining the **bytes (4-8 bytes)** and **access permissions**, such as read or write [7]. Additionally, the RX characteristic can be used for any commands sent from the client to the peripheral.


### APP

The Communication and the App subsystems will interface to form the rest of the Smart Golf Visor's wireless communication architecture. There will be **no physical inputs** or **outputs**; instead, the app will **receive wireless data packets** inputs from the launch monitor and **output wireless data packets** to the visor. Because the smartphone running the app is within close proximity of the launch monitor and visor, this further justifies the use of **BLE (Bluetooth Low Energy) protocol**.

For the BLE network, app will serve as the **BLE central** to simplify device pairing for the user. In this role, the smartphone acts as a **centralized hub**, initiciating connections and continuously scanning for peripheral devices. It will receive the desired shot metrics from the launch monitor and foward that information to the visor. Therefore, the launch monitor and visor will function as **peripheral devices** within the network [5].

The packeted shot metrics received by the application are first unpacked, meaning that the raw bytes of the BLE UART TX characteristic payload are **read** and **decoded** according to the binary format defined by the launch monitor’s service. Each desired shot metric (e.g., ball speed, launch angle, spin rate) is **extracted** from the payload and **stored** in internal variables for processing. Within the app, the smash factor is calculated based on the launch speed and club head speed. Once processed, the application repackages the metrics (ball speed, ball distance, launch angle, spin rate, and smash factor) into a **fixed-length binary BLE payload** formatted for the visor’s **custom GATT characteristic**, matching the structure used by the launch monitor. This repackaged data is then transmitted to the ESP32-S3 in the visor, where it is interpreted and displayed in real time. The BLE UART RX characteristic may also be used to **send commands** to the peripheral, whether that is the PiTrac DIY Launch Monitor or the visor.

### HUD
The Communication and Heads up Display subsystems will create an internal SPI network for devices within the visor. The IMU and OLED display requires the following connections: 

##### IMU
- Serial Clock (SCLK)
- Master Out / Slave In (MOSI)
- Master In / Slave Out (MISO)
- Chip Select (CS)
- Interrupt (INT)

##### OLED Display
- Serial Clock (SCLK)
- Master Out / Slave In (MOSI)
- Chip Select (CS)
- Data / Command (DC)
- Reset (RST) 

Both the IMU and OLED display use SCLK, CS, and MOSI to ensure proper communication and reliable data transfer. The **Serial Clock (SCLK)** provides synchronization, ensuring that each device knows when to shift out and sample data. It shall operate at around **6MHz** to provide fast, reliable data while staying within the supported range of the peripheral devices. To communicate correctly on the same SPI bus, all devices must operate in the same SPI mode, which defines the **clock polarity (CPOL)** and **clock phase (CPHA**). In this design, all peripherals will use **SPI Mode 0**, which sets the clock to idle low and samples data on the rising edge [8]. The Serial Clock can be sent over any available GPIO pin.

Before transferring, a **Chip Select (CS)** pin is important for distinguishing which peripheral device the microcontroller wants to communicate with. Once the CS pin is set **low**, the desired device is **activated** and the information is transferred bit-by-bit [9]. The Chip Select can be implemented over any available GPIO pins as it just requires a digital signal to determine device status.

The **Master Out / Slave In (MOSI)** line allows the microcontroller to transmit data and commands to peripheral devices, such as initialization sequences, configuration registers, or read-request commands. Data on **MOSI** is transferred one bit at a time, synchronized to the **Serial Clock (SCLK)**. On each clock cycle, the microcontroller places a bit on the **MOSI** line, and the peripheral samples that bit on the appropriate clock edge (the rising edge in **SPI Mode 0**). This bit-clocking mechanism ensures reliable, deterministic communication between the master and all slave devices on the bus. [10]

The IMU additionally requires a **Master In / Slave Out (MISO)** and **Interrupt (INT)** line to send the data packets read by the sensor back to the microcontroller. The operatation of **MISO** is essentially the same defined for **MOSI** but sends data in the reverse direction. [11] The sent data packet consists of 3-axis accelerometer data (6 bytes), 3-axis gyroscope data (6 bytes), and optional temperature readings. The interrupt pin will be set high to notify the ESP32-S3 that it has data ready for it, reducing wait time and latency.

The OLED display will require two additional control signals consisting of a **Data/Command (DC)** and a **Reset (RST)** line. The Data/Command pin determines whether the information sent to the OLED display is data (set pin high) or a command to follow (set pin low). The **Reset (RST)** pin provides a hardware reset, when pin is set to low, to ensure a reliable and clear any undefined internal states before initialization. [12] 




### VISOR DESIGN


The Communication and Visor Design subsystems interface through the physical mounting of the Communication subsystem's PCB. Although the mounting itself does **not transfer data**, proper positioning is essential to maintain reliable connections between the Power subsystem, Communication subsystem's PCB, and the Micro OLED display. The standard thickness of a PCB is **1.6 mm** and shall be secured using **M2 screws** through **2.5 mm pre-drilled mounting holes**. To prevent short circuits and allow airflow for thermal management,  **5–10 mm nylon** standoffs shall  elevates the PCB above the visor surface. The orientation will be aligned with the placement of the display to **minimize cable strain** and **ensure consistent electrical connections**. The mounting design also allows for easy maintenance or replacement of the PCB and attached components without disturbing the overall visor assembly.

## Buildable Schematic 



### Low-pass Filter
<p align="center">
  <img src="https://hackmd.io/_uploads/S1U9avHWWe.png" alt="Low-Pass Filter Input and Output Voltage">
  <br>
  <strong>Figure 1 :  LC filter Schematic</strong>
</p>

Figure 1, the low pass filter seen is very similar to the filter suggested in the ESP32-S3-WROOM1 datasheet [3]. The suggested filter consists of **3 parallel capacitors** (10 µF, 1 µF, and 0.1 µF), a **2nH inductor**, and a **decoupling capacitor** (0.1 µF) placed close to the MCU (microcontroller unit). However, to effienciently filter out the higher frequencies, a 10 µH inductor will replace the 2nH inductor. The $\mathbf{10\,\mu\text{F}}$  capacitor ($C_1$) primarily addresses low-frequency ripple voltage from the buck boost converter. Conversely, the smaller capacitors ($\mathbf{1\,\mu\text{F}}$and $\mathbf{0.1\,\mu\text{F}}$) and $\mathbf{10\,\mu\text{H}}$ inductor form a low-pass filter stage to suppress higher frequency switching transients and EMI/RFI (Electromagnetic Interference/Radio Frequency Interference), leveraging the low Equivalent Series Inductance (ESL) of the ceramics. Finally, the $\mathbf{0.1\,\mu\text{F}}$ decoupling capacitor is placed immediately adjacent to the microcontroller's 3.3V input pin (the $V_o$ node) to provide the instantaneous current bursts required by the MCU's fast-switching digital and wireless circuitry. The microcontroller's **3.3V input pin** will be connected at the **Vo node** in the following schematic. This arrangement provides both **high- and low-frequency noise suppression**, improving power integrity for reliable microcontroller operation. Additionally, a **10 Ω shunt resistor** will be placed in parallel with the **10 µH inductor** to provide proper filter damping. [13]

### Wireless 
<p align="center">
  <img src="https://hackmd.io/_uploads/rJANxsqlbx.png" alt="BLE Wireless Network">
  <br>
  <strong>Figure 2 :  BLE Wireless Network</strong>
</p>

Figure 2 illustrates the data flow within the wireless subsystem. The smartphone application operates as the **BLE central**, while the launch monitor and visor function as **BLE peripherals**. This architecture supports straightforward connectivity between devices and corresponds to the communication pathways defined in the subsystem interfacing descriptions for both the Communication–Launch Monitor and Communication–App interactions.

### Wired
<p align="center">
  <img src="https://hackmd.io/_uploads/rJUB_cEZWl.png" alt="SPI Master Slave Network">
  <br>
  <strong>Figure 3 :  SPI Master/Slave Network</strong>
</p>

Figure 3 presents the wired data-transfer paths between the microcontroller, IMU, and display within the HUD subsystem. All communication lines operate at **3.3 V** and use the SPI protocol to ensure **high-speed** and **reliable** data exchange. These connections correspond directly to the signal interactions defined in the Communication and HUD subsystem interfacing, where the roles of the SPI master and slave devices, required signal lines, and timing dependencies are outlined.

<p align="center">
  <img src="https://hackmd.io/_uploads/rJCuj-2WZg.png" alt="USB to UART Communication Network">
  <br>
  <strong>Figure 4 :  USB to UART Communication Network</strong>
</p>

Figure 4 shows the circuitry used to program the microcontroller, as detailed in the wired communication section of the proposed solution. A CP2102N USB-to-UART bridge converts **5 V USB** data signals to **3.3 V** UART signals compatible with the ESP32-S3, enabling firmware uploading and serial communication via a USB-C interface.

<p align="center">
  <img src="https://hackmd.io/_uploads/r1tQywtD-e.png" alt="PCB Schematic for Wired Communication">
  <br>
  <strong>Figure 5 :  PCB Schematic for Wired Communication</strong>
</p>

Figure 5 shows the **complete PCB schematic**, incorporates the ESP32-S3 microcontroller with the needed low pass filter, USB-to-UART bridge, TVS diode protection network, as well as incorporates the IMU needed for the HUD subsystem and pin connections for the Micro OLED display. This schematic represents the full electrical design required to construct the PCB.

## Printed Circuit Board Layout

The Communications Subsystem PCB was designed using a **four-layer stackup** to support structured routing, signal integrity, power distribution, and grounding. The top layer contains **routed signal traces** and **incoming power connections**, along with a **ground pour**. The second layer consists of a **continuous ground plane** to provide a low-impedance return path and minimize noise coupling. The third layer is a **solid 3.3 V power plane** used to distribute regulated power across the board. The bottom layer contains **additional signal routing** and includes a **ground pour** to further support return current paths and reduce electromagnetic interference.

<p align="center">
  <img src="https://hackmd.io/_uploads/ryqUl_qDWl.png" alt="PCB Layout">
  <br>
  <strong>Figure 6 :  PCB Layout</strong>
</p>

Figure 6 shows the components' placement on the board and their routed copper traces carrying signals and the incoming **3.3V** within the **front layer (red)** and the **back layer (blue)**. The incoming power traces seen on the right side of the board's layout are thicker **(0.508 mm)** to accomodate for the current levels that will drawn **(max 350 mA)** and reduce resistive voltage drop. The **routed traces** do not include any **90 degree** turns to prevent any additional impedances towards the signals. On each corner of the board, **2.5 mm mounting holes** are placed to ensure proper mounting to the visor chassis. 


<p align="center">
  <img src="https://hackmd.io/_uploads/BJ9bIutD-e.png" alt="PCB Render">
  <br>
  <strong>Figure 7 :  PCB Render</strong>
</p>

Figure 7 presents the **3D rendering** of the assembled PCB. The rendering illustrates component placement, connector accessibility, and overall board organization. This visualization enables verification of component spacing, mounting alignment, and mechanical compatibility prior to fabrication while maintaining electrical equivalence to the layout shown in Figure 6.

## Flowchart

<p align="center">
  <img src="https://hackmd.io/_uploads/ryggwpN-We.png" alt="Bluetooth Transmission Flowchart">
  <br>
  <strong>Figure 8 :  Bluetooth Transmission Flowchart</strong>
</p>

<p align="center">
  <img src="https://hackmd.io/_uploads/ByFokjNb-g.png" alt="SPI Transmission Flowchart">
  <br>
  <strong>Figure 9 :  SPI Transmission Flowchart</strong>
</p>

## BOM

###### Bill of Materials
![Screenshot 2026-02-11 222839](https://hackmd.io/_uploads/BJkdv05DZg.png)
![Screenshot 2026-02-11 222902](https://hackmd.io/_uploads/BJddPC5vZg.png)
![Screenshot 2026-02-11 223112](https://hackmd.io/_uploads/rJ05DC5PWe.png)


# Analysis

According to the constraints of the system, the following is an in-depth analysis supporting the design abiding by it. 

#### Wireless
The wireless communication of the Smart Golf Visor consists of Bluetooth Low Energy (BLE) protocol, which is inherently designed to operate exclusively within the **2.4 GHz ISM band**. Throughout the 2.402-2.480 GHz range, **40 radio frequencies channels** are defined, each spaced out **2 MHz apart**, providing multiple frequency options for both advertising and data transmission. The ESP32-S3-WROOM1 module enforces this limitation through its hardware consisting of: its RF synthesizer, power amplifier, low-noise amplifier, and a band-specific matching network. These are both electrically and physically tuned to only operate with this **2.4 GHz** range. The RF synthesizer is tightly integrated into the chip’s digital logic and closed firmware, allowing it to generate only BLE-standardized channel frequencies. Additionally, the other remaining RF front-end components (power amplifier, low-noise amplifier, and a band-specific matching network)  are optimized exclusively for **2.4 GHz operation** and experience **severe gain degradation** outside that band. Together, these constraints create a hardware-enforced RF boundary that ensures the system can only transmit within the required **2.4 GHz ISM band**, directly satisfying the wireless communication constraint. 

The BLE protocol is also **highly energy-efficient** and well-suited for small data transmissions, supporting extended single-charge use. The reason for being highly energy-efficient is due to the RF module going into a deep-sleep state, only drawing  **1 μA** at **3V**, once the data has been sent or received [14]. For a typical 18-hole round, the average golfer takes about **100 strokes** and lasts approximately **4 hours** [15]. Breaking that down, the average golfer is swinging **~25 swings** an hour. For the majority of the time, the BLE radio will remain in a deep sleep, only waking to briefly transit or receive shot metrics, thereby minimizing energy consumption and maximizing the specified battery life **(3,000–5,000 mAh)**.

The Pitrac DIY Launch Monitor outputs the shot metrics (ball speed, ball distance, launch angle, spin rate, club head speed, and timestamp) in **JSON fomart**. Each metric will be represented by a **4-byte float** and the timestamp represented by a 4-byte unsigned integer. With a total of 5 metrics and a timestamp the total bytes of the packet is: 

$$
\text{Total Packet Bytes}=
(5 ~ \text{metrics} ~ * ~ 4 ~ \text{bytes}) ~ +
(1 ~ \text{timestamp} ~ * ~ 4 ~ \text{bytes}) = 24 ~\text{bytes}
$$

The Raspberry Pi 5, smartphone, and ESP32-S3 all support BLE connections with configurable **Maximum Transmission Unit (MTU)** sizes, allowing reliable transmission of the 24-byte binary packet containing the shot metrics and timestamp. While the default MTU is 23 bytes (20 bytes payload), these devices can negotiate larger MTUs: Raspberry Pi 5 and ESP32-S3 up to **247 bytes**, iOS up to **185 bytes**, and Android up to **517 bytes**. Because the packet fits within these negotiated MTUs, it can be sent in a single BLE notification, **minimizing fragmentation** and **reducing latency**. Fragmentation occurs when a payload exceeds the MTU, requiring multiple notifications to transmit and reassemble the data, which adds overhead and delay. Using a single notification ensures real-time delivery of shot metrics to the visor.

When transmitting data packets, the **Packet Error Rate (PER)** directly predicts whether a packet will be lost, and it is fundamentally influenced by the modulation scheme, packet length, and received signal strength. BLE employs **Gaussian Frequency-Shift Keying (GFSK)**, a modulation technique which encodes digital bits by shifting the carrier frequency between two distinct deviation levels while its reliability depends heavily on the receiver’s **Signal-to-Noise Ratio (SNR)**. As distance increases, the received signal undergoes free-space path loss, typically decreasing on the order of **20–25 dB** per decade of distance under outdoor line-of-sight conditions, which reduces SNR and increases the **bit error rate (BER)** [16].  Within the specified **10-meter** operating range for this system, the received signal remains well above the approximate **–95 to –100 dBm** noise floor, even assuming conservative transmit powers of **0–10 dBm** [17].  This maintains a sufficiently high SNR to ensure that BER—and consequently PER—remains far **below** the **1% requirement**. Packet length also contributes directly to reliability, as longer packets contain more bits and therefore more opportunities for error; however, the Smart Golf Visor system transmits at most **40 bytes per packet**, substantially reducing the probability of accumulated bit errors. The accompanying PER-versus-distance simulation assumes larger packet sizes for worst-case analysis, meaning the actual system’s PER will be even lower in practice. 

The simulation calculates total packet loss as:

$$
\text{Total Packet Loss} = \frac{ \text{ Number of lost packets}}{\text{Total number of packets}}
$$

plotted against the distance between devices. The number of lost packets is estimated using a **Monte Carlo simulation**, which generates random packet errors based on the **packet error rate (PER)** formula:

$$
\text{PER} = 1 - (1 - \text{BER})^{L_\text{packet}}
$$

where $L_\text{packet}$ is the packet length in bits and the **bit error rate (BER)** is approximated for BLE 1 M PHY GFSK modulation as:

$$
\text{BER} = \frac{1}{2} \exp(-\text{SNR}_\text{lin})
$$

The **SNR** is calculated from the received power, which accounts for transmit power, antenna gains, and **free-space path loss (FSPL)**:

$$
P_r(\text{dBm}) = P_t + G_t + G_r - 20 \log_{10} \left( \frac{4 \pi d f_c}{c} \right)
$$

This approach allows the simulation to estimate BLE packet loss as a function of distance, modulation, and packet length [18]


<p align="center">
  <img src="https://hackmd.io/_uploads/ByXBTOzZ-e.png" alt="BLE packet loss rate vs distance">
  <br>
  <strong>Figure 10: BLE Packet Loss Rate as Device Distance Increases</strong>
</p>


These combined factors and simulation demonstrates that packet loss remains negligible at **10 meters**, and the system comfortably satisfies the **≤1%** packet-loss specification,  providing realiable data transfer throughout the subsystems (launch monitor, app, and visor).

Having a **PER of ≤ 1%** contributes significantly to a lower latency time below **120ms** as the chance for a packet to be lost and retransmitted are very low. The other most important factors are the **CI (connection interval)** and **App processing jitter**. The CI is a set interval agreed to by the BLE central and peripheral device where both devices wake up to transmits or receive, immediately going back to a **deep-sleep state** until the next interval. Most smartphones today are limitted to a **30ms connection interval** the need to balance BLE radio activity with system-wide power consumption policies. Depending on the device, some may be able to compensate at **22.5 ms**, allowing the latency time to be even lower. The **App processing jitter** is an unpredictable delay between the smartphone BLE stack and the app's program. This delays can typically last between **5 ms to 50ms**.

In the following Monte Carlo simulation of the BLE network, the design specifications of **≤ 120 ms latency** at the **95th percentile** is validated in Figure 10. The combination of the **30 ms** CI constraint and potential **50 ms** jitter excursions defines the realistic upper bound of the system’s end-to-end latency.


<p align="center">
  <img src="https://hackmd.io/_uploads/SyiGTrDZZg.png" alt="System End-to-End Wireless Latency">
  <br>
  <strong>Figure 11: System End-to-End Wireless Latency</strong>
</p>

#### Wired

To avoid the need for a logic-level shifter, the SPI communication between the ESP32-S3, Adafruit TDK InvenSense ICM-20948 9-DoF IMU, and the 1.51" Transparent OLED display will operate at the same voltage. According to their datasheets, each device accepts **3.3 V logic** for proper operation. While the Transparent OLED display’s supply voltage is **7–16 V** due to its onboard regulator, its logic input still operates at **3.3 V** [19], allowing it to communicate directly with the ESP32-S3. This ensures that all devices satisfy the required **3.3 V logic-level** constraint.

The SPI bus is capable of handling the required data rates with significant margin. The **Serial Clock (SCLK)** is set to **6 MHz**, giving a maximum theoretical throughput of:

$$
\text{SPI Throughput} = \frac{f_\text{SCLK}}{8~\text{bits/byte}} = \frac{6~\text{MHz}}{8} \approx 750{,}000~\text{bytes/sec}
$$

This represents the amount of bytes that can be successfully transmitted in a second. The Adafruit TDK InvenSense ICM-20948 9-DoF IMU generates **12 bytes** per sample (3-axis accelerometer + 3-axis gyroscope) at no more than a sampling rate of **400 Hz** [19]:

$$
\text{IMU Data Rate} = 12~\text{bytes/sample} \times 400~\text{samples/sec} = 4{,}800~\text{bytes/sec}
$$

Similarly, the 1.51" Transparent OLED display requires approximately **128 bytes** per frame. At a **60 Hz** refresh rate:

$$
\text{OLED Data Rate} = 128~\text{bytes/frame} \times 60~\text{frames/sec} = 7{,}680~\text{bytes/sec}
$$

The combined data rate of both devices is therefore:

$$
\text{Total Data Rate} \approx 2{,}400 + 7{,}680 = 10{,}080~\text{bytes/sec}
$$

The total combined data rate of **10,080 bytes/sec** is **less than 2%** of the maximum theoretical SPI throughput, leaving significant headroom for additional commands and bursts of bytes. This justifies that both peripherals are easily handled with plenty of margin for reliability.

Latency is also crucial for data transfer in a timely manner. The Adafruit TDK InvenSense ICM-20948 9-DoF IMU consists of a **12 byte** full sample and with a serial clock of **6 MHz** [19], it has a latency of 

$$
t_\text{IMU} = \frac{12~\text{bytes} \times 8~\text{bits}}{6~\text{MHz}} \approx 16~\mu\text{s}
$$

The 1.51" Transparent OLED display with a full frame consists of a **128-byte** full sample with a serial clock of **6 MHz**, the display latency will be 

$$
t_\text{OLED} = \frac{128~\text{bytes} \times 8~\text{bits}}{6~\text{MHz}} \approx 0.17~\text{ms}
$$

These low latencies ensure that both sensor data acquisition and display updates occur effectively instantaneously from the user’s perspective, satisfying the real-time performance requirements of the Smart Golf Visor.


For reliable programming of the ESP32-S3, a USB to UART bridge IC will need to be implemented to take a 5V input USB signal to a 3.3V UART signal. The operation of the USB to UART bridge IC is shown in Figure 12:

<p align="center">
  <img src="https://hackmd.io/_uploads/rJUUPKRb-x.png" alt="USB Plug-in & CP2102N Bridge IC Connection">
  <br>
  <strong>Figure 12: USB Plug-in & CP2102N Bridge IC Connection</strong>
</p>



The **VBUS** connection does **not supply power** to the CP2102N, but instead allows the IC to determine if there is a signal present. Because the VBUS line consists of a **5V signal**, the datasheet recommends implementing a voltage divider in order to reduce the voltage to a safe level at the input. The resistors used for the voltage divider are $\mathbf{\text{22.1k}\Omega}$ and $\mathbf{\text{47.5k}\Omega}$ which reduces the voltage to the following:

$$
V_{in} = (5\text{V})\frac{47.5~\text{k}\Omega}{47.5~\text{k}\Omega + 22.1~\text{k}\Omega }\approx 3.41 ~\text{V}
$$

This is well within the operating voltage range of the ESP32-S3, where the absolute maximum input voltage is **3.6 V**. Even at the maximum output voltage of **5.25V** from the USB due to its internal restrictions, it will still the input voltage to stay within the safe operating range but remains close to the maximum.

$$
V_{in} = (5.25\text{V})\frac{47.5~\text{k}\Omega}{47.5~\text{k}\Omega + 22.1~\text{k}\Omega }\approx 3.58 ~\text{V}
$$

However when accounting for ± 1% tolerances for both the $\mathbf{\text{22.1k}\Omega}$ and $\mathbf{\text{47.5k}\Omega}$ resistors, the divider output voltage can increase beyond the limit of **3.6 V**. 

$$
22.1~\text{k}\Omega * 0.99 = 21.879~\text{k}\Omega
$$

$$
47.5~\text{k}\Omega  * 1.01 = 47.975~\text{k}\Omega
$$

$$
V_{in} = (5.25\text{V}) *\frac{47.975~\text{k}\Omega}{47.975~\text{k}\Omega + 21.879~\text{k}\Omega }\approx  3.606~\text{V}
$$

Since this exceeds the safe maximum operating voltage, the $\mathbf{\text{47.5k}\Omega}$ resistor can be reduced to a $\mathbf{ \text{43.2k}\Omega}$ resistor. This **reduces** the ratio and the output voltage of the divider effectively creating a **safe buffer** within the ESP32-S3's operating range. This ensures the worst-case operation remains below **3.6 V**.

$$
22.1~\text{k}\Omega * 0.99 = 21.879~\text{k}\Omega
$$

$$
43.2~\text{k}\Omega * 1.01 = 43.632~\text{k}\Omega
$$

$$
V_{in} = (5.25~\text{V})* \frac{43.632~\text{k}\Omega}{43.632~\text{k}\Omega + 21.879~\text{k}\Omega }\approx 3.49 ~\text{V}
$$

At the nominal voltage of **5V**, the input voltage is reduced to

$$
V_{in} = (5\text{V})* \frac{43.2~\text{k}\Omega}{43.2~\text{k}\Omega + 22.1~\text{k}\Omega }\approx 3.3 ~\text{V}
$$

While the corresponding nominal current is

$$
I_{nom} = \frac{5~\text{V}}{43.2~\text{k}\Omega + 22.1~\text{k}\Omega }\approx 76.6 ~\mu\text{A}
$$

This extremely low nominal current is fully compliant with the USB low-power specification by drawing only about $\mathbf {0.08\%}$ of the $\mathbf{100~\text{mA}}$ limit for an unconfigured device, placing a **minimal load** on the host [20].

To provide proper protection for the CP2102N IC, the datasheet recommends connecting a SP0503BAHTG ESD (Electrostatic Discharge) protection chip[13]. This TVS (Transient Voltage Suppression) diode array serves as the primary protection component for the CP2102N USB-to-UART bridge, safeguarding the device against **high-energy voltage transients** and **electrostatic discharge events**. The array is connected across **D+**, **D–**, and **VBUS**, where it **clamps voltage spikes** to approximately **8.5 V** and **shunts excess current** safely to ground. 

According to IEC 61000‑4‑2, the maximum air-discharge ESD voltage level for extreme testing is **15 kV**. Using the HBM parameters **R = 330 $\Omega$** and **C = 150 pF** [21], the instantaneous peak current delivered by the ESD event before the TVS diode can react is: 

$$
I_{peak} = \frac{15~\text{kV}}{330~\Omega }\approx 45.46~\text{A}
$$

This is a very high current produced however the total energy stored in the capacitor is:

$$
E = \frac{1}{2} * (150 ~pF) * (15 kV)^2 \approx 16.9 ~ mJ 
$$


The pulse energy experienced is well within the SP0503BAHTG's rated for **0.1 J pulse energy**, allowing the diode to **fully absorb** the **entire** pulse. The device’s fast response time of **<1 ns** enables rapid clamping of ESD transients, ensuring that the CP2102N is effectively protected even under nanosecond-scale voltage spikes [22]. The following simulation, Figure 12, illustrates an **overestimated** pulse width of **50 ns** with a **1 ns** rise time is simulated at a scaled voltage of **100 V**, which is significantly lower than the actual **15 kV** air-discharge voltage. This scaling is applied to clearly visualize the clamping voltage while still demonstrating the diode’s protective behavior. The simulation demonstrates the clamping voltage stabilizing at **8.5 V**, effectively illustrating the diode’s protection characteristics.


<p align="center">
  <img src="https://hackmd.io/_uploads/rJXnql3-Wx.png" alt="ESD pulse clamped by SP0503BAHTG at CP2102N input pins">
  <br>
  <strong>Figure 13: Simulation of ESD Pulse Clamped by SP0503BAHTG at CP2102N Input Pins</strong>
</p>


At this clamping voltage, the voltage divider will step the voltage down to at the input node to:

$$
V_{in} = (8.5V) * \frac{43.2k\Omega}{(43.2k\Omega) + (22.1k\Omega)}  \approx 5.62 V 
$$

The input voltage of **5.62 V** is within the absolute-maximum rating for the CP2102N bridge IC of **5.8 V** [3]. Therefore the CP2102N will be able to withstand this if a ESD pulse was to occur. 

Once the USB signal from a host computer is converted to **UART** by the CP2102N, the serial communication is routed to the ESP32-S3 for programming and data exchange. The **TXD** pin of the CP2102N connects to the **RX** pin of the ESP32-S3, and the **RXD** pin connects to the **TX** pin of the ESP32-S3, ensuring proper cross-connection for bidirectional data transfer. Both pins operate at **3.3 V logic levels**, fully compatible with the ESP32-S3’s input thresholds. A shared ground between the CP2102N and ESP32-S3 establishes a **stable** voltage reference, which is critical for avoiding data corruption caused by voltage level mismatches or floating references.

The CP2102N also provides additional control and status signals that can enhance reliability and facilitate automatic programming. The **RTS (Request to Send)** and **CTS (Clear to Send)** lines can be connected between the CP2102N and ESP32-S3 to implement hardware flow control. This allows the ESP32-S3 to pause the CP2102N when its internal buffers are full, preventing data loss during high-speed communication, particularly at baud rates above **115,200 bps**.

For programming and bootloader control, the **DTR (Data Terminal Ready)** and **RTS** signals play a critical role. **DTR** is connected to the ESP32-S3’s **EN (reset)** pin, while **RTS** is connected to **IO0** (boot mode selection). During firmware upload, the **CP2102N** toggles these signals in a specific sequence: **DTR** briefly pulls **EN** low to reset the microcontroller, and **RTS** simultaneously pulls **IO0** low to select **UART** bootloader mode. This automated mechanism ensures the ESP32-S3 reliably enters programming mode without manual intervention, while returning to normal operation once programming completes. Timing is carefully controlled, and the signals remain **inactive** during normal operation, allowing the microcontroller to run user applications without interference [2].

#### Low-pass filter
The **ESP32-S3-WROOM1** is powered by an upstream buck boost converter that **regulates** the input voltage but introduces **high-frequency ripples** as a byproduct of its switching operation. To suppress these ripples as well as additional noise generated by ESP32’s wireless and digital activity, the **LC circuit** shown in Figure 1 is implemented to act as a low-pass filter, preserving the integrity and stability of of the **3.3 V rail** by attenuating high-frequency disturbances before they reach the microcontroller.

To evaluate the performance of the proposed low-pass network, the cutoff frequency is first determined:  

$$f_c = \frac{1}{2\pi \sqrt{LC_{TOT}}}$$

$$ C_{TOT}  = 10\mu\text{F}+1\mu\text{F}+0.1\mu\text{F} = 11.1\mu\text{F}$$

$$f_c = \frac{1}{2\pi \sqrt{(10\mu\text{H})(11.1\mu\text{F})}}  \approx 15.1 \text{kHz}$$ 

The **15.1 kHz** cut-off frequency is also known as the **low-pass corner frequenc**y at which the signal's voltage is reduced by **-3db**, essentially acting as an **effective cut-off**. Any frequencies exceeding this are substantially suppressed. This design characteristic ensures that any noise generated by the buck boost converter or high-frequency switching noise from the microcontroller itself is **immediately filtered** when it appears on the **$3.3\text{V}$ rail**.

The noise reduction across a broad frequency range are acheived through the parallel capacitors and series inductor. The inductor presents a high impedance barrier to higher-frequencies, protecting the microcontroller. The high frequencies are **shunted** to the ground through the **low impedance path** prodivided by the parallel capacitors. At low frequencies (for example **1 kHz**), the **$10 \mu \text{H}$ inductor** provides a lower reactance than the parallel capacitors as shown below:

$$X_L = {2\pi\text{(1kHz)($10 \mu \text{H})$}} \approx 0.063\Omega$$

$$X_{C1} = \frac{1}{2\pi{(1\text{kHz})(10 \mu \text{F})}} \approx 15.9 \Omega$$

$$X_{C2} = \frac{1}{2\pi{(1\text{kHz})(1 \mu \text{F})}} \approx 159.2 \Omega$$

$$X_{C3} = \frac{1}{2\pi{(1\text{kHz})(0.1 \mu \text{F})}} \approx 1592 \Omega$$

At low frequencies, The **$10 \mu \text{H}$ inductor** is acting as a **short circuit**, allowing for the **3.3V power signal** to pass through to the microcontroller while still **suppressing** undesired high-frequency content. As the frequency begins to increase to around middle frequency ranges, the **$10 \mu \text{H}$ inductor's** reactance grows acting as a high impedance barrier, allowing the parallel capacitors to mitigate the noise. The higher frequency ranges are the filter's **main mitigation target** as the buck boost converter produces ripples in this range (**2.5 MHz**). The following is impedances for each component at a frequency of **1MHz**: 

$$X_L = {2\pi\text{(1 MHz)($10 \mu \text{H})$}} \approx 63\Omega$$

$$X_{C1} = \frac{1}{2\pi{(1\text{MHz})(10 \mu \text{F})}} \approx 0.0159 \Omega$$

$$X_{C2} = \frac{1}{2\pi{(1\text{MHz})(1 \mu \text{F})}} \approx 0.159 \Omega$$

$$X_{C3} = \frac{1}{2\pi{(1\text{MHz})(0.1 \mu \text{F})}} \approx 1.59 \Omega$$

Although the inductor provides an impedance of around **63 $\Omega$** at **1 MHz**, the shunt resistor provides a maximum impedance of **10 $\Omega$**, as signals have a path to bypass the inductor. Because the parallel capacitors present substantially lower reactance at these frequencies, the resulting noise is effectively diverted to ground. As the filter encounters frequencies in the high range category, the capacitors provide an even **smaller impedance** and **easier travel path**, further enhancing attenuation. Below is the impedances of each component experiencing a **10MHz** frequency: 

$$X_{C1} = \frac{1}{2\pi{(10\text{MHz})(10 \mu \text{F})}} \approx 0.00159 \Omega$$

$$X_{C2} = \frac{1}{2\pi{(10\text{MHz})(1 \mu \text{F})}} \approx 0.0159 \Omega$$

$$X_{C3} = \frac{1}{2\pi{(10\text{MHz})(0.1 \mu \text{F})}} \approx 0.159 \Omega$$

Because the shunt resistor establishes a **10 Ω** maximum impedance in the bypass path, high-frequency noise components are effectively suppressed, as the capacitors continue to provide a substantially lower-impedance route to ground. This behavior further validates that the filter maintains a stable and unaltered **3.3V** supply under high-frequency loading conditions.

The shunt resistor also serves an important role in the **dissipation** of energy at the filter's **resonance frequency**. The resonance frequency is equal to the cut-off frequency of 1**5.1 kHz**. Without proper damping at this frequency, the system gain increases drastically as seen in the following Bode Plot: 

<p align="center">
  <img src="https://hackmd.io/_uploads/BJt_H9Ub-x.png" alt="Low-Pass Filter Undamped">
  <br>
  <strong>Figure 14: Low-Pass Filter Undamped</strong>
</p>

The severe peak, seen at the resonance frequency, is approximately +20 dB. Calculating  the gain of the system, we get: 

$$ \text{System Gain}= 10 ^{(20 \text{dB} / 20)} \approx 10 $$

In this case, the **3.3V** DC input to the filter becomes a **33V** output exceeding the operating range of the ESP32-S3-WROOM1, essentially **destroying** it. To get rid of this increased gain, damping resistances needs to be applied in order to dissipate the energy generated from the resonance frequency. In this case, a **10 $\Omega$** shunt resistor is placed in parallel with the inductor to equalize the maximum load resistance **(10 $\Omega$)** of the microcontroller when it is drawing max current (approximately **350 mA**) [2]. In addition to the damping shunt resistance, the **$10\mu\text{H}$** inductor's **DCR** (DC resistance) also provides a series resistance to help dissipate any build up of energy caused from resonance frequency oscillation. Since it is in series with the input voltage, the approximate voltage drop for a **65 $\text m \Omega$** DSR is:

$$ V_{DR,L} = 330 mA * 0.065 \approx 21.45 \text{mV} $$

allowing the voltage regulation to be maintained while still contributing to the filter's high noise-blockage impedance. The parallel capacitors will also provide a **ESR** (Equivalent Series Resistance) similar to the **$10\mu\text{H}$ inductor**. This will also contribute to the damping of the filter, as it will help damp the self-resonant frequencies produced by the capacitors but still providing a **low-impedance path** to ground for the high frequencies [23]. A **0.05 ESR** will also produce a voltage drop of: 

$$ V_{DR,C} = 330 mA * 0.05 \approx 16.5 \text{mV} $$

which also minimizes the voltage drop over the capacitance's resistance while still maintaining the **low impedance path** to ground and contributing to the dissipation of built up energy caused by resonance oscillations.

With the proper damping in place for the low-pass filter, ensuring stability of all **ESP32-S3-WROOM1** load types, the following shows the cut-off and signal attenuation of the filter: 
   

<p align="center">
  <img src="https://hackmd.io/_uploads/H157kdHW-x.png" alt="Low-Pass Filter with Damping">
  <br>
  <strong>Figure 15: Low-Pass Filter with Damping</strong>
</p>


The plot demonstrates the expected low-pass behavior, with the output magnitude remaining **stable** below the corner frequency and exhibiting **significant attenuation** for frequencies above the cutoff. This confirms that the filter effectively **rejects** high-frequency noise components while preserving the integrity of the **3.3V** supply rail.

Within this low-pass filter, the inductor is more than just a barrier as it also provides **protection** against **inrush current** during the **transient response** of a circuit or load changing. Since the capacitors are not charged, the act as **short circuits** drawing a lot of current to charge in which the inductor mitigates the current flow to the microcontroller, **protecting** it from damage. 

$$\frac{di}{dt} = \frac{3.3V}{10\mu\text{H}} \approx 0.33 \text{A/$\mu$s}$$

The **$10\mu\text{H}$ inductor** provides a **slow ramp up** current of $\mathbf{0.33 ~\text{A/}\mu\text{s}}$ supplied to the microcontroller until the buck boost converter responds and **reestablishes** regulation. The **decoupling 0.1 $\mu\text{F}$ capacitor** placed near the microcontroller’s supply pin further suppresses high-frequency disturbances generated locally at the device

The simulated input and output waveforms of the filter are shown in Figure 16: 

<p align="center">
  <img src="https://hackmd.io/_uploads/BkhRnPrb-x.png" alt="Low-Pass Filter Input and Output Voltage">
  <br>
  <strong>Figure 16: Low-Pass Filter Input Voltage & Output Voltage</strong>
</p>

The analysis and simulations confirm that the proposed **LC low-pass filter** effectively stabilizes the **3.3 V** rail by attenuating switching ripple from the buck boost converter (**2.5 MHz**) and high-frequency noise generated by the microcontroller. The chosen inductor–capacitor network provides strong high-frequency suppression, while the **10 Ω damping resistor**, along with the inductor’s **DCR** and capacitor **ESR**, **eliminates** the resonant gain that would otherwise amplify disturbances near the corner frequency. Additionally, the inductor **limits** inrush current during transients, helping **maintain** voltage regulation as the capacitors charge. With these combined characteristics, the filter reliably ensures a **clean**, **stable**, and **well-damped** supply voltage for the **ESP32-S3-WROOM1** under all expected operating conditions.
<br>

#### PCB
Power demands for the SPI subsystem are **low** and **easily** managed within standard PCB design limits. The ESP32-S3 typically draws a maximum current of approximately **350 mA** during MCU transmission operations [2], the ICM-20948 IMU consumes **≈0.75 mA** (about **2.5 mW** at **3.3 V**)[19], and the 1.51″ transparent OLED module consists of 128 pixels that draws **320 µA** per pixel, leading to approximately a **41 mA** max current draw [12]. With such modest current levels, voltage drop, ground bounce, and power-distribution noise remain minimal when using a solid **3.3 V** plane and proper decoupling at each device. For signal integrity, the SPI lines are routed as single-ended traces designed to approximate 50 Ω impedance on a standard **1.6 mm FR-4 stackup**, which typically corresponds to a trace width of **0.2–0.3 mm**. Spacing between adjacent SPI signals follows the “**3W rule**” which keeps traces separated by at least three times their width to reduce capacitive crosstalk [24]. All SPI lines are routed over a continuous, unbroken ground reference plane to provide a clean return path and minimize loop inductance [25]. Additionally, routing avoids **90° corners**, which create impedance discontinuities and can distort high-speed edges; instead, **45° bends** or **smooth arcs** are used [26]. These combined practices—controlled trace geometry, proper spacing, continuous ground referencing, low current draw, and avoidance of sharp angles—ensure that the SPI bus maintains clean, low-distortion edges at the selected **6 MHz** clock rate and supports reliable communication across the PCB.



## References

[1] IEEE Standards Association, *IEEE 802.15.1 Standard for Wireless Personal Area Networks (PAN),* 2023. [Online]. Available: https://standards.ieee.org.

[2] Espressif Systems, “Establish Serial Connection with ESP32,” ESP-IDF Programming Guide, [Online]. Available: https://docs.espressif.com/projects/esp-idf/en/stable/esp32/get-started/establish-serial-connection.html

[3] Silicon Labs, CP2102N USB to UART Bridge Data Sheet, Rev. 1.5, Silicon Labs, 2023. [Online]. Available: https://www.silabs.com/documents/public/data-sheets/cp2102n-datasheet.pdf

[4] Espressif Systems, “ESP32-S3-WROOM-1 & WROOM-1U Datasheet v1.61,” Nov. 2022. [Online]. Available: https://www.espressif.com/sites/default/files/documentation/esp32-s3-wroom-1_wroom-1u_datasheet_en.pdf.

[5] NovelBits, “A Guide to BLE Peripherals & Centrals,” Nov. 2025. [Online]. Available: https://novelbits.io/ble-peripherals-centrals-guide/.

[6] Renesas Electronics Corp., “GATT — Tutorial: Custom Profile (DA145xx),” Online: https://lpccs-docs.renesas.com/tutorial-custom-profile-DA145xx/gatt.html.

[7] M. Afaneh, “Bluetooth GATT: How to Design Custom Services & Characteristics,” *NovelBits*, Apr. 10, 2023. [Online]. Available: https://novelbits.io/bluetooth‑gatt-services-characteristics/.

[8] Microchip Technology Inc., “50.6.2 Serial Clock Phase and Polarity,” in SAMA5D29 Microprocessor Unit — 50 Quad Serial Peripheral Interface (QSPI), Online: https://onlinedocs.microchip.com/oxy/GUID-2ACDA668-0A87-46A1-B7FC-9DC74A5461AD-en-US-2/GUID-177CC5D0-A46B-4C9B-8366-526E716A73C3.html.

[9]J. Bakos, Chapter 3 — Serial Peripheral Interconnect (SPI), University of South Carolina, Jan. 2023. Available: https://www.cse.sc.edu/~jbakos/491/text_ch3.pdf.

[10] P. Dhaker, “Introduction to SPI Interface,” Analog Dialogue, vol. 52, Sep. 2018. Available: https://www.analog.com/en/resources/analog-dialogue/articles/introduction-to-spi-interface.html.

[11] “Synchronous serial communication: The basics,” ITP Physical Computing, https://itp.nyu.edu/physcomp/lessons/synchronous-serial-communication-the-basics/. 

[12] Solomon Systech Ltd., SSD1309 128 × 64 Dot Matrix OLED/PLED Segment/Common Driver with Controller, Rev. 1.1, Jul. 2011. Available: https://files.waveshare.com/upload/9/9c/SSD1309Datasheet.pdf.

[13] Analog Devices, “Selecting the Right Passive and Discrete Components for Top System Performance,” Analog Devices Technical Article, 2020. [Online]. Available: https://www.analog.com/en/resources/technical-articles/selecting-the-right-passive-and-discrete-components.html.

[14] T. Tosi, F. Taffoni, M. Santacatterina, R. Sannino, and D. Formica, “Performance Evaluation of Bluetooth Low Energy: A Systematic Review,” Sensors, vol. 17, no. 12, Art. 2898, Dec. 2017, doi: 10.3390/s17122898.

[15] B. Park, “Understanding the Average Golf Score,” Skillest Blog, Feb. 26, 2025. [Online]. Available: https://skillest.com/blog/average-golf-score/.

[16] D. S. Tipper, Wireless Communication Fundamentals (Telcom 2700 Slides 2), Dept. of Information Science & Telecommunications, University of Pittsburgh, Pittsburgh, PA, USA, 2019. [Online]. Available: https://sites.pitt.edu/~dtipper/2700/2700_Slides2.pdf.

[17] Bluetooth SIG, Bluetooth LE Primer, v1.2.0, 2022. [Online]. Available: https://www.bluetooth.com/wp-content/uploads/2022/05/the-bluetooth-le-primer-v1.2.0.pdf.

[18] J. G. Proakis and M. Salehi, Digital Communications, 5th ed. New York, NY, USA: McGraw–Hill, 2008.
 
[19] TDK InvenSense, “ICM-20948 9-Axis MotionTracking Device Datasheet, Rev. 1.3,” 2016. [Online]. Available: https://invensense.tdk.com/wp-content/uploads/2016/06/DS-000189-ICM-20948-v1.3.pdf.

[20] USB Implementers Forum, Inc. (USB-IF), "Universal Serial Bus Specification, Revision 2.0," Apr. 27, 2000. [Online]. Available: www.usb.org/documents.

[21] M. Xiong, “ESD Fundamentals Part 2: IEC 61000‑4‑2 Rating,” Texas Instruments, SSZT871, Nov. 2017. [Online]. Available: https://www.ti.com/lit/ta/sszt871/sszt871.pdf.

[22] Littelfuse Inc., “SP0503BAHTG: TVS diode array datasheet,” SP050xBA Lead‑Free/Green Series, Oct. 21, 2022. [Online]. Available: https://www.littelfuse.com/assetdocs/tvs‑diode‑array‑spasp050xba‑lead‑freegreen‑datasheet.pdf.

[23]  Z. Zuo, “LC Filter for Power Supply Design Tips,” Cadence PCB Design & Analysis Blog, Aug. 30, 2023. Accessed: Nov. 27, 2025. [Online]. Available: https://resources.pcb.cadence.com/blog/lc-filter-for-power-supply-design-tips.

[24] Sierra Circuits, “Understanding Crosstalk in High-Speed PCB Design,” ProtoExpress, 2023. Accessed: Feb. 5, 2025. [Online]. Available: https://www.protoexpress.com/blog/crosstalk-high-speed-pcb-design/.

[25] NASA SSRI, “High-Speed PCB Design Guide,” NASA System Safety Research Institute, 2022. Accessed: Feb. 5, 2025. [Online]. Available: https://s3vi.ndc.nasa.gov/ssri-kb/static/resources/High-Speed%20PCB%20Design%20Guide.pdf.

[26] E. Bogatin, “Should You Worry About 90-Degree Bends in Circuit Board Traces?” Signal Integrity Journal, 2020. Accessed: Feb. 5, 2025. [Online]. Available: https://www.signalintegrityjournal.com/articles/2104-should-you-worry-about-90-degree-bends-in-circuit-board-traces.
