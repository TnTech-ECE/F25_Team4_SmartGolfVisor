# Detailed Design

## Function of the Subsystem

The **Heads-Up Display (HUD) subsystem** is responsible for presenting a clear and responsive graphical interface that allows the user to view key golf shot data metrics (ball speed, ball distance, launch angle, back spin, and side spin) and coaching suggestions while wearing the Smart Golf Visor. The subsystem integrates both hardware and software components to deliver real-time feedback with minimal delay, ensuring that the displayed information remains visually clear, stable, and responsive during use.


## Specifications and Constraints

This section outlines the specifications and constraints established for the HUD subsystem. These specifications define the minimum performance and functional requirements necessary to ensure proper operation within the overall system.

#### Specifications

Listed below are the specification shall statements:
*  The HUD **shall** project real-time data metrics and coaching suggestions in the user’s field of view.
*  The HUD **shall** operate at a minimum of 60 fps to ensure smooth visuals. 
*  The IMU **shall** provide real-time motion tracking to maintain display alignment with head movement. 
*  The HUD **shall** not obstruct the golfer’s natural line of sight or impair safety during use. 
*  The HUD **shall** communicate and synchronize seamlessly with the Communications and Power subsystems. 
*  All hardware **shall** be designed to integrate with the Physical Visor Design subsystem for structural compatibility. 
    
#### Constraints

Listed below are constraints the subsystem must abide by:

| Constraint | Standard | Relevance to Design | Design Rationale |
|------------|---------|-------------------|----------------|
| The HUD software shall ensure that displayed information does not overwhelm the user or distract from safe operation. | ANSI/HFES 100-2007: Human Factors Engineering of Computer Workstations, emphasizing display legibility, clear information organization, and limiting cognitive load. | Aligns with established usability standards to reduce cognitive load, improve legibility, and maintain safe operation during play. | By limiting cognitive load and organizing information clearly, the design ensures the golfer can quickly interpret key data without distraction, improving safety and usability. |
| Any image projection onto the lenses or glass shall remain within safe optical radiation limits. | ISO 15004-2: Ophthalmic instruments – Eye safety, setting limits for retinal and corneal irradiance over 250–2500 nm for continuous exposure (~4 hours). Specific limits: <br>• UV (250–400 nm): ≤ 0.021 mW/cm² <br>• Visible (400–700 nm): ≤ 0.069 mW/cm² <br>• Near-IR (700–1400 nm): ≤ 1.04 mW/cm² <br>• IR (1400–2500 nm): ≤ 3.47 mW/cm² | Ensures compliance with international eye safety standards, protecting the user from harmful optical radiation during prolonged use. | Maintaining irradiance below ISO limits prevents eye damage during typical golf usage (~4 hours), allowing safe deployment of the HUD in real-world conditions. |
| The display shall not obstruct the user’s view. | Human-factors design principle: Maintain unobstructed line of sight for safety and performance. | Maintains situational awareness and prevents interference with the golfer’s natural line of sight, supporting safety and performance. | Ensures the HUD does not interfere with natural vision, allowing the golfer to make accurate shots without distraction. |
| The display shall maintain clear and readable visuals under all expected lighting conditions. | Human-factors and display design best practices: Ensuring legibility and readability in varying environmental conditions. | Guarantees that information remains legible under varying lighting and environmental conditions, ensuring consistent usability. | Ensures that the golfer can reliably read key information outdoors in bright sunlight or shaded areas, maintaining performance and user confidence. |

 <p align="center">
      Table 1: HUD Subsystem Constraints
    </p>

## Overview of Proposed Solution

The proposed solution for the HUD subsystem is a program written in C that contains a graphic interface that includes the data metrics (ball speed, ball distance, launch angle, back spin, and side spin) calculated from the user's golf swing and coaching suggestions. This code is stored on the ESP32-S3 microcontroller and is displayed on the transparent OLED display that sits in front of one of the eyewear lenses of the Smart Golf Visor for the user to see. Below is a more in depth overview of the proposed solution.

#### Software
The software that will be used for programming is **Arduino IDE**. **Arduino IDE** is a free software that is compatible with many different boards including an ESP32-S3. It is a strong choice for the programming in the subsystem because it provides an easy setup, a simple workflow, and a massive number of libraries that support nearly every sensor, display, and communication module including the **Adafruit TDK InvenSense ICM-20948 9-DoF IMU** and **1.51" transparent OLED display** used in this subsystem.

#### IMU
The **ICM-20948** organizes its registers into four user banks (Bank 0-3) with each bank containing a distinct set of configuration and data registers. **User Bank 0** deals with sensor data output and basic device control (including interrupts). **User Bank 1** is used for sensor self-test and offset calibration. **User Bank 2** configures gyroscope and accelerometer ranges, filters, and sample rates. **User Bank 3** contains reserved and advanced calibration features.

For this application, the User Bank registers that will be used are **User Bank 0** to obtain sensor outputs and configure interrupts, and **User Bank 2** for initialization of measurement ranges and filtering. **User Bank 1** may be referenced during testing for offset calibration and sensor self-tests, but is not part of the core implementation. **User Bank 3** contains advanced calibration features and is not required for this application. 

To switch between the User banks, the **REG_BANK_SEL (0x7F)** register is used. Writing 0x00 selects Bank 0, 0x10 selects Bank 1, 0x20 selects Bank 2, and 0x30 selects Bank 3.

#### User Bank 0 Commands
* **Who_AM_I (0x00)** - confirms communication and device identity
* **PWR_MGMT_1 (0x06)** - wakes up the device, selects clock source
* **PWR_MGMT_2 (0x07)** - enable/disable individual accelerometer and gyroscope axes
* **ACCEL_XOUT_H/L (0x2D/0x2E)** - high/low bytes of accelerometer x-axis readings
* **ACCEL_YOUT_H/L (0x2F/0x30)** - high/low bytes of accelerometer y-axis readings
* **ACCEL_ZOUT_H/L (0x31/0x32)** - high/low bytes of accelerometer z-axis readings
* **GYRO_XOUT_H/L (0x33/0x34)** - high/low bytes of gyroscope x-axis readings
* **GYRO_YOUT_H/L (0x35/0x36)** - high/low bytes of gyroscope y-axis readings
* **GYRO_ZOUT_H/L (0x37/0x38)** - high/low bytes of gyroscope z-axis readings
* **INT_ENABLE (0X10)** - enables/disables specific interrupts
* **INT_STATUS (0x1A)** - checks if interrupts have occurred
* **INT_PIN_CFG (0x0F)** - configures interrupt pin behavior

#### User Bank 2 Commands
* **GYRO_SMPLRT_DIV (0x00)** - Sample rate divider for gyroscope
* **GYRO_CONFIG_1 (0X01)** - Configures the gyro range (±250–2000 dps), digital low pass filter, and bandwidth
* **ACCEL_SMPLRT_DIV_1 (0x10)** - Sample rate divider for upper byte of accelerometer
* **ACCEL_SMPLPRT_DIV_2 (0x11)** - Sample reate divider for lower byte of accelerometer
* **ACCEL_CONFIG (0x14)** - Configures accelerometer range (±2–16 g)
* **ACCEL_CONFIG_2 (0x15)** - Configures accelerometer digital low pass filter, bandwidth, and averging

All commands have been gathered from the ICM-20948 datasheet [1]

A **Madgwick Sensor Fusion Algorithm (SFA)** will be implemented to convert raw IMU data into usable orientation estimates. It outputs orientation as a quaternion, a four-dimensional complex number that can be used to represent the orientation of a ridged body or coordinate frame in three-dimensional space. Madgwick was selected because it provides fast convergence and and doesn't demand a lot of processing power, which makes it ideal for running in real time on the ESP32-S3. [2]

#### OLED Display
The **Transparent OLED display** is a monochrome 128x64 dot matrix used to project the swing data metrics (ball speed, ball distance, launch angle, back spin, and side spin) and coaching suggestions. The **SSD1309 driver chip** receives text and graphics data from the program running on the ESP32-S3. This driver chip contains a Graphic Display Data RAM (GDDRAM) with a capacity of 128x64 bits. GDDRAM stores the bit patterns that determine which pixels are lit on the display. The RAM is divided into eight pages, PAGE0 to PAGE7. Each page represents 8 vertical pixels (one byte) across all 128 columns. A page structure of the GDDRAM of the SSD1309 is shown in the figure below.

<p align="center">
  <img src="https://hackmd.io/_uploads/Hkg6Z-SbWl.png" alt="GDDRAM">
</p>

<p align="center"><em>Figure 1: GDDRAM Page Structure of SSD1309</em></p>

<p align="center">
  <img src="https://hackmd.io/_uploads/HkB-fWH--e.png" alt="GDDRAM Enlargement">
</p>

<p align="center"><em>Figure 2: Enlarged GDDRAM Page Structure of SSD1309</em></p>


The display ram is read and written by a page memory addressing mode. In this mode, the column address pointer automatically increments after each byte written. When the column pointer reaches the end of the row, it resets to the column start address. The page address pointer does not change automatically, so the user must set a new page address to access the next page of RAM. The sequence of page and column pointer movement in page addressing mode is shown in the figure below.

<p align="center">
  <img src="https://hackmd.io/_uploads/S1Q-B-Hb-l.png" alt="Figure 3">
  <br>
  <em>Figure 3 : Page and Column address point sequence movement for page addressing mode</em>
</p>


#### Display Initialization Commands
* **Display off (0xAE)** - keeps the screen off while configuring. 
* **Set Display Clock Frequency (0xD5)** - controls the internal clock speed for pixel driving
* **Set Multiplex Ratio (0xA8)** - defines the COM lines (rows) are active
* **Set Display Start Line (0x40)** - sets which RAM line maps to the top of the screen
* **Charge Pump Setting (0x8D, 0x14)** - enables the internal DC/DC charge pump
* **Set Contrast Control (0x30)** - sets constrast for visibility
* **Entire Display ON (0xA4)** - ensures the display shows RAM data
* **Set Normal Display (0xA6)** - set the pixels ON when data bit=1
* **Display ON (0xAF)** - Turns on screen with all settings applied

All commands have been gathered from the SSD1309 datasheet [3]
**Page addressing mode** is enabled during intialization by sending command 0x20 followed by 0x02. The starting RAM access pointer is defined by first setting the page start address (0xB0-0xB7), then the lower start column address (0x00-0x0F), and finally the upper start column address(0x10-0x1F).

After initialization, the text containing the swing data metrics, shot direction, and coaching suggestions is sent to the **SSD1309 driver chip** through the SPI MOSI line. The **U8G2.h** graphics library is used to simplify rendering and eliminates the need to manually manage font bitmaps and low-level addressing. High-level functions are used from this library such as **u8g2.setFont()** which selects the font style and size, allowing for an adjustment of appearance and readibility of the text on the display, **u8g2.drawStr(x,y,"text")** to draw a string at a specified coordinate on the display internal buffer, **u8g2.clearBuffer()** to clear the internal buffer, and **u8g2.sendBuffer()** to push buffer contents to the OLED. These functions will be used to ensure the text is correctly displayed providing a clear and responsive heads-up interface for the user.

To comply with **ANSI/HFES 100-2007 usability guidelines** [4], the HUD will ensure legibility and minimal cognitive load by using sans-serif fonts at least 8-10 pixels high for a typical visor viewing distance of 40-60 cm, with consistent spacing and high contrast to maximize clarity under outdoor lighting. To avoid overwhelming the golfer, the interface shall present no more than 3-5 data elements at a time, grouped locally and sequenced temporally to cycle through three sets of metrics. Ball speed and ball distance are shown first, followed by launch angle and back spin, and then side spin with a coaching suggestion. Each set of metrics shall remain visible for approximately 5 seconds, providing sufficient time for the golfer to scan and interpret the information before the display advances. The active display content will also be constrained to no more than 15% of the total 128x64 screen area ensuring no obstruction of the golfer's field of view.

Ensuring **ISO 15004-2 ophthalmic safety standards** [5] compliance with a transparent OLED display is highly achievable becasue these panels emit primarily in the visible spectrum with negligible UV and IR output, meaning the main safety lever is controlling visible irradiance. The display will enforce this through capping the contrast register to limit peak luminance, but the cap will be calibrated to remain high enough for clear visibility in an outdoor environment. The active pixel area of no more than 15% of the 128x64 matrix and metrics sets sequenced at 5 seconds reduce cumulative exposure. Together, these measures confirm that the HUD delivers swing metrics and coaching suggestions safely, without risk of ocular strain or photobiological hazard during prolonged use.



## Interface with Other Subsystems
The HUD subsytem interfaces with the following subsystems: Power, Communications, Visor Design.

#### Power

The HUD and Power subsystems will interface through the supply of a **3.3 V DC** power signal to the **Adafruit TDK InvenSense ICM-20948 9-DoF IMU** and **1.51" Transparent OLED display module**. Although the OLED display itself requires a driving voltage between 7-16V DC, it is paired with a **SSD1309 driver chip** that accepts 3.3 V DC input and internally boosts it to the required range. Both the ICM-20948 and SSD1309 driver chip are connected to the 3.3 V DC system rail. 

The components have relatively low current demands, with the SSD1309 driver chip drawing a maximum of 40 mA and the ICM-20948 drawing 0.76 mA. Together, the components draw a maximum current of 40.76 mA, which is well within the capabilities of the designated power subsystem.

#### Communications

The HUD and Communications subsystems interface through a SPI serial connection with the **ICM-20948** and the **SSD1309 driver chip**. Both devices use a standard 4-wire SPI communication with the following connections:
#### ICM-20948
* Serial Clock (SCLK)
* Master Out / Slave In (MOSI)
* Master In / Slave Out (MISO)
* Chip Select (CS)
* Interrupt (INT)

#### SSD1309 Driver Chip
* Serial Clock (SCLK)
* Master Out / Slave In (MOSI)
* Chip Select (CS)
* Data / Command (DC)
* Reset (RST)

The HUD subsystem directly interfaces with **Master Out / Slave In (MOSI)** and **Master In / Slave Out (MISO)** lines. The MOSI line allows the microcontroller to transmit data and commands to peripheral devices, such as initialization sequences, configuration registers, or read-request commands. Conversely, the MISO line enables the microcontroller to recieve data from the peripheral devices. 

The **ICM-20948** uses both MOSI and MISO, while the OLED display requires only MOSI. Input and Output data from these devices are stored in a single one-byte (8 bit) variable, initialized using the uint8_t type. Output data transmitted via MOSI line include commands, text, and the data metrics such as ball speed, ball distance, launch angle, back spin, and side spin. Input data received via MISO consist of accelerometer, gyroscope, and magnetometer readings from the ICM-20948.

The function **SPI.transmit(x)** is used to send or receive data. It simultaneously clocks out 8 bits on MOSI while clocking in 8 bits on MISO , provided the peripheral is designed to respond immediately which applies to the ICM-20948 but not to the OLED display. Below is a picture that reflects the interfacing characteristics of the 4-wire SPI configuration

<p align="center">
  <img src="https://hackmd.io/_uploads/H1zxlDB-be.png" alt="Figure 4: Serial Interface Characteristics (4-wire SPI)">
  <br>
  <em>Figure 4: Serial Interface Characteristics (4-wire SPI)</em>
</p>

#### Visor Design

The HUD and Visor Design will interface through a **printed circuit board (PCB)**, a **SSD1309 driver chip**, and a **1.51" Transparent OLED display** mounted onto the Smart Golf Visor. The **PCB** serves as the mounting platform provides wired connections between the IMU, OLED display, and the microcontroller. The standard thickness is 1.6 mm and shall be secured with M2 screws through 2.5 mm pre-drilled mounting holes. To prevent short circuits and allow airflow for thermal management, 5–10 mm nylon standoffs shall elevates the PCB above the visor surface. The **ICM-20948** will be soldered directly onto this PCB.

Adjacent to the **PCB enclosure** will be another enclosure that houses the **SSD1309 driver board**. This board has a measurement of 41 mm by 22.5 mm will also be secured using M2 screws through 2.5 mm pre-drilled mounting holes. Nylon standoffs of 5 t0 10 mm will again be used to provide electrical isolation and airflow.

The **transparent OLED display** will sit in an enclosure positioned in front of the right eye lens of the Smart Golf Visor. The display measures at  42.04 mm by 27.22 mm and will be secured to the frame of the Smart Golf Visor using M2 screws. This placement ensures that the display is properly aligned for the user while maintaining structural stability and thermal safety.

## Buildable Schematic 

#### Pseudocode
Below is the pseudocode for the HUD subsystem. This is subject to change during the testing and implementation phase.

#### Step 1 - Initialize ICM-20948 and OLED Display
The first step is to initialize the ICM-20948 so that motion can be detected and the OLED display to receive graphical data before anything is drawn to it. This step wakes the IMU from reset, enables the accelerometer and gyroscope readings, and then configures sensitivity, sample rate, digital filtering, and interrupt behavior. Once the IMU is configured, the display is prepared by configuring clock timing, power boost, contrast, and addressing mode, then enables the display and initializes the U8G2 graphics library. Font, text color, and scale are set afterward, allowing readable HUD elements to be rendered.

<p align="center">
  <img src="https://hackmd.io/_uploads/S1-YS59WWl.png" alt="Figure 5: Pseudocode for HUD subsystem (Step 1)">
  <br>
  <em>Figure 5: Pseudocode for HUD subsystem (Step 1)</em>
</p>

#### Step 2 - Configure IMU Data-Ready Interrupt

The next step is to configure and enable the IMU's data-ready interrupt, notifying whenever new sensor samples are available. When the interrupt triggers, the ISR clears the internal interrupt latch and sets a flag signaling the main loop to retrieve the new sensor data.

<p align="center">
  <img src="https://hackmd.io/_uploads/Hynk2qqbZl.png" alt="Figure 6: Pseudocode for HUD subsystem (Step 2)">
  <br>
  <em>Figure 6: Pseudocode for HUD subsystem (Step 2)</em>
</p>

#### Step 3 - Run Main Loop 

The main loop continuously monitors whether the IMU interrupt has been triggered. When the interrupt triggers, it reads and process acceleerometer and gyroscoppe measurements to determine if a golf swing has occured. If a swing is detected, the corresponding data metrics (ball speed, ball distance, launch angle, back spin, side spin) and coaching suggestion is retrieved and is sent to be updated to the HUD.

<p align="center">
  <img src="https://hackmd.io/_uploads/S1cXR59bZg.png" alt="Figure 7: Pseudocode for HUD subsystem (Step 3)">
  <br>
  <em>Figure 7: Pseudocode for HUD subsystem (Step 3)</em>
</p>

#### Step 4 - Read IMU Sensor Data

After the main loop detects an interrupt signaling that new IMU data is available, the readIMUdata() function is called. This function reads the high and low bytes from each accelerometer and gyroscope axis and combines them into complete sensor values, producing raw motion data used for further processing.

<p align="center">
  <img src="https://hackmd.io/_uploads/B1Lcgoc-bg.png" alt="Figure 8: Pseudocode for HUD subsystem (Step 4)">
  <br>
  <em>Figure 8: Pseudocode for HUD subsystem (Step 4)</em>
</p>

#### Step 5 - Process IMU Data with Madgwick SFA

Once the raw accelerometer and gyroscope readings are obtained, the main loop passes them to the Madgwick sensor fusion algorithm. This algorithm fuses the sensor data to calculate accurate orientation and rotational motion, providing the metrics needed to detect a golf swing and trigger HUD updates.

<p align="center">
  <img src="https://hackmd.io/_uploads/Skj3Mjc-Wx.png" alt="Figure 9: Pseudocode for HUD subsystem (Step 5 Part 1) [2]">
  <br>
  <em>Figure 9: Pseudocode for HUD subsystem (Step 5 Part 1) [2]</em>
</p>

<p align="center">
  <img src="https://hackmd.io/_uploads/Bk21ms5b-e.png" alt="Figure 10: Pseudocode for HUD subsystem (Step 5 Part 2) [2]">
  <br>
  <em>Figure 10: Pseudocode for HUD subsystem (Step 5 Part 2) [2]</em>
</p>

#### Step 6 - Render Metrics to HUD

After computing orientation and rotational motion from the sensor fusion algorithm and a golf swing is detected, the main loop sends over the data metrics (ball speed, ball distance, launch angle, back spin, and side spin) and coaching suggestion to the OLED display. The **renderHUD()** function cycles through different screens of this data, displaying the data for 5 seconds before automatically advancing to the next, providing the user with enough time to read and interpret each set of metrics without overwhelming the display or missing key information.


<p align="center">
  <img src="https://hackmd.io/_uploads/S1qKric-We.png" alt="Figure 11: Pseudocode for HUD subsystem (Step 6)">
  <br>
  <em>Figure 11: Pseudocode for HUD subsystem (Step 6)</em>
</p>

## Flowchart
<p align="center">
  <img src="https://hackmd.io/_uploads/SkFCmwSdWg.png" alt="Figure 12 : HUD Software Flowchart">
  <br>
  <em>Figure 12 : HUD Software Flowchart</em>
</p>

## BOM
<p align="center">
  <img src="https://hackmd.io/_uploads/Sy_xEvBdbe.png" alt="Figure 13 : HUD Subsystem BOM">
  <br>
  <em>Figure 13 : HUD Subsystem BOM</em>
</p>

## Analysis

#### Frame Rate
In order for the Transparent OLED Display to run at a minimum 60 frames per second (fps), the frame rate formula is

<p align= "center"> $F_{\text{FRM}} = \frac{F_{\text{osc}}}{D \times K \times \text{No. of Mux}}$ </p>

In this formula, Fosc represents the oscillator frequency of the SSD1309 chip which ranges from 360 kHz to 540 kHz. The parameter D is the clock divide ratio which scales the internal oscillator to adjust the pixel clock. The clock divide ratio is set at 1 by default and provides the fastest speed so it will be reatined in this application. The parameter K denotes the number of display clocks per row as is derived by

<p align = "center"> $K = \text{Phase 1 period} + \text{Phase 2 period} + K_0$ </p>

And by default, K equals 
<p align = "center"> $K=2+2+65=69$ </p>
The number of multiplex rows (Mux) is set to 64 for a 128x64 display. For a desired frame rate of 60 fps, the oscillator frequency can be calculated by rearranging the formula to 
<p align= "center"> $F_{\text{osc}} = F_{\text{FRM}} \times (D \times K \times \text{No. of Mux})$ </p>
. Substituting values gives a needed oscillator frequency of 265 kHz. Since the SSD1309's documented minimum oscillator frequency is 360 kHz, the display will actually run at a minimum of 81 fps, thereby exceeding the 60 fps requirement.

#### Madgwick Sensor Fusion Algorithm
The Madgwick Sensor Fusion Algorithm (SFA) is to ensure accurate orientation of the Smart Golf Visor is being portrayed. The Madgwick sensor fusion algorithm is designed to produce accurate orientation estimates by combining accelerometer and gyroscope measurements so that the strengths of each sensor cancel out the weaknesses of the other. The gyroscope  provides fast, smooth, short-term rotational changes but drifts over time while the accelerometer provides a long-term absolute reference for orientation by measuring gravity but is noisy during motion. Its primary advantage is that it corrects for the limitations of each sensor while requiring minimal processing power.

The orientation is represented using a quaternion, a four-dimensional complex number that can be used to represent the orientation of a ridgid body or coordinate frame in three-dimensional space. It is represented as 
<p align="center">
  <img src="https://hackmd.io/_uploads/r1tvWAj--l.png" alt="Quaternion Orientation Diagram">
  <br>
</p>

In the equation above, $q_1$ is the scalar component and $q_2,q_3,q_4$ form the vector component. Using quaternions help avoid gimbal lock, loses one degree of rotational freedom because two of its axes become parallel, allowing for smooth and continuous tracking of three-dimensional motion.

The gyroscope measures the angular rate in the x, y, and z axis of the sensor frame and is represented as the vector 
 <p align= "center"> $^{s}\omega = [0 \; \; \omega_x \; \; \omega_y \; \; \omega_z]$ </p>

Using this vector, the quaternion derivative that describes the rate of change of orientation of the earth frame relative to the sensor frame can be calculated as

<p align="center">
  <img src="https://hackmd.io/_uploads/Hk3cxk2bWx.png" alt="Screenshot 2025-12-01 213250">
  <br>
</p>

Now for the accurate orientation estimation, a gyroscope-based prediction is combined with a gradient-descent correction stage. The gyroscope prediction is gathered from the quaternion's instantaneous rate of change

<p align="center">
  <img src="https://hackmd.io/_uploads/BkjSB1nWbg.png" alt="Screenshot 2025-12-01 220344">
  <br>
</p>

where the previous quaternion is multiplied by the gyroscope-angular velocity quaternion to express the continuous-time rotational kinematics. A discrete Euler integration is then performed

<p align="center">
  <img src="https://hackmd.io/_uploads/r1FYUy3W-l.png" alt="Screenshot 2025-12-01 220405">
  <br>
</p>


propagating orientation forward over the sampling interval at 200 Hz. This provides from smooth and responsive short-term orientation tracking but accumulates drift over time. To correct this drift, a gradient-descent update is applied that aligns the predicted gravity direction with the accelerometer-measured gravity vector. The mismatch is expressed by the objective function

<p align="center">
  <img src="https://hackmd.io/_uploads/SkArsynbWe.png" alt="Screenshot 2025-12-01 222901">
  <br>
</p>


and its Jacobian,

<p align="center">
  <img src="https://hackmd.io/_uploads/BJq5o1nW-e.png" alt="Screenshot 2025-12-01 222924">
  <br>
</p>

the gradient of the error is computed by multiplying these two together identifying the direction in quaternion space that most rapidly reduces the gravity-alignment error. After normalizing and scaling this gradient by the gain β, the correction is subtracted from the gyro-derived quaternion rate to form the final update:

<p align="center">
  <img src="https://hackmd.io/_uploads/S1zqpk3WZg.png" alt="Screenshot 2025-12-01 223923">
  <br>
</p>


The gain β controls how strongly the orientation is corrected using accelerometer data versus relying on the gyroscope. A higher β improves responsiveness but can introduce jitter during quick head movements, while a lower β gives smoother output but allows slow drift. The figure below shows the effect of the gain β between 0 to 0.5 on the static and dynamic performance.

<p align="center">
  <img src="https://hackmd.io/_uploads/S1_1EehWWg.png" alt="Figure 14: Effect of β on proposed filter performance">
  <br>
  <em>Figure 14: Effect of β on proposed filter performance</em>
</p>

Because the sampling rate will be set at 200 Hz, the gain can be lower and from the graph provided a β of between 0.1 and 0.3 would be sufficient for both static and dynamic performance of the Smart Golf Visor.


#### Display Brightness

The OLED display must remain readable in a sunlit outdoor environment, which is one of the most challenging conditions for any transparent display. Waveshare does not publish a luminance value (in nits) for the OLED display, so brightness must be evaluated through indirect electrical parameters and optical design considerations. Similar transparent OLEDs displays on the market have a peak brightness in the range of 80-150 nits so it is reasonable to assume that the peak brightness will not exceed 150 nits.

The SSD1309 specifies that each segment driver provides an adjustable current from 0 to 320 µA in 256 steps. OLED luminance is directly proportional to the drive current by the equation

<p align= "center"> $L_{\text{panel}} = I_{\text{avg}}* η$ </p>
<p align= "center"> $I_{\text{avg}}$ is found by dividing the peak current (max of 320 µA) by the number of Mux rows for a 128x64 display (64 rows). </p>

<p align= "center"> $I_{\text{avg,max}} = \frac{I_{\text{peak,max}}}{\text{Mux rows}} = \frac{320\;\mu\text{A}}{64} = 5\;\mu\text{A}$ </p>


$η$ repesents the luminous efficacy of the OLED material which describes how efficiently electrical current is converted into visible light (units: cd/A). The luminous efficacy of the transparent OLED display is not documented, but from the equation presented above it is clear that panel luminance increases as the average current per multiplex row increases. Therefore, the maximum peak current will be used in an effort to maximize brightness of the display and minimize washout from ambient light. This outdoor readability of the display will be tested after the unit is acquired whether maximum drive current provides sufficient contrast.

If the max brightness setting of the OLED display is still insufficient for outdoor readability, physical shading and optical tinting will be incorporated to reduce ambient washout and increase perceived contrast, allowing the user to clearly view the display in bright conditions.

#### ANSI/HFES 100-2007 Compliance

The active display content is constrained to no more than 15% of the total display screen area (128x64) to prevent the user from being overwhelmed with information, which can lead to mental fatigue. The total number of pixels in the 128x64 display is 
<p align= "center"> $Total \; Pixels  = 128 \times 64 = 8192 \; pixels$ </p>

To remain ANSI/HFES 100-2007 complient, the maximum number of pixels that can be active at any one time is

<p align= "center"> $P = 8192 \times 0.15 \approx 1228 \; pixels$ </p>

Using a standard 6x8 font, where the average number of lit pixels per character ($P_c$) is approximately 18, the maximum number of characters that can be displayed simultaneously without exceeding the 15% limit is

<p align= "center"> $N_{\text{max}} = \frac{P}{P_c} = \frac{1228}{18} \approx 68 \;\text{characters}$ </p>


For the Smart Golf Visor application, the planned layout shows three sets of data metrics that cycle with a five-second timer, with the last set including one line of a coaching suggestion. Assuming a maximum of 20 characters per line, the total number of characters displayed at one time is

<p align= "center"> $N_{\text{display}} = 3 \times 20 = 60 \; characters$ </p>

This results in an estimated total pixel usage of

<p align= "center"> $P_{\text{used}} = 60 \times 18 = 1080 \; pixels$ </p>

Since 1080 pixels is below the 1228-pixel limit, the display design complies with ANSI/HFES 100-1207 requirement, ensuring that the user's view of the real environment remains unobstructed, cognitive load is minimized, and important information is legible without overwhelming the user.


#### ISO 15004-2 Compliance

The transparent OLED display emits primarily in the visible spectrum (400-700 nm) with negligible UV and IR emission for standard OLED materials. With a maximum display luminance assumed to be 150 $cd/m^2$, the retinal irradiance $E_{\text{ret}}$ can be approximated from display luminance using the equation

<p align = "center"> $E_{\text{ret}} = \frac{L_{\text{panel}} \cdot A_{\text{pupil}}}{f^2} \cdot \frac{1}{683}$ </p>


In this equation, $L_{\text{panel}}$ represents the peak OLED luminance (150 cd/m^2), $A_{\text{pupil}}$  which is the pupil area which can be calculated as

<p align = "center"> $A_{\text{pupil}} = \pi \left(\frac{d}{2}\right)^2$ </p>


f represents the eye-to-display distance, and 683 is the convervsion from $cd/m^2$ to $W/m^2$.

To show the worst case scenario for retinal irradiance, the pupil diameter  is set to 7 mm which is typical for indoor dim-light conditions. With this diameter the pupil area becomes

<p align = "center"> $A_{\text{pupil}} = \pi \left(\frac{0.007}{2}\right)^2 = 3.848 \times 10^{-5} \; \text{m}^2$ </p>


The eye-to-display distance f is set to 20 mm which is the closest a display can be to the eye, considering the distance from the cornea to the retina. Using these parameters, the retinal irradiance is calculated as

<p align = "center"> $E_{\text{ret}} = \frac{150 \cdot 3.848 \times 10^{-5}}{0.02^2} \cdot \frac{1}{683}= 0.0211 \; W/m^2 =0.00211  \; mW/cm^2$ </p>

ISO 15004-2 specifies that visible light retinal irradiance cannot exceed 0.069 mW/cm². The calculated value is well below this limit, even in the worst-case scenario where the entire display is on. Since no more than 15% of the display will be active at any time, the actual retinal irradiance will be proportionally lower, further ensuring compliance with ISO 15004-2.

## References

[1] ICM-20948 | Invensense, https://invensense.tdk.com/wp-content/uploads/2016/06/DS-000189-ICM-20948-v1.3.pdf. 

[2] X-io, https://x-io.co.uk/downloads/madgwick_internal_report.pdf. 

[3] Waveshare, https://www.waveshare.com/w/upload/9/9c/SSD1309Datasheet.pdf. 

[4] ANSI/HFES 100-2007 Human Factors Engineering of Computer Workstations, https://www.xybix.com/hubfs/ANSI_HFES_100-200727E2.pdf?t=1508538849931.

[5] “Ophthalmic instruments — Fundamental requirements and test methods — Part 2: Light hazard protection,” ISO, https://www.iso.org/obp/ui/en/#iso:std:iso:15004:-2:ed-2:v1:en. 

[6] B. Fan et al., “Influence of sampling rate on wearable IMU orientation estimation accuracy for Human Movement Analysis,” Sensors (Basel, Switzerland), https://pmc.ncbi.nlm.nih.gov/articles/PMC11991382/#sec2-sensors-25-01976. 

[7] Olikraus, “U8G2REFERENCE,” GitHub, https://github.com/olikraus/u8g2/wiki/u8g2reference#sendbuffer. 

[8] “OLED testing: Current efficiency, power efficiency and lifetime,” Ossila, https://www.ossila.com/pages/oled-testing-guide#:~:text=An%20example%20of%20Luminance%20vs,to%20follow%20the%20luminance%20decay. 

[9] OP22 retinal exposure assessment – horizontal or, https://files.cie.co.at/x046_2019/x046-OP22.pdf.
