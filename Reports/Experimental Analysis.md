# Experimental Analysis

## Designing Experiments
Below is a list of experiments that the SMART Golf Visor Team has conducted.

**Launch Monitor Battery Life Experiment**
- **Purpose**
The primary criterion being evaluated in this experiment is the operational endurance of the PiTrac Launch Monitor under a standard power load. The design objective is to achieve a minimum continuous runtime of 2 hours, which corresponds to the typical duration of a golf practice session. Ensuring this baseline requirement is met confirms that the high-draw components specifically the Raspberry Pi, dual cameras, and strobe system can operate reliably without voltage instability or unexpected shutdowns. From a user perspective, this test verifies that the hardware configuration is capable of sustaining performance throughout a full session without the need for mid-round recharging.

- **Procedure**
The experiment is conducted using the following steps:
    1. Fully charge the 12 V, 10 Ah battery to its maximum capacity.
    2. Connect the battery to the PiTrac Launch Monitor power rails.
    3. Power on the system and initialize all primary subsystems (Cameras, Strobe, and Processing unit).
    4. Start a digital timer immediately upon system initialization.
    5. Operate the system continuously under representative load conditions, including active BLE heartbeat and periodic shot-processing cycles.
    6. Monitor system performance until the device shuts down or the battery protection circuit engages due to low voltage.
    7. Stop the timer and record the total elapsed runtime.
    
    Equipment used: PiTrac Launch Monitor, 12 V 10 Ah battery, digital stopwatch/timer, and digital multimeter for voltage verification.
    
    Prior to testing, all system components are verified for functional integrity, and the battery is confirmed to be at its full-charge termination voltage.

- **Data Collection**
The primary metric recorded is the total system runtime, measured in hours and minutes. The runtime is defined as the duration from initial power-on to the point of functional failure or automated shutdown due to battery depletion. Each trial result is documented in a structured log to track performance against the 2-hour (120-minute) threshold.

- **Trials**
A single full-duration discharge trial is conducted to verify that the system meets the baseline operational requirement. While this provides an initial assessment of battery endurance, additional trials would improve the statistical confidence of the results.

- **Potential Biases**
Potential sources of bias in the launch monitor battery and power testing include multimeter and stopwatch measurement uncertainty, especially during the initialization phase where precise start times are synchronized. Another possible source of variation is that the baseline powered state, while testing primary hardware draw, does not account for the additional power fluctuations associated with active BLE transmission or CPU-intensive shot processing. To mitigate these effects, the battery was fully charged to a verified termination voltage prior to the start of the trial, and the system was maintained under consistent baseline conditions throughout the duration of the experiment.


**Visor Battery Life Experiment**
- **Purpose**:
  The purpose of this experiment is to measure the battery life of the SMART Golf Visor. One of the requirements we were given by our customer Matt Manzaro for this project is to design a system that has the battery power capable of lasting at minimum 9 holes during golf play. On average, 9 holes of golf takes around two hours so we will be testing that this system can at minimum last that amount of time. In addition to the two-hour use case, this experiment also evaluates the Smart Golf Visor power subsystem current draw and uses those measured values to estimate visor battery life relative to the expectations from the detailed design.

- **Procedure**:
 
  The experiment is conducted using the following steps:
    
    1. Power the Smart Golf Visor PCB using the intended battery/input source.
    2. Connect a digital multimeter/current meter to measure voltage and current.
    3. Measure and record the regulated output voltage.
    4. Measure and record the idle current of the system.
    5. Power cycle the system and measure the startup current.
    6. Operate the system with Bluetooth communication and OLED display active, then measure and record the current.
    7. Operate the system with only the OLED display active, then measure and record the current.
    8. Allow the system to return to idle and measure the current again.
    9. Repeat measurements in each state to obtain multiple data points.
    10. Use the collected data to estimate overall power consumption and determine expected runtime based on the 3.7 V, 4000 mAh visor battery.
    
    **Equipment used**: Smart Golf Visor power PCB, Smart Golf Visor Communications PCB, 1.51" Transparent OLED Display, 3.7 V 4000 mAh LiPo battery, digital multimeter/current meter. 
    
    Prior to testing, all equipment is powered on and verified to be functioning correctly.

- **Data Collection**:
    The data recorded during the experiment will include the voltage of the SMART Golf Visor battery, measured using a digital multimeter in volts, as well as regulated voltage readings, idle current readings, startup current readings, Bluetooth + OLED current readings, OLED-only current readings, and estimated battery life based on the highest measured active current. These data points will be documented in a table.

- **Trials**:

     Three trials will be conducted for idle current, startup current, and Bluetooth/OLED activity current in order to ensure repeatability and reliability of the collected data.

- **Potential Biases**:

  Potential sources of bias in the visor battery and power testing include multimeter and current meter measurement uncertainty, especially during startup where current spikes may happen quickly and exact peak values may depend on meter refresh rate. Another possible source of variation is that the simulated 9-hole round may not perfectly match the real pace of play or the exact power draw of real match conditions. To mitigate these effects, multiple readings and multiple trials were taken for each current condition and data was recorded consistently under the same indoor lab conditions.


**Visor Battery IEC 62133 Safety Operation Experiment**
- **Purpose**:

  The purpose of this experiment is to evaluate the thermal safety performance of the Smart Golf Visor power PCB during normal operating conditions. The primary metric measured is the PCB temperature after one hour of continuous runtime and whether the subsystem remains within safe operating limits defined in the detailed design. According to IEC 62133, the battery and associated system components shall operate safely within a temperature range of 0–60 °C. This directly supports the power subsystem design requirement, as the Smart Golf Visor is a wearable device that must avoid excessive heating during operation. From the customer perspective, maintaining safe operating temperatures is essential to ensure comfort and safe wear during extended use.

- **Procedure**:

  The experiment is conducted using the following steps:
    
    1. Power the Smart Golf Visor power PCB using the 3.7V 4000 mAh LiPo battery.
    2. Place the system in normal indoor lab conditions and ensure the PCB is not enclosed.
    3. Allow the system to run continuously for one hour under steady operating conditions.
    4. After one hour of runtime, measure and record temperature at multiple PCB locations.
    5. Measure and record temperature on key metal components on the PCB using a temperature measurement device.
    6. Repeat temperature measurements to ensure consistency and reduce measurement error.

    **Equipment used**: Smart Golf Visor power PCB, 3.7V 4000 mAh LiPo Battery, temperature measurement device.

    Prior to testing, all equipment is powered on and verified to be functioning correctly.

- **Data Collection**:

  The data recorded during this experiment will include temperature measurements taken after one hour of runtime. Temperature will be measured in degrees Fahrenheit and also converted to degrees Celsius for comparison to design limits. The temperature data will be documented in tables for analysis.

- **Trials**:

  Four temperature readings were taken after one hour of runtime at different board locations to capture variation across the PCB. Since this testing used one working prototype of the Smart Golf Visor power PCB, no duplicate prototypes were available for comparison.

- **Potential Biases**:

  One potential source of bias in this experiment is temperature probe placement, since slightly different contact locations may produce slightly different values. Thermal testing was also completed with the PCB not enclosed, so final temperatures inside the visor may vary somewhat from these measured values. To mitigate these issues, multiple readings were taken at several PCB locations and data was recorded consistently under the same indoor lab conditions.
  
**Launch Monitor to Visor Display Latency Experiment**
- **Purpose**:

    In this experiment, the primary criterion being evaluated is the end-to-end system latency from when shot metrics become available at the PiTrac Launch Monitor to when they are displayed on the Smart Golf Visor. The design objective is to achieve a latency of 2 seconds or less, ensuring timely feedback for the user. Providing low-latency data transfer allows users to receive shot metrics quickly without needing to reference an external application, improving overall usability during practice or gameplay. In addition to latency, the reliability of data transmission is evaluated by monitoring unsuccessful or lost data transfers.
    
- **Procedure**:
    The experiment is conducted using the following steps:

    1. Connect all devices within close proximity (within 2m).
    2. Start a session on the web application.
    3. Position a smartphone camera so that both the PiTrac display and Smart Golf Visor display are visible within the frame.
    4. Begin recording using slow-motion video (240 fps).
    5. Perform a golf shot to generate shot data from the launch monitor.
    6. Continue recording until the corresponding data is displayed on the Smart Golf Visor.
    7. Stop recording and save the video.
    8. Review the footage frame-by-frame to determine the time difference between the initial shot event and the display of data on the visor.
    9. Record the measured latency for the trial.
    10. Repeat for the required number of trials, noting any failed or missing data transmissions.
    
    **Equipment used**: PiTrac Launch Monitor, Smart Golf Visor, web application, smartphone with slow-motion recording capability, and laptop.
    
    All devices are powered on and verified to be functioning correctly prior to testing. 

- **Data Collection**:
    
    The latency is measured in milliseconds (ms) by counting the number of frames between the initiation of the shot event and the corresponding display on the Smart Golf Visor, then converting this value based on the video frame rate. These values are recorded in a structured table for each trial. Reliability is measured by tracking successful and unsuccessful data transmissions, with a running tally maintained for each attempt.

- **Trials**:
    
    A total of five trials are conducted to evaluate system performance. While this provides an initial assessment of latency and reliability, additional trials would improve the statistical confidence of the results.

- **Potential Biases**:
    Potential sources of variability include uncertainty in identifying the exact frame corresponding to the start of the shot event and the moment the data appears on the Smart Golf Visor display. Additionally, limitations in video playback resolution and manual frame counting may introduce small measurement errors. To mitigate these effects, slow-motion footage is reviewed multiple times to ensure consistent frame identification, and all trials are conducted under controlled indoor conditions to minimize external variability.
 

**BLE Connection Setup Experiment**
- **Purpose**:
    
    In this experiment, the primary criterion being evaluated is the time required to establish a connection between the PiTrac Launch Monitor, web application, and Smart Golf Visor. The project objective is to achieve a connection time of 10 seconds or less, ensuring a quick and efficient setup process. Meeting this requirement minimizes user wait time and allows for a seamless transition into match play or practice sessions. From a user experience perspective, fast and reliable connectivity ensures that shot data appears without noticeable delay, maintaining uninterrupted interaction with the system. In addition to connection time, the reliability of the connection process is evaluated by tracking failed connection attempts.


- **Procedure**:
    The experiment is conducted using the following steps:

    1. Place all devices within close proximity (within 2m).
    2. Begin recording using the smartphone’s slow-motion video mode, ensuring the stopwatch and device screens are visible.
    3. Initiate the connection process through the web application.
    4. Allow the system to attempt connection with both devices.
    5. Continue recording until both devices are successfully connected or the attempt fails.
    6. Stop recording and save the video.
    7. Review the footage frame-by-frame to determine the time at which the connection was successfully established.
    8. Record the measured connection time.
    9. If a connection attempt fails, record the failure and repeat the process.
    
    Equipment used: PiTrac Launch Monitor, Smart Golf Visor, laptop, smartphone with slow-motion recording capability, and stopwatch (visible in frame).
    
    Prior to testing, all devices are powered on and verified to be functioning correctly. A iPhone 17 Pro is positioned to capture both the device screens (indicating connection status) and a running stopwatch within the same frame. Testing is conducted indoors to provide a consistent environment.

    
- **Data Collection**:
    
    The time required to establish a successful connection will be measured in seconds using a stopwatch application. Data is recorded in a structured table that includes trial number and connection time. One data point is collected per successful trial. In addition, failed connection attempts are recorded as tally counts to evaluate the reliability of the connection process.

- **Trials**:

   A total of three trials are conducted to ensure consistency and allow for comparison of connection times across multiple attempts.

- **Potential Biases**:
    
    Potential sources of variability include interference from nearby Bluetooth or wireless devices, which may affect connection time and reliability. Additionally, while slow-motion video recording is used to improve timing accuracy, some uncertainty remains in identifying the exact frame at which a successful connection is established. This may introduce a small measurement error due to frame rate limitations and interpretation of visual indicators on the device screens. To mitigate these effects, all unnecessary Bluetooth devices in close proximity are turned off, and slow-motion footage is reviewed frame-by-frame to obtain the most accurate possible estimate of connection time.

**BLE Connection Range Experiment**
- **Purpose**:

    In this experiment, the primary criterion being evaluated is the connection range between all devices. The project objective is to provide a stable connection of at least 40 yards for open line of sight between devices, which corresponds to the average width of a fairway. Achieving this range ensures that users can maintain connectivity in practical scenarios, such as during cart path only conditions, where a golfer may be positioned on the opposite side of the fairway while still requiring a reliable connection between devices. In addition, failed connection attempts will be monitored to assess the overall reliability and robustness of the system, ensuring that connections can be consistently established and maintained under typical operating conditions.

- **Procedure**:
    
    The experiment is conducted using the following steps:
    
    1. Connect all devices within close proximity (within 2m).
    2. Verify that a stable BLE connection has been successfully established.
    3. Begin walking away slowly while carrying the phone running the web application, keeping all devices powered and active.
    4. Continuously transmit test shots to monitor connection stability.
    5. Measure the distance as separation increases.
    6. Conclude the trial when the connection becomes unstable or is lost, and record the maximum distance.
    
    **Equipment used**:  PiTrac Launch Monitor, Smart Golf Visor, Web Application, measuring tape, and laptop. 
    
    Prior to testing, all devices are powered on and verified to be functioning correctly. Additionally, no specific environmental condition is needed for the entirety of the expirement as it is varied between the two trials.

- **Data Collection**:

   The maximum distance at which a stable connection is maintained will be measured using a measuring tape and recorded in both yards and feet on a data sheet. A single data point is collected per trial at the point where the connection becomes unstable or is lost.

- **Trials**:
    The maximum range at which the devices still have a stable connection established, 1 trial will be done for both a clear line of sight and a obstructed line of sight.
    
- **Potential Biases**:
    
    Potential Biases that may introduce variability into the results, includes environmental interference from nearby electronic devices, signal reflections, and slight differences in surroundings between trials. Additionally, variations in device orientation, antenna positioning, and walking speed may affect the measured connection range. To mitigate these effects, device orientation is kept consistent, movement is performed at a steady pace, and testing is conducted in controlled environments whenever possible.
    
## Conducting Experiments

**Launch Monitor Battery Life Experiment**
A consistent procedure was followed to evaluate the operational endurance of the PiTrac Launch Monitor under a representative power load. In this experiment, the system was powered using a fully charged 12 V, 10 Ah battery, and operated continuously until shutdown to determine whether the device meets the minimum 2‑hour runtime requirement.

The total runtime was measured using a digital timer started at system initialization and stopped at the moment of functional failure or battery‑protection cutoff. Throughout the trial, the system was maintained under typical operating conditions, including active BLE heartbeat and periodic shot‑processing cycles, to ensure that the measured endurance reflects realistic usage. Voltage was periodically verified using a digital multimeter, and all observations were recorded immediately to maintain accuracy.

Results:

<div align="center">

| Trial | Total Runtime (hr) | Met 2-Hour Requirement |
|-------|--------------------|------------------------|
| 1     | 3                  | Yes                    |

*Table 1: Total Runtime Results*

</div>

The results confirm that the PiTrac Launch Monitor exceeds the 2‑hour operational requirement, demonstrating sufficient battery capacity and stable power delivery across all major subsystems.

Notes on Components:  
All components remained fully functional throughout the test, and no hardware damage occurred. The system operated with its standard configuration, including the Raspberry Pi processing unit, dual cameras, and strobe illumination system. The battery was verified at full termination voltage prior to testing, ensuring that the measured runtime reflects true full‑charge performance.

**Visor Battery Life Experiment**

A consistent procedure was followed to evaluate the power consumption and performance of the Smart Golf Visor power PCB across multiple operating states. In each trial, voltage and current measurements were recorded using a digital multimeter while the system transitioned through idle, startup, Bluetooth communication with OLED display active, OLED-only operation, and return-to-idle conditions.

The collected data was analyzed to determine average idle current, peak startup current, and active operating current, with multiple trials conducted to ensure consistency and accuracy. Voltage readings for the regulated rails were also measured and compared against detailed design expectations to verify proper operation. Using the maximum measured active current, battery life was estimated based on the visor’s 3.7 V, 4000 mAh Li-Po battery capacity, providing a conservative runtime estimate. All measurements were documented immediately, and results were compared against design requirements to assess overall system performance and reliability.

<div align="center">

| Rail | Reading 1 | Reading 2 | Average |
|------|-----------|-----------|---------|
| 5 V rail | 4.822 V | 4.800 V | 4.811 V |
| 3.3 V rail | 3.278 V | 3.289 V | 3.284 V |

*Table 2: Voltage Readings*

</div>

<div align="center">

| Trial | Idle Current |
|-------|--------------|
| 1     | 73 mA        |
| 2     | 74 mA        |
| 3     | 72 mA        |

*Table 3: Idle Current Readings*

</div>

Average idle current = 73.0 mA

<div align="center">

| Trial | Initial Current | Peak Startup Current | Settled Idle Current | Off-State Current | Final Current |
|-------|----------------|----------------------|----------------------|-------------------|---------------|
| 1     | 50 mA          | 152 mA               | 72 mA                | 16 mA             | 0 mA          |
| 2     | 80 mA          | 164 mA               | 73 mA                | 15 mA             | 0 mA          |
| 3     | 55 mA          | 146 mA               | 72 mA                | 15 mA             | 0 mA          |

*Table 4: Startup Current Readings*

</div>

Average peak startup current = 154.0 mA

<div align="center">

| Trial | Idle Current | Bluetooth + OLED | OLED Only | Return to Idle |
|-------|--------------|------------------|-----------|----------------|
| 1     | 71 mA        | 78 mA            | 75 mA     | 71 mA          |
| 2     | 71 mA        | 78 mA            | 76 mA     | 72 mA          |
| 3     | 73 mA        | 80 mA            | 77 mA     | 73 mA          |

*Table 5: Operating State Current Readings*

</div>

Average Bluetooth + OLED current = 78.7 mA  
Maximum measured Bluetooth + OLED current = 80 mA

<div align="center">

| Parameter | Detailed Design Expectation | Measured Result | Outcome |
|-----------|----------------------------|------------------|---------|
| 3.3 V regulated rail | 3.3 V ± 5% | 3.284 V average | Meets requirement |
| Peak-design current | ~440 mA | 164 mA max startup, 80 mA max active measured | Below expected max |

*Table 6: Comparison to Detailed Design Expectations*

</div>

**Battery life estimate using measured current and current battery:**

The visor battery in the detailed design is a 3.7 V, 4000 mAh Li-Po battery. Converting this to energy gives:

$$
E_{\text{battery}} = V \times Ah = 3.7 \times 4.0 = 14.8 \text{ Wh}
$$

Using the highest measured active current of **80 mA** at **5 V**, the corresponding load power is:

$$
P = VI = 5 \times 0.080 = 0.40 \text{ W}
$$

The estimated runtime is then:

$$
t = \frac{E_{\text{battery}}}{P} = \frac{14.8 \text{ Wh}}{0.40 \text{ W}} = 37.0 \text{ hours}
$$

The estimated runtime is used to evaluate whether the system meets the minimum 6-hour continuous operation requirement, as well as to assess its ability to exceed the approximately 2-hour runtime needed to support at least 9 holes of golf.

Notes on Components:
All components used during testing remained fully functional, and no parts were damaged during the experiment.

**Visor Battery IEC 62133 Safety Operation Experiment**
A consistent procedure was followed to evaluate the thermal performance of the Smart Golf Visor power PCB after one hour of continuous operation under normal conditions. In each trial, temperature measurements were taken at multiple locations on the PCB as well as on exposed metal components using a temperature measurement device to capture both general board heating and worst-case user-touchable surface temperatures.

The collected data was analyzed to determine average PCB temperature and average metal surface temperature, with multiple readings taken to ensure consistency and accuracy. These measured values were then compared against the detailed design requirement that user-touchable surfaces remain below 41 °C after 60 minutes of operation. All measurements were documented immediately, and the results confirmed that the system remained well within safe thermal limits, demonstrating acceptable thermal performance and safe operation for extended use.

**Temperature readings after an hour of runtime:**

  <div align="center">

| Reading | Temperature (°F) | Temperature (°C) |
|---------|------------------|------------------|
| 1       | 75.7 °F          | 24.3 °C          |
| 2       | 77.2 °F          | 25.1 °C          |
| 3       | 76.5 °F          | 24.7 °C          |
| 4       | 76.7 °F          | 24.8 °C          |

*Table 7: PCB Temperature Results*

</div>
 
 Average PCB temperature = 76.5 °F = 24.7 °C

  <div align="center">

| Reading | Temperature (°F) | Temperature (°C) |
|---------|------------------|------------------|
| 1       | 82.6 °F          | 28.1 °C          |
| 2       | 88.4 °F          | 31.3 °C          |
| 3       | 84.9 °F          | 29.4 °C          |
| 4       | 87.9 °F          | 31.1 °C          |

*Table 8: Metal Parts of PCB Temperature Results*

</div>

  Average metal temperature = 85.95 °F = 30.0 °C


  The measured temperatures are used to evaluate whether the system meets the requirement of keeping user-touchable surfaces below 41 °C after 60 minutes of operation, as well as to confirm safe thermal performance based on the maximum recorded temperature of 30 °C on metal surfaces. 
  
  Notes on Components:
  All components used during testing remained fully functional, and no parts were damaged during the experiment.

**Launch Monitor to Visor Display Latency Experiment**

A consistent procedure was followed to measure the end-to-end latency between the PiTrac Launch Monitor and the Smart Golf Visor. In each trial, a golf shot was performed while recording both the launch monitor output and visor display using slow-motion video.

The latency was determined by reviewing the recorded footage frame-by-frame and measuring the time difference between the initial shot event and when the corresponding data was displayed on the Smart Golf Visor. Measurements were documented immediately to ensure accuracy, and any unsuccessful or missing data transmissions were recorded to assess system reliability.

<div align="center">

| Trial | Latency (ms) | Successful Transmission |
|-------|---------------|------------------------|
| 1     | 464           | Yes                    |
| 2     | 324           | Yes                    |
| 3     | 466           | Yes                    |
| 4     | 407           | Yes                    |
| 5     | 480           | Yes                    |

*Table 9: Latency Test Results*

</div>

Average Time: 428 ms
Failed Transmissions: 0

The results are used to evaluate whether the system meets the 2-second latency requirement, as well as to assess the consistency and reliability of data transmission across multiple trials.

Notes on Components:
All components used during testing remained fully functional, and no parts were damaged during the experiment. However, the custom PCB developed for the Communications subsystem was replaced with an ESP32 DevKit-V1 to provide reliable BLE functionality for the Smart Golf Visor. The custom PCB did not support proper flashing of the ESP32, rendering it inoperable for this experiment.

**BLE Connection Setup Experiment**

A consistent procedure was followed to measure the time required to establish a connection between the PiTrac Launch Monitor, web application, and Smart Golf Visor. In each trial, all devices were placed within close proximity and a connection attempt was initiated while recording the process using slow-motion video.

The time at which the connection was successfully established was determined by reviewing the recorded footage frame-by-frame, using a visible stopwatch for reference. Measurements were documented immediately to ensure accuracy, and failed connection attempts were recorded to assess overall reliability.

<div align="center">

| Trial | Connection Time (s) | Successful Connection |
|-------|---------------------|------------------------|
| 1     | 1.36                | Yes                    |
| 2     | 1.92                | Yes                    |
| 3     | 1.63                | Yes                    |

*Table 10: BLE Connection Time Results*

</div>
	
Average Time: 1.6 seconds
Failed Attempts: 0

The results are used to evaluate whether the system meets the 10-second connection requirement, as well as to assess the consistency and reliability of the connection process across multiple trials.

Notes on Components:
All components used during testing remained fully functional, and no parts were damaged during the experiment. The ESP32 DevKit-V1 was used in place of the custom PCB for the Communications subsystem, consistent with the configuration described in the Launch Monitor to Visor Display Latency Experiment, to ensure reliable BLE functionality.

**BLE Connection Range Experiment**

A consistent procedure was followed to measure BLE connection range under two conditions: an open environment and an environment with a concrete wall obstruction. In each trial, the transmitter and receiver were gradually separated while maintaining consistent orientation and operating conditions until the connection became unstable or was lost.

The distance at which the BLE connection became unstable or disconnected was recorded as the maximum reliable range. Measurements were documented immediately to ensure accuracy, and the data was organized clearly to support comparison between environments.


<div align="center">

| Environment               | Range (ft) | Range (yd) |
|---------------------------|------------|------------|
| Open Line-of-Sight        | 384        | 128        |
| Concrete Wall Obstruction | 68         | 23         |

*Table 11: Communication Range Results*

</div>

Notes on Components:
All components used during testing remained fully functional, and no parts were damaged during the experiment. The ESP32 DevKit-V1 was used in place of the custom PCB for the Communications subsystem, consistent with the configuration described in previous experiments, to ensure reliable BLE communication between the PiTrac Launch Monitor and Smart Golf Visor.

## Analyzing Results and Drawing Conclusions

**Launch Monitor Battery Life Experiment**

The battery life results show a clear and dependable performance profile for the PiTrac Launch Monitor. The system maintained continuous operation for 3 hours, providing a comfortable margin above the 2‑hour requirement. This extended runtime suggests that the power subsystem is not operating near its limits and that the overall load—cameras, strobe, Raspberry Pi, and BLE activity—remains well within what the battery can reliably support.

Some variability is expected in endurance testing, and a few factors could influence the exact runtime. Manual timing introduces small uncertainties, and the use of a single discharge cycle limits the ability to observe natural variation across repeated tests. Additionally, while the system was operated under representative conditions, real‑world usage may involve more frequent shot processing or heavier BLE traffic, which could slightly alter power consumption. Even so, the substantial runtime margin observed here reduces the likelihood that these factors would meaningfully impact the system’s ability to meet its requirement.

Overall, the data indicates that the launch monitor comfortably satisfies the 2‑hour operational benchmark. The absence of voltage instability, brownouts, or subsystem failures reinforces the reliability of the power architecture and suggests that the battery and distribution circuitry are appropriately sized for the current hardware configuration. The system’s stable behavior throughout the test also implies that runtime performance is driven primarily by predictable load characteristics rather than environmental or operational variability.

While the results are strong, expanding the test set would provide a more complete picture of long‑term performance. Additional trials across multiple charge cycles, different temperatures, and varying processing intensities would help confirm consistency and identify any edge‑case endurance limitations. These follow‑up experiments would deepen confidence in the system’s readiness for extended real‑world use.

**Visor Battery Life Experiment**
The battery life analysis indicates that the Smart Golf Visor power subsystem performs well within design expectations. Using the maximum measured active current of 80 mA provides a conservative, worst-case estimate, resulting in an expected runtime of approximately 37 hours. This significantly exceeds both the minimum 6-hour continuous operation requirement and the estimated 2-hour usage needed for a 9-hole round of golf, demonstrating strong overall power efficiency.

The consistency of current measurements across trials suggests stable system behavior with no abnormal power spikes during operation. Additionally, the relatively low active and idle current values indicate that the system is not heavily power-constrained and has ample margin for reliable performance.

Some uncertainty exists due to simplifying assumptions in the calculation, such as constant voltage and continuous operation at peak current. In real-world use, the system will cycle between different states, often operating below peak consumption, meaning actual battery life is likely longer than estimated. Factors such as battery aging, efficiency losses, and environmental conditions may slightly reduce performance, but are unlikely to impact the system enough to violate design requirements.

Overall, the results confirm that the power subsystem is capable of supporting extended operation with a large safety margin, and no additional design changes are necessary based on the current data.

**Visor Battery IEC 62133 Safety Operation Experiment**
The collected temperature data shows that the Smart Golf Visor power PCB maintains safe operating temperatures during extended use. After one hour of continuous operation, the PCB temperature averaged 24.7 °C and the metal components averaged 30.0 °C, both well below the 41 °C limit for user-touchable surfaces defined in the detailed design. This indicates that the system does not generate excessive heat and remains safe for wearable use.

The measurements are consistent across multiple readings, suggesting stable thermal performance without significant fluctuations or localized overheating. The relatively small temperature increase between the PCB and metal components also indicates effective heat distribution across the board.

While minor sources of error may exist, such as ambient temperature variations or slight differences in measurement location, these do not significantly impact the overall conclusion due to the large margin below the thermal limit. Even with potential increases in temperature from enclosure effects or reduced airflow, the system is expected to remain within safe operating conditions.

Based on these results, the power subsystem meets the required thermal safety criteria and demonstrates reliable performance without risk of overheating during normal operation.

**Launch Monitor to Visor Display Latency Experiment**

The latency measurements show a tightly grouped set of values, indicating consistent system performance across all trials. The absence of large variation or outliers suggests that latency is stable and not significantly influenced by minor differences in shot conditions or system state. This consistency is critical, as predictable performance contributes more to user experience than occasional fast but inconsistent responses.

Potential sources of uncertainty include identifying the exact frame corresponding to the initiation of the shot and the moment the data appears on the Smart Golf Visor. While slow-motion video reduces this uncertainty, small timing discrepancies may still be present due to frame resolution and interpretation of visual indicators. However, these errors are minimal relative to the measured latency values.

Based on the observed data, the system meets the 2-second latency requirement with substantial margin, 428 ms average. The consistently low latency values indicate that the system is capable of delivering near real-time feedback, allowing users to receive shot metrics quickly without interrupting their workflow.

Additionally, the absence of failed transmissions suggests that the communication pipeline between the launch monitor and visor is reliable under controlled conditions. This indicates that latency is primarily determined by processing and transmission time rather than instability in the communication link.

While the results are consistent, the limited number of trials introduces some uncertainty. Additional testing under varied environmental conditions would help confirm that performance remains stable across different use cases.

**BLE Connection Setup Experiment**

The connection time data demonstrates consistent and repeatable system behavior across all trials. The relatively small variation in connection times indicates that the initialization process is stable and not highly sensitive to environmental or operational changes. This level of consistency is important for ensuring a predictable and user-friendly setup experience.

Potential sources of variability include minor measurement uncertainty from frame-by-frame analysis and possible interference from surrounding wireless devices. Although these factors may introduce small deviations, their impact on overall performance is minimal due to controlled testing conditions.

Based on the observed data, the system meets the 10-second connection time requirement with significant margin. The consistently low connection times indicate that users can quickly establish connections and begin using the system without delay, supporting a seamless transition into practice or gameplay.

The absence of failed connection attempts further demonstrates the reliability of the connection process, suggesting that the system can consistently establish connections without requiring retries or user intervention. This reliability indicates that connection performance is primarily dependent on system initialization rather than external variability.

Due to the limited number of trials conducted, additional testing would be beneficial to confirm long-term consistency and identify any less frequent connection issues.

**BLE Connection Range Experiment**

The results demonstrate a strong dependence of connection range on environmental conditions. Under open line-of-sight conditions, the system achieves a range well beyond the required threshold, indicating that the communication system is capable of supporting typical use scenarios with significant margin.

In contrast, the obstructed condition results in a substantial reduction in range, highlighting the sensitivity of BLE signals to physical barriers. This indicates that while the system performs reliably in open environments, its performance is significantly impacted when signal paths are blocked.

Potential sources of variability include environmental interference, slight differences in device orientation, and measurement precision when determining the exact point of disconnection. While these factors may influence the exact measured values, they do not affect the overall trend observed between the two conditions.

Based on the observed data, the system meets and exceeds the 40-yard connection range requirement under open conditions. This ensures reliable operation in practical scenarios, such as across a fairway. The reduced performance in the obstructed condition indicates that physical barriers are the primary limiting factor in BLE communication, directly attenuating signal strength and reducing effective range.

The clear difference between conditions suggests a causal relationship between line-of-sight availability and connection performance, reinforcing the importance of minimizing obstructions during use.

Because only a single trial was conducted per condition, the results should be interpreted as approximate. Additional trials would help quantify variability and improve confidence in the measured ranges.


## Documenting and Tracking Components

- **Initial Inventory**

  Below is a list of every item ordered and is divided up into the different subsystems of this project

***Visor Power Subsystem***
<div align="center">

| Item Name                                      | Vendor        | Cost ($) | Quantity | Assigned Storage Location |
|-----------------------------------------------|---------------|----------|----------|----------------------------|
| 3.7V 4000mAh LiPo Battery                     | Amazon        | $13.49   | 1        | Capstone Lab Table 3       |
| Inline Fuse Holder (Waterproof)               | Amazon        | $7.99    | 1        | Capstone Lab Table 3       |
| Hook-Up Wire (26 AWG)                         | DigiKey       | $1.93    | 1        | Capstone Lab Table 3       |
| 700 mA Glass Fuse                             | DigiKey       | $1.38    | 2        | Capstone Lab Table 3       |
| SUD50P06-15 P-Channel MOSFET                  | DigiKey       | $0.70    | 5        | Capstone Lab Table 3       |
| 1kΩ 0402 Resistor                             | DigiKey       | $0.00007 | 30       | Capstone Lab Table 3       |
| SMAJ5.0CA TVS Diode                           | DigiKey       | $0.04    | 7        | Capstone Lab Table 3       |
| Green 0402 LED                                | DigiKey       | $0.01    | 37       | Capstone Lab Table 3       |
| 10µF 50V MLCC Capacitor (0805)                | DigiKey       | $0.07    | 29       | Capstone Lab Table 3       |
| 1.5µH Power Inductor                          | DigiKey       | $0.05    | 7        | Capstone Lab Table 3       |
| TPS63031 Buck-Boost Converter                 | DigiKey       | $0.99    | 5        | Capstone Lab Table 3       |
| ETA9740 Battery Charging IC                   | DigiKey       | $0.27    | 5        | Capstone Lab Table 3       |
| LTC4412 Ideal Diode Controller                | DigiKey       | $3.05    | 5        | Capstone Lab Table 3       |
| SPDT Slide Switch (SMD)                       | DigiKey       | $1.17    | 5        | Capstone Lab Table 3       |
| 1µH Power Inductor                            | DigiKey       | $0.06    | 9        | Capstone Lab Table 3       |
| JST 2-Pin Connector (2mm)                     | DigiKey       | $0.19    | 5        | Capstone Lab Table 3       |
| USB Type-C 16-Pin Connector                   | DigiKey       | $0.08    | 5        | Capstone Lab Table 3       |
| 100nF 0402 Capacitor                          | DigiKey       | $0.0013  | 35       | Capstone Lab Table 3       |
| 10µF 6.3V 0402 Capacitor                      | DigiKey       | $0.01    | 35       | Capstone Lab Table 3       |
| 3A Resettable Fuse                            | DigiKey       | $0.11    | 7        | Capstone Lab Table 3       |
| 5.1V Zener Diode                              | DigiKey       | $0.03    | 15       | Capstone Lab Table 3       |
| 56kΩ 0402 Resistor                            | DigiKey       | $0.0005  | 20       | Capstone Lab Table 3       |
| 2N7002 N-Channel MOSFET                       | DigiKey       | $0.02    | 10       | Capstone Lab Table 3       |
| 100kΩ 0402 Resistor                           | DigiKey       | $0.008   | 20       | Capstone Lab Table 3       |
| 5.1kΩ 0402 Resistor                           | DigiKey       | $0.008   | 20       | Capstone Lab Table 3       |
| AON7403 P-Channel MOSFET                      | DigiKey       | $0.18    | 5        | Capstone Lab Table 3       |
| 10nF 0402 Capacitor                           | DigiKey       | $0.0014  | 20       | Capstone Lab Table 3       |
| 10kΩ 0402 Resistor                            | DigiKey       | $0.0008  | 20       | Capstone Lab Table 3       |
| 0Ω 0805 Resistor                              | DigiKey       | $0.0021  | 20       | Capstone Lab Table 3       |
| 91kΩ 0402 Resistor                            | DigiKey       | $0.0003  | 20       | Capstone Lab Table 3       |
| Power PCB Board                               | DigiKey       | $0.42    | 5        | Capstone Lab Table 3       |
| LiPo Charger + Boost Board                    | Seeed Studio  | $4.99    | 1        | Capstone Lab Table 3       |

*Table 12: Visor Power Subsystem Item List*
</div>

***Visor Hardware Subsystem***
<div align="center">

| Item Name                                         | Vendor  | Cost ($) | Quantity | Assigned Storage Location |
|--------------------------------------------------|---------|----------|----------|----------------------------|
| 2200PCS M2 M3 M4 M5 Metric Screws Assortment Kit | Amazon  | $19.99   | 1        | Capstone Lab Table 3       |
| Polymaker ASA Filament 1.75mm Black              | Amazon  | $29.99   | 2        | Capstone Lab Table 3       |
| Klein Tools 60161 Safety Glasses                 | Amazon  | $14.98   | 1        | Capstone Lab Table 3       |
| Klein Tools 60483 Safety Glasses Gasket          | Amazon  | $3.99    | 1        | Capstone Lab Table 3       |
| Ducky Electro-Seal Waterproofing Spray           | Amazon  | $28.95   | 1        | Capstone Lab Table 3       |
| Ginsco Heat Shrink Tubing Kit (580 pcs)          | Amazon  | $7.99    | 1        | Capstone Lab Table 3       |

*Table 13: Visor Hardware Subsystem Item List*
</div>

***Communications Subsystem***

<div align="center">

| Item Name | Vendor | Cost ($) | Quantity | Assigned Storage Location |
|------------|--------|----------|----------|----------------------------|
| ESP32-S3-WROOM1-N16R2 | DigiKey | $12.26 | 2 | Capstone Lab Table 3 |
| 10 µF Capacitor | DigiKey | $1.39 | 1 | Capstone Lab Table 3 |
| 1 µF Capacitor | DigiKey | $0.26 | 1 | Capstone Lab Table 3 |
| 0.1 µF Capacitor | DigiKey | $0.08 | 4 | Capstone Lab Table 3 |
| 10 µH Inductor | DigiKey | $0.10 | 1 | Capstone Lab Table 3 |
| 5.1 kΩ Resistor | DigiKey | $0.10 | 2 | Capstone Lab Table 3 |
| 10 kΩ Resistor | DigiKey | $0.10 | 2 | Capstone Lab Table 3 |
| 10 Ω Resistor | DigiKey | $0.10 | 1 | Capstone Lab Table 3 |
| 43.2 kΩ Resistor | DigiKey | $0.10 | 1 | Capstone Lab Table 3 |
| 22.1 kΩ Resistor | DigiKey | $0.10 | 1 | Capstone Lab Table 3 |
| USB-C Socket Breakout | DigiKey | $1.94 | 1 | Capstone Lab Table 3 |
| USB to UART Bridge IC | DigiKey | $12.42 | 3 | Capstone Lab Table 3 |
| TVS Protection Diode | DigiKey | $0.53 | 1 | Capstone Lab Table 3 |
| 7 Pin Connector Headers | DigiKey | $1.12 | 1 | Capstone Lab Table 3 |
| Push Buttons | DigiKey | $0.61 | 2 | Capstone Lab Table 3 |
| PCB | JLC PCB | $7.00 | 5 | Capstone Lab Table 3 |

*Table 14: Communications Subsystem Item List*
</div>

***HUD Subsystem***
<div align="center">

| Item Name | Vendor | Cost ($) | Quantity | Assigned Storage Location |
|-----------|--------|----------|----------|----------------------------|
| 1.51" Transparent OLED Display | NewEgg | $52.59 | 1 | Capstone Lab Table 3 |
| BMI323 IMU Sensor | DigiKey | $3.27 | 1 | Capstone Lab Table 3 |
| FFC Flexible Ribbon Cable 24-pin, 0.5 mm pitch, 100mm (Type B) | Amazon | $5.69 | 2 | Capstone Lab Table 3 |
| 24-pin 0.5mm FFC/FPC Extender | Adafruit | $0.95 | 1 | Capstone Lab Table 3 |

*Table 15: HUD Subsystem Item List*
</div>

***Launch Monitor Subsystem***  
<div align="center">

| Item Name | Vendor | Cost ($) | Quantity | Assigned Storage Location |
|-----------|--------|----------|----------|----------------------------|
| Raspberry Pi 5 FPC Camera Cable - 22-pin 0.5mm to 15-pin 1mm - 500mm long | Adafruit | $2.50 | 2 | Capstone Lab Table 3 |
| 6mm 3MP wide angle lens | Adafruit | $49.30 | 1 | Capstone Lab Table 3 |
| USB COB LED strip lights – 2M | Amazon | $6.99 | 1 | Capstone Lab Table 3 |
| USB-C to USB-C Cable | Amazon | $7.49 | 1 | Capstone Lab Table 3 |
| 12V 10Ah LiFePO₄ battery + charger | Amazon | $94.99 | 1 | Capstone Lab Table 3 |
| Heatsink assembly for 100W COB | Amazon | $23.99 | 1 | Capstone Lab Table 3 |
| IMX296 mono global shutter camera module | Amazon | $43.00 | 2 | Capstone Lab Table 3 |
| Mirror acrylic sheet | Amazon | $12.99 | 1 | Capstone Lab Table 3 |
| 100 µF Aluminum - Polymer Capacitors | DigiKey | $0.41 | 1 | Capstone Lab Table 3 |
| 330uF Polymer Capacitor | DigiKey | $1.52 | 1 | Capstone Lab Table 3 |
| 10µF 25V Ceramic Capacitor | DigiKey | $1.39 | 1 | Capstone Lab Table 3 |
| 1µF 10V Ceramic Capacitor | DigiKey | $0.26 | 1 | Capstone Lab Table 3 |
| 0.1µF 25V Ceramic Capacitor | DigiKey | $0.08 | 4 | Capstone Lab Table 3 |
| 10µH Inductor | DigiKey | $0.10 | 1 | Capstone Lab Table 3 |
| 5.1kΩ Resistor | DigiKey | $0.05 | 2 | Capstone Lab Table 3 |
| 10kΩ Resistor | DigiKey | $0.10 | 2 | Capstone Lab Table 3 |
| 10Ω Resistor | DigiKey | $0.10 | 1 | Capstone Lab Table 3 |
| 43.2kΩ Resistor | DigiKey | $0.10 | 1 | Capstone Lab Table 3 |
| 22.1kΩ Resistor | DigiKey | $0.10 | 1 | Capstone Lab Table 3 |
| TVS Diode | DigiKey | $0.53 | 1 | Capstone Lab Table 3 |
| 40mm Cooling Fan | DigiKey | $4.14 | 1 | Capstone Lab Table 3 |
| Inline Fuse Holder | DigiKey | $3.95 | 1 | Capstone Lab Table 3 |
| 7.5A Blade Fuse | DigiKey | $3.04 | 4 | Capstone Lab Table 3 |
| 5V 5A Step-down Regulator | DigiKey | $34.95 | 1 | Capstone Lab Table 3 |
| P-channel MOSFET | DigiKey | $1.09 | 1 | Capstone Lab Table 3 |
| TVS SMBJ18A | DigiKey | $0.16 | 1 | Capstone Lab Table 3 |
| 1000µF 25V Electrolytic Capacitor | DigiKey | $1.42 | 2 | Capstone Lab Table 3 |
| 1000µF 10V Electrolytic Capacitor | DigiKey | $1.08 | 2 | Capstone Lab Table 3 |
| Green SMD LED | DigiKey | $0.58 | 2 | Capstone Lab Table 3 |
| 1kΩ Resistor | DigiKey | $0.10 | 15 | Capstone Lab Table 3 |
| Micro-Fit Connector Housing 2-pos | DigiKey | $0.34 | 6 | Capstone Lab Table 3 |
| Micro-Fit Crimp Terminal | DigiKey | $0.10 | 10 | Capstone Lab Table 3 |
| High Power IR Emitter | DigiKey | $2.82 | 10 | Capstone Lab Table 3 |
| IR Acrylic Sheet | Edmund Optics | $21.00 | 1 | Capstone Lab Table 3 |
| P-channel MOSFET (JLCPCB) | JLCPCB | $0.70 | 5 | Capstone Lab Table 3 |
| 1kΩ Resistor (JLCPCB) | JLCPCB | $0.00007 | 30 | Capstone Lab Table 3 |
| TVS Diode 5V (JLCPCB) | JLCPCB | $0.038 | 7 | Capstone Lab Table 3 |
| Green LED (JLCPCB) | JLCPCB | $0.0078 | 4 | Capstone Lab Table 3 |
| 10µF Capacitor (JLCPCB) | JLCPCB | $0.07 | 2 | Capstone Lab Table 3 |
| USB-C Connector | JLCPCB | $0.08 | 5 | Capstone Lab Table 3 |
| Resettable Fuse | JLCPCB | $0.11 | 7 | Capstone Lab Table 3 |
| Zener Diode | JLCPCB | $0.03 | 15 | Capstone Lab Table 3 |
| N-channel MOSFET | JLCPCB | $0.02 | 10 | Capstone Lab Table 3 |
| Power PCB Board | JLCPCB | $0.42 | 5 | Capstone Lab Table 3 |
| Strobe Connector Board | JLCPCB | $2.00 | 5 | Capstone Lab Table 3 |
| PiTrac Power Board | JLCPCB | $4.37 | 5 | Capstone Lab Table 3 |
| Raspberry Pi 5 (8GB) | SparkFun | $130.00 | 1 | Capstone Lab Table 3 |
| Raspberry Pi Active Cooler | SparkFun | $15.00 | 1 | Capstone Lab Table 3 |

*Table 16: Launch Monitor Subsystem Item List*
</div>

- **Usage Tracking**
    **Visor Battery Life Experiment**
    The following items were used for this experiment: All items from the Visor Power Subsystem, Visor Hardware Subsystem, Communication Subsystem, and HUD Subsystem (See above for list of items)
  
    **Visor Battery IEC 62133 Safety Operation Experiment**
    The following items were used for this experiment: All items from the Visor Power Subsystem, Visor Hardware Subsystem, Communication Subsystem (See above for list of items)
    
    **Launch Monitor to Visor Display Latency Experiment**
    The following items were used for this experiment: All items from the Visor Power Subsystem, Visor Hardware Subsystem, Communication Subsystem, and HUD Subsystem (See above for list of items)
    
    **BLE Connection Setup Experiment**
    The following items were used for this experiment: All items from the Visor Power Subsystem, Visor Hardware Subsystem, Communication Subsystem (See above for list of items)
    
    **BLE Connection Range Experiment**
    The following items were used for this experiment: All items from the Visor Power Subsystem, Visor Hardware Subsystem, Communication Subsystem, and HUD Subsystem (See above for list of items)


## Statement of Contributions


Andrew Goostree focused on the battery subsystem experiment by developing the experimental approach, carrying out the testing, analyzing the resulting data, and documenting his findings in this report. He also contributed to other experiments by assisting with testing and helping compile results.

Bryce Hughes supported multiple experiments by contributing to their setup and execution, assisting with interpretation of the collected data, and helping document the results and observations throughout the report.

Landen Johnson worked on the power subsystem experiments by helping generate and refine experimental ideas, performing the necessary testing, analyzing the outcomes, and writing up the results in this document.

Michael Madigan was involved in the BLE communication experiments by contributing to the planning of the tests, participating in their execution, reviewing the collected data, and assisting with documenting the findings.

Brandon Zerkovich contributed across all experiments by assisting in their setup and execution, supporting data collection and analysis efforts, and documenting the experimental results in this report. He also developed the Document and Tracking Components section.

Ryan Zerkovich contributed to the BLE communication experiments between devices by assisting in the design of the experiments, carrying out the testing procedures, analyzing the collected data for performance and reliability, and documenting the results and conclusions in this report.
