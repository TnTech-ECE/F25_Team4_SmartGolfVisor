# Application Subsystem Detailed Design

### Smart Golf Visor
---


## Function of the Subsystem

The Application Subsystem functions as the central control and data-management component within the Smart Golf Visor system. Its primary role is to coordinate communication between the launch monitor, the visor-mounted microcontroller, and the cloud-database, ensuring that golfers receive real-time performance feedback and long-term analytical insights. Acting as the user interface, the subsystem enables golfers to initiate sessions, view live shot metrics, review historical performance, and receive automated coaching feedback derived from stored data.

Operationally, the application interprets and processes incoming shot data from the launch monitor, extracts the key performance metrics—ball speed, carry distance, launch angle, spin rate, and smash factor—and forwards simplified numerical metrics to the visor display via Bluetooth Low Energy (BLE) [1], [2], [3]. Simultaneously, the subsystem stores the complete processed dataset in a secure cloud database, enabling later retrieval for trend analysis and performance review. Through this coordinated action, the subsystem ensures that both live and historical information remain accurate, organized, and accessible.

In the broader context of the full Smart Golf Visor solution, the Application Subsystem serves as the bridge that integrates the hardware and user experience layers. It manages device connections, enforces data integrity, interprets and presents information in a user-friendly manner, and enables real-time and cloud-assisted decision-making. Without this subsystem, the launch monitor, visor hardware, and cloud components would remain isolated, and golfers would be unable to interact meaningfully with the system or access actionable performance insights.

## Specifications and Constraints

The **Application Subsystem** must operate within several technical, practical, and ethical constraints that govern its design and implementation. These constraints arise from user requirements, hardware capabilities, communication standards, browser limitations, and socio-economic factors. Together, they define the boundaries within which the subsystem must function to ensure safety, reliability, usability, and interoperability with the rest of the Smart Golf Visor system.

<p align="center">
  <strong>Table I</strong><br>
  Unified Constraints, Standards/Guidance, and Design Impact Table
</p>

| **Constraint** | **Standard/Guidance** | **Design Implication** | **Design Rationale** |
|---------------|--------------|--------------------------|----------------------|
| **Browser-Based Bluetooth Limitations** | Web Bluetooth API [4], [5], [6]; iOS BLE Restrictions [14] | The application relies on the Web Bluetooth API, which is supported primarily in Chromium-based browsers [4], [5], [6]. iOS Safari imposes partial BLE restrictions, limiting certain characteristics and connection stability [14]. | The subsystem must therefore use standardized GATT (Generic Attribute Profile) services and handle inconsistent browser support by detecting unavailable features, providing clear fallback messages, disabling unsupported actions, or guiding users toward supported browsers when necessary. |
| **Real-Time Performance Requirements** | — | The system must process and transmit shot metrics from the launch monitor to the visor with minimal latency to preserve real-time feedback. | This imposes constraints on data parsing efficiency, BLE communication speed, and front-end rendering. |
| **Cloud Connectivity Requirements** | HTTPS [8]; TLS [7] | All shot data must be uploaded to a secure cloud database through HTTPS [8]. | This requires reliable internet access and the use of Transport Layer Security (TLS) encrypted communication channels, constraining the system’s offline capabilities [7]. |
| **Mobile-Friendly Interface Constraints** | — | Golfers will primarily use smartphones on the course; the subsystem must maintain responsive layout behavior, limited CPU usage, and mobile-compatible user interfaces. | Ensures practical usability and acceptable performance on mobile devices during real-world use. |
| **Bluetooth Low Energy (BLE) Standards Compliance** | Bluetooth SIG GATT Specifications [1], [2], [3] | The subsystem must follow Bluetooth SIG GATT specifications for device discovery, service querying, characteristic read/write operations, and secure pairing [1], [2], [3]. | Ensures interoperability, standards compliance, and reliable BLE communication with external hardware. |
| **Data Privacy and Ethical Handling** | — | User performance data constitutes personally identifiable behavioral information. | The system must ethically store and transmit this data using secure channels and must not expose it to unauthorized parties. Compliance with general privacy principles—including data minimization, secure storage, and transparent data handling—is required. |
| **HTTPS and TLS Security Requirements** | TLS [7]; HTTPS [8] | All cloud communication must use HTTPS with TLS encryption, ensuring data confidentiality and integrity [7], [8]. | This constraint influences server configuration, API integration, and hosting requirements. |
| **Accessibility and Fair Use Constraints** | — | Because the system is intended for general users with varying levels of technological familiarity, the application must minimize user friction and avoid requiring specialized hardware or paid platforms whenever possible. | Promotes equitable access and minimizes technical barriers to adoption. |
| **Compatibility with Launch Monitor Output Format** | — | The application must parse and validate incoming data packets using the predefined shot metric format (ball speed, carry distance, launch angle, spin rate, smash factor). | Any deviation requires standardized error handling. |
| **Compatibility with Visor Microcontroller (ESP-32)** | BLE GATT & ATT Specifications [1], [3]; ESP32 BLE Documentation [13] | The subsystem must transmit simplified real-time metrics to the visor over BLE using the specific GATT characteristics defined by the visor subsystem. BLE packet size and update rate limitations directly constrain this behavior, meaning the subsystem must compress or down-sample metric values to fit within the 20-byte GATT characteristic limit and schedule transmissions to avoid exceeding permissible connection intervals [1], [3]. | The subsystem must also implement queueing or rate-limiting logic to prevent dropped packets and ensure that updates arrive consistently on the visor’s microcontroller [13]. |
| **Session-Based Data Structure Constraints** | — | All stored data must follow a consistent schema (Tables III–V) to ensure interoperability with the cloud database, historical analytics modules, and coaching insight generation algorithms. | Enforces long-term data consistency and compatibility across analytics systems. |
| **Low-Cost Implementation Requirement** | — | The project’s budget and intended use case require a low-cost solution. | A web-based application avoids app store fees, eliminates the need for platform-specific development, and reduces long-term maintenance costs. |
| **Open-Access Availability** | — | The system must be usable by anyone with a modern smartphone or computer without paid software installations. | This constraint supports equitable access for golfers regardless of income level. |

---

### **Formal Specifications**

<p align="center">
  <strong>Table II</strong><br>
  The following measurable specifications define the subsystem’s required performance
</p>

<center>

| **Specification** | **Target Value** |
|--------------------|------------------|
| BLE connection setup time | ≤ 10 seconds |
| End-to-end shot metric latency (monitor → app → visor) | ≤ 2 seconds |
| Cloud upload time per metric set | ≤ 2 seconds |
| Supported devices | Chrome/Edge on platforms that expose native Web Bluetooth (excluding iOS) [4], [5], [6], [14]
| Session storage throughput | 1–3 shots per minute typical |
| UI responsiveness | ≤ 100 ms user interaction delay 

</center>
    
Together, these specifications and constraints ensure that the Application Subsystem remains secure, responsive, portable, and compatible with all other components of the Smart Golf Visor system while meeting the practical needs of golfers in real-world environments.



## Overview of Proposed Solution

The Application Subsystem for the Smart Golf Visor system is implemented as a web-based Streamlit Web Application that balances portability, real-time performance, and cost-effectiveness [9], [10]. This approach allows golfers to access the application on any modern smartphone or desktop browser without requiring installation, while simultaneously providing a responsive, mobile-optimized interface suitable for on-course use.

The proposed solution fulfills all system specifications by performing four primary functions:

1. **Data Acquisition and Communication**
The application establishes a Bluetooth Low Energy (BLE) connection with the launch monitor to receive real-time shot metrics and simultaneously transmits simplified numerical metrics to the visor’s microcontroller using predefined GATT characteristics [1], [2], [3]. Adaptive handling of browser and device limitations ensures stable operation across supported platforms [4], [5], [6].

2. **Data Processing and Storage**
Incoming data is parsed, validated, and processed in real-time. Key metrics, including ball speed, carry distance, launch angle, spin rate, and smash factor, are stored in a secure cloud database according to the standardized Sessions, Shots, and Insights schema. This ensures interoperability with historical analytics modules and supports future expansions or additional metrics.

3. **User Interface and Visualization**
The application provides an intuitive, mobile-friendly dashboard that allows users to start and end sessions, view live shot metrics, review historical sessions, and access performance insights. The interface is designed to be responsive, lightweight, and visually clear, minimizing battery usage and maintaining high usability on smartphones.

4. **Coaching Insights Generation**
Leveraging the stored shot data, the application generates automated coaching insights to suggest performance adjustments. Insights are delivered both within the dashboard for post-session review and in real-time via the visor display, providing actionable feedback to improve performance during practice or rounds.

The solution architecture leverages modern web technologies (Streamlit Cloud) for a scalable, maintainable platform. The system ensures secure data transactions through HTTPS and TLS [7], [8], enforces BLE standards for device communication [1], [2], [3], and provides a consistent experience across all supported devices. By integrating real-time data acquisition, cloud storage, and coaching insights, this proposed solution meets all functional requirements while minimizing cost, deployment complexity, and platform dependency.

The web-based approach ensures broad accessibility, low development overhead, and future-proof scalability, making it the most practical solution for delivering a fully functional, reliable, and user-friendly Smart Golf Visor application.



## Interface with Other Subsystems

The **Application Subsystem** interfaces directly with three official subsystems within the Smart Golf Visor architecture: the **Launch Monitor Subsystem**, the **Visor Subsystem**, and the **Communications Subsystem**. These interfaces define the precise data pathways, BLE communication structures, and operational handshakes required to support real-time performance tracking and display.

### **1. Interface with the Launch Monitor Subsystem**
**Type:** Wireless Interface (via Communications Subsystem – BLE)  
**Direction:** Input  
**Protocol / Standard:** BLE GATT Service and Characteristics defined by the launch monitor [1], [2], [3]  

The application connects to the launch monitor through the Communications Subsystem using Bluetooth Low Energy [1]. Once connected, the app subscribes to the launch monitor’s shot-data characteristic, which transmits raw numerical metrics—including **ball speed**, **carry distance**, **launch angle**, **spin rate**, and **smash factor**. These values are typically encoded in manufacturer-specific structures, requiring the application to perform data parsing, byte-order handling, unit normalization, and timestamping.

The interface must also manage BLE-specific behaviors such as:  
- Automatic resubscription on reconnection  
- Detection of lost packets or partial metric sets  
- Handling variable update intervals depending on launch monitor model  
- Implementing a startup handshake that confirms the monitor is ready to stream  

Processed and validated data are then forwarded to the cloud database and visor interface.

### **2. Interface with the Visor Subsystem**
**Type:** Wireless Interface (via Communications Subsystem – BLE)  
**Direction:** Output  
**Protocol / Standard:** BLE GATT Service and Characteristics defined by the visor firmware  

The application sends **simplified real-time shot metrics** to the visor microcontroller via BLE. The visor subsystem defines specific GATT characteristics—for example, a “Current Shot Metrics” characteristic containing compact integer fields for ball speed, carry distance, and other HUD-displayable values. Because BLE imposes strict limitations on packet size and interval timing [1], [2], [3], the application must package all transmitted values into the smallest possible binary structures while still meeting the visor’s parsing expectations.

The interface must:  
- Maintain an active BLE connection with stable notification intervals (e.g., 10–50 ms depending on device and browser [11], [12])  
- Format metric packets exactly according to the visor’s expected byte layout  
- Respect the visor’s required update rate to avoid buffer overrun on the microcontroller  
- Handle the visor’s readiness or acknowledgment characteristic to confirm that the HUD is prepared to receive updates  
- Support retransmission of the most recent metric set after visor reconnection when acknowledgment or readiness signaling is available  

This interface ensures that each new shot is immediately available on the heads-up display with minimal latency.

### **3. Interface with the Communications Subsystem**
**Type:** Wireless Transport Layer (BLE)  
**Direction:** Bidirectional  
**Protocol / Standard:** BLE GATT (browser-supported Web Bluetooth)  

All BLE interactions between the application and external hardware (launch monitor and visor) are mediated through the Communications Subsystem. The application relies on browser-provided Web Bluetooth capabilities to initiate scans, connect to devices, discover services, and subscribe to or write characteristics [1], [2], [3]. Because browser BLE support varies—particularly on Safari for iOS—the interface must implement fallback handling and state management strategies [14].

The application must:  
- Detect supported BLE features at runtime and disable unsupported operations  
- Provide user feedback when browser limitations prevent device pairing  
- Retry failed connections using exponential backoff to remain responsive under unstable BLE conditions  
- Gracefully handle cases where required GATT characteristics are unavailable, renamed, or restricted by the browser  
- Use standardized UUIDs where possible to maximize cross-browser compatibility  

The Communications Subsystem acts as the transport layer that enables the application to bridge real-time data from the launch monitor to the visor while maintaining stable, secure BLE communication under varying device and browser conditions.

## Buildable Schematic

Because the Application Subsystem is implemented entirely in software as a browser-based Streamlit web application, the “buildable schematic” for this subsystem is represented as structured pseudocode rather than an electrical circuit diagram. This section presents the GitHub file structure, as well as pseudocode for each Python file in the repository, illustrating the logical organization, data flow, and responsibilities of each module.

### **GitHub Repository Layout and File Structure Explanation**

- **smart-golf-visor-app/**
  - `streamlit_app.py`
  - `README.md`
  - **pages/**
    - `session_history.py`
    - `insights.py`
    - `device_connection.py`
  - **modules/**
    - `ble_launch_monitor.py`
    - `ble_visor.py`
    - `data_models.py`
    - `cloud_storage.py`
    - `insights_engine.py`
  - **assets/**
    - `ui_styles.css`
    - **icons/**
    - **diagrams/**

**Explanation of Structure**

- **streamlit_app.py**  
  The main entry point for the Streamlit cloud application. Handles routing, UI initialization, and high-level event flow.

- **/pages/**  
  Contains Streamlit multipage views including session history, performance insights, and BLE device management.

- **/modules/**  
  Core logic modules:
  - BLE communication with launch monitor and visor  
  - Data schema models and validation  
  - Cloud upload and syncing  
  - Insight generation algorithms  

- **/assets/**  
  Static files such as CSS, images, and architecture diagrams.

This structure isolates BLE logic, data handling, and UI code, ensuring maintainability, clarity, and reproducible behavior across Streamlit Cloud deployments.


---
### **Pseudocode Modules**

### `streamlit_app.py` — Main Application Entry Point

This pseudocode represents the main control logic of the Streamlit-based Smart Golf Visor application. It initializes all shared system objects, including the cloud database interface, Bluetooth connections to the launch monitor and visor, and the coaching insights engine, using persistent session state to maintain continuity across user interactions. A sidebar navigation system routes the user between live session monitoring, historical data review, performance insights, and device connection management. During an active session, the application handles real-time Bluetooth data acquisition from the launch monitor, converts raw packets into structured shot data, stores the results in the cloud database, generates coaching insights, and transmits compacted metrics to the visor for heads-up display. The application also manages session lifecycle control, including session initialization, live visualization, and secure session termination with cloud storage.

```t
IMPORT streamlit as st

FROM modules.data_models IMPORT Session, Shot, Insight
FROM modules.cloud_storage IMPORT CloudDatabase
FROM modules.ble_launch_monitor IMPORT LaunchMonitorBLE
FROM modules.ble_visor IMPORT VisorBLE
FROM modules.insights_engine IMPORT InsightsEngine

# --------------------------------------------------
# Configure page and initialize shared objects
# --------------------------------------------------
SET page title and layout via st.set_page_config()

IF "db" NOT in st.session_state:
    st.session_state["db"] = CloudDatabase()

IF "monitor_ble" NOT in st.session_state:
    st.session_state["monitor_ble"] = LaunchMonitorBLE()

IF "visor_ble" NOT in st.session_state:
    st.session_state["visor_ble"] = VisorBLE()

IF "insights_engine" NOT in st.session_state:
    st.session_state["insights_engine"] = InsightsEngine()

IF "current_session" NOT in st.session_state:
    st.session_state["current_session"] = None

# --------------------------------------------------
# Sidebar navigation
# --------------------------------------------------
DISPLAY sidebar with options:
    - "Dashboard / Live Session"
    - "Session History"
    - "Insights"
    - "Device Connections"

SET current_page = selection from sidebar

# --------------------------------------------------
# Route to the appropriate page logic
# --------------------------------------------------
IF current_page == "Dashboard / Live Session":
    CALL render_live_session_page(
        monitor_ble = st.session_state["monitor_ble"],
        visor_ble   = st.session_state["visor_ble"],
        db          = st.session_state["db"],
        insights    = st.session_state["insights_engine"],
        current_session = st.session_state["current_session"]
    )

ELIF current_page == "Session History":
    IMPORT pages.session_history AS history_page
    CALL history_page.render(db = st.session_state["db"])

ELIF current_page == "Insights":
    IMPORT pages.insights AS insights_page
    CALL insights_page.render(db = st.session_state["db"],
                              insights_engine = st.session_state["insights_engine"])

ELIF current_page == "Device Connections":
    IMPORT pages.device_connection AS device_page
    CALL device_page.render(monitor_ble = st.session_state["monitor_ble"],
                            visor_ble   = st.session_state["visor_ble"])

# --------------------------------------------------
# Live session rendering function
# --------------------------------------------------
FUNCTION render_live_session_page(monitor_ble, visor_ble, db, insights, current_session):
    DISPLAY heading "Live Session"

    IF current_session is None:
        BUTTON "Start New Session":
            CREATE new Session object with start_time, device metadata
            st.session_state["current_session"] = new Session
    ELSE:
        DISPLAY session metadata (start time, number of shots)

    # Device connection status
    DISPLAY monitor_ble connection state
    DISPLAY visor_ble connection state

    # Connect buttons
    BUTTON "Connect Launch Monitor":
        CALL monitor_ble.connect()
    BUTTON "Connect Visor":
        CALL visor_ble.connect()

    # Capture shot data when a new packet arrives
    IF monitor_ble.has_new_shot():
        raw_data = monitor_ble.read_shot()
        shot = Shot.from_raw_packet(raw_data)
        APPEND shot to current_session.shots

        # Store in cloud
        CALL db.store_shot(current_session.session_id, shot)

        # Generate insight
        new_insight = insights.generate_for_shot(shot, current_session)
        IF new_insight is not None:
            CALL db.store_insight(new_insight)

        # Send simplified metrics to visor
        compact_payload = visor_ble.encode_metrics(shot)
        CALL visor_ble.send(compact_payload)

    # Display latest shot metrics in UI
    IF current_session has shots:
        DISPLAY last shot metrics and basic graphs

    BUTTON "End Session":
        SET current_session.end_time = current timestamp
        CALL db.store_session(current_session)
        st.session_state["current_session"] = None
END FUNCTION
```

### `pages/session_history.py` — Session History Page

This pseudocode defines the logic for the **Session History** page of the Streamlit application. It retrieves all previously recorded practice sessions for the current user from the cloud database and presents them through a selectable interface. Upon selecting a session, the application loads the associated shot and insight data, computes key summary statistics such as total shots and average performance metrics, and visualizes performance trends using time-series plots. The module also displays all coaching insights generated for the selected session, enabling users to review historical performance, analyze trends, and evaluate past practice effectiveness within a single consolidated interface.

```t
IMPORT streamlit as st
FROM modules.cloud_storage IMPORT CloudDatabase
FROM modules.data_models IMPORT Session, Shot, Insight

FUNCTION render(db: CloudDatabase):
    DISPLAY heading "Session History"

    # Retrieve all sessions for current user
    sessions = db.get_sessions_for_user(current_user_id)

    IF sessions is empty:
        DISPLAY "No sessions found."
        RETURN

    # List sessions in a table or selectbox
    selected_session = st.selectbox(
        label = "Select a session",
        options = sessions,
        format_function = lambda s: s.start_time + " - " + s.device_type
    )

    IF selected_session is not None:
        # Load associated shots and insights
        shots = db.get_shots_for_session(selected_session.session_id)
        insights = db.get_insights_for_session(selected_session.session_id)

        DISPLAY summary statistics:
            - total shots
            - average ball speed
            - average carry distance
            - launch angle / spin distributions

        # Plot metrics over time
        PLOT line chart: shot index vs. ball_speed
        PLOT line chart: shot index vs. carry_distance

        # Show insights
        FOR each insight in insights:
            DISPLAY insight.insight_type and insight.insight_value
END FUNCTION
```

### `pages/insights.py` — Insights Overview Page

This pseudocode implements the **Performance Insights** page of the application, which provides users with aggregated coaching feedback across selected timeframes. The user selects a desired analysis window (e.g., last session, week, month, or all sessions), after which the system retrieves all relevant session and shot data from the cloud database. The Insights Engine processes this data to compute higher-level performance trends, consistency measures, and optimization recommendations. The resulting insights are presented as summary metrics and individual insight cards, allowing users to evaluate long-term performance patterns, identify strengths and weaknesses, and receive targeted practice guidance based on historical data.

```t
IMPORT streamlit as st
FROM modules.cloud_storage IMPORT CloudDatabase
FROM modules.insights_engine IMPORT InsightsEngine

FUNCTION render(db: CloudDatabase, insights_engine: InsightsEngine):
    DISPLAY heading "Performance Insights"

    # User selects a timeframe or filter
    timeframe = st.selectbox("Timeframe", ["Last Session", "Last Week", "Last Month", "All Sessions"])

    # Fetch relevant sessions and shots
    sessions, shots = db.get_sessions_and_shots_for_timeframe(current_user_id, timeframe)

    IF shots is empty:
        DISPLAY "No data available for the selected timeframe."
        RETURN

    # Compute aggregated insights via engine
    summary_insights = insights_engine.generate_aggregated_insights(sessions, shots)

    # Display high-level metrics and trends
    DISPLAY key metrics:
        - average ball speed
        - consistency indices
        - optimized carry recommendations

    # Display insight cards
    FOR each high-level insight IN summary_insights:
        DISPLAY card with:
            - insight title
            - explanation text
            - suggested practice focus
END FUNCTION
```

### `pages/device_connection.py` — Device Connection & Diagnostics Page

This pseudocode defines the **Device Connections & Diagnostics** page, which allows users to scan for, connect to, and disconnect from both the launch monitor and the visor via Bluetooth Low Energy. It provides real-time connection status, basic diagnostic test functions for reading and writing BLE data, and displays browser BLE capability information to help verify platform compatibility and troubleshoot communication issues.

```t
IMPORT streamlit as st
FROM modules.ble_launch_monitor IMPORT LaunchMonitorBLE
FROM modules.ble_visor IMPORT VisorBLE

FUNCTION render(monitor_ble: LaunchMonitorBLE, visor_ble: VisorBLE):
    DISPLAY heading "Device Connections & Diagnostics"

    # Launch monitor section
    DISPLAY subheading "Launch Monitor"
    DISPLAY current connection state (connected / disconnected)
    BUTTON "Scan and Connect to Launch Monitor":
        CALL monitor_ble.scan_and_connect()
    BUTTON "Disconnect Launch Monitor":
        CALL monitor_ble.disconnect()

    # Test read
    IF monitor_ble.is_connected():
        BUTTON "Test Read Shot Data":
            test_packet = monitor_ble.read_test_packet()
            DISPLAY parsed test_packet fields

    # Visor section
    DISPLAY subheading "Visor"
    DISPLAY current connection state (connected / disconnected)
    BUTTON "Scan and Connect to Visor":
        CALL visor_ble.scan_and_connect()
    BUTTON "Disconnect Visor":
        CALL visor_ble.disconnect()

    # Test write
    IF visor_ble.is_connected():
        BUTTON "Send Test Metrics to Visor":
            mock_shot = construct synthetic Shot with placeholder values
            compact_payload = visor_ble.encode_metrics(mock_shot)
            CALL visor_ble.send(compact_payload)
            DISPLAY "Test packet sent."

    # Browser capability diagnostics
    DISPLAY "Browser BLE Support:"
    DISPLAY monitor_ble.get_browser_capability_report()
END FUNCTION
```

### `modules/ble_launch_monitor.py` — Launch Monitor BLE Interface

This pseudocode defines the **LaunchMonitorBLE** class, which encapsulates all Bluetooth Low Energy communication with the launch monitor. It handles device discovery, secure connection establishment, GATT service and characteristic discovery, and real-time shot data acquisition. The class also provides connection status checks, diagnostic test packet support, and browser BLE capability reporting to ensure reliable operation across supported platforms.

```t
IMPORT typing utilities

CLASS LaunchMonitorBLE:
    ATTRIBUTE device_handle
    ATTRIBUTE shot_characteristic
    ATTRIBUTE connected_flag

    FUNCTION __init__():
        SET device_handle = None
        SET shot_characteristic = None
        SET connected_flag = False

    FUNCTION scan_and_connect():
        # Use Web Bluetooth via Streamlit JS bridge
        REQUEST user to select BLE device matching launch monitor filters
        IF user selects a device:
            ASSIGN device_handle
            DISCOVER GATT services and characteristics
            FIND shot data characteristic (UUID known from spec)
            SET shot_characteristic
            SET connected_flag = True

    FUNCTION disconnect():
        IF device_handle is not None:
            CLOSE BLE connection
        SET connected_flag = False

    FUNCTION is_connected() -> bool:
        RETURN connected_flag

    FUNCTION has_new_shot() -> bool:
        # Implementation-specific buffering or notification flag
        RETURN True if new notification received on shot_characteristic

    FUNCTION read_shot() -> raw_packet:
        READ latest value from shot_characteristic
        RETURN raw_packet (byte array or structured data)

    FUNCTION read_test_packet() -> raw_packet:
        # Optional: read or generate a sample packet for diagnostics
        RETURN example raw packet

    FUNCTION get_browser_capability_report() -> dict:
        # Return information on what BLE features are available in this browser
        RETURN dictionary with flags for:
            - supports_web_bluetooth
            - supports_required_services
            - supports_notifications
```

### `modules/ble_visor.py` — Visor BLE Interface

This pseudocode defines the **VisorBLE** class, which manages all Bluetooth Low Energy communication with the visor microcontroller. It handles device discovery, secure connection establishment, GATT characteristic discovery for real-time metric transmission, and connection state management. The class also encodes processed shot metrics into compact binary packets and transmits them to the visor using efficient BLE write operations optimized for low latency and reliable HUD updates.

```t
IMPORT typing utilities
FROM modules.data_models IMPORT Shot

CLASS VisorBLE:
    ATTRIBUTE device_handle
    ATTRIBUTE metrics_characteristic
    ATTRIBUTE connected_flag

    FUNCTION __init__():
        SET device_handle = None
        SET metrics_characteristic = None
        SET connected_flag = False

    FUNCTION scan_and_connect():
        REQUEST user to select BLE device matching visor filters
        IF device selected:
            ASSIGN device_handle
            DISCOVER GATT services and characteristics
            FIND "Current Shot Metrics" characteristic UUID
            SET metrics_characteristic
            SET connected_flag = True

    FUNCTION disconnect():
        IF device_handle is not None:
            CLOSE BLE connection
        SET connected_flag = False

    FUNCTION is_connected() -> bool:
        RETURN connected_flag

    FUNCTION encode_metrics(shot: Shot) -> bytes:
        # Pack key metrics into a compact binary format
        # Example fields:
        #  - ball_speed (int16)
        #  - carry_distance (int16)
        #  - launch_angle (int16 scaled)
        #  - spin_rate (int16)
        CREATE bytearray payload
        ENCODE shot.ball_speed into payload
        ENCODE shot.carry_distance into payload
        ENCODE shot.launch_angle into payload
        ENCODE shot.spin_rate into payload
        RETURN payload

    FUNCTION send(payload: bytes):
        IF metrics_characteristic is not None AND connected_flag is True:
            WRITE payload to metrics_characteristic with "write without response"
```

### `modules/data_models.py` — Data Schema Models

This pseudocode defines the **core data models** used throughout the application to enforce a consistent and structured schema for all stored and processed information. The Session class represents a single practice session and aggregates all recorded shots, while the Shot class encapsulates individual swing metrics decoded directly from raw BLE packets. The **from_raw_packet** method standardizes packet parsing and derived metric computation, ensuring data integrity at ingestion. The **Insight** class stores automated coaching outputs linked to sessions and individual shots. Together, these models form the backbone of the system’s data integrity, cloud interoperability, and analytics pipeline.

```t
FROM dataclasses IMPORT dataclass
FROM datetime IMPORT datetime
FROM typing IMPORT Optional, List

@dataclass
CLASS Session:
    session_id: UUID
    user_id: UUID
    start_time: datetime
    end_time: Optional[datetime]
    device_type: str
    notes: Optional[str]
    shots: List["Shot"]

@dataclass
CLASS Shot:
    shot_id: UUID
    session_id: UUID
    timestamp: datetime
    ball_speed: float
    launch_angle: float
    carry_distance: float
    spin_rate: float
    smash_factor: float
    raw_packet: Optional[bytes]

    @classmethod
    FUNCTION from_raw_packet(cls, raw_packet) -> "Shot":
        # Parse BLE packet into fields
        DECODE ball_speed from raw_packet
        DECODE launch_angle from raw_packet
        DECODE carry_distance from raw_packet
        DECODE spin_rate from raw_packet
        COMPUTE smash_factor from ball_speed and other metrics
        RETURN new Shot instance with decoded values

@dataclass
CLASS Insight:
    insight_id: UUID
    session_id: UUID
    shot_id: Optional[UUID]
    insight_type: str
    insight_value: str
    created_at: datetime

```

### `modules/cloud_storage.py` — Cloud Database Interface

This pseudocode defines the **cloud database interface** layer responsible for all persistent data storage and retrieval within the application. The **CloudDatabase** class manages secure authentication, serializes application objects into JSON, and communicates with the remote database using HTTPS-based API endpoints. It supports creation and retrieval of sessions, shots, and insights, enabling both real-time data logging and historical analysis. By abstracting all networking and database operations into a single module, this design enforces separation of concerns, simplifies error handling, and ensures consistent interaction with the cloud backend across the application.

```t
IMPORT requests or cloud SDK
FROM modules.data_models IMPORT Session, Shot, Insight

CLASS CloudDatabase:
    ATTRIBUTE base_url
    ATTRIBUTE auth_token

    FUNCTION __init__():
        LOAD base_url and auth_token from configuration or Streamlit secrets

    FUNCTION store_session(session: Session):
        SERIALIZE session to JSON
        SEND POST request to base_url + "/sessions"
        HANDLE response and errors

    FUNCTION store_shot(session_id: UUID, shot: Shot):
        SERIALIZE shot to JSON
        SEND POST request to base_url + "/sessions/{session_id}/shots"
        HANDLE response and errors

    FUNCTION store_insight(insight: Insight):
        SERIALIZE insight to JSON
        SEND POST request to base_url + "/insights"
        HANDLE response and errors

    FUNCTION get_sessions_for_user(user_id: UUID) -> List[Session]:
        SEND GET request to base_url + "/users/{user_id}/sessions"
        PARSE JSON into Session objects
        RETURN list

    FUNCTION get_shots_for_session(session_id: UUID) -> List[Shot]:
        SEND GET request to base_url + "/sessions/{session_id}/shots"
        PARSE JSON into Shot objects
        RETURN list

    FUNCTION get_insights_for_session(session_id: UUID) -> List[Insight]:
        SEND GET request to base_url + "/sessions/{session_id}/insights"
        PARSE JSON into Insight objects
        RETURN list

    FUNCTION get_sessions_and_shots_for_timeframe(user_id, timeframe) -> (List[Session], List[Shot]):
        # Translate timeframe into date range
        DETERMINE start_date and end_date
        QUERY sessions and shots within date range
        RETURN (sessions, shots)
```

### `modules/insights_engine.py` — Coaching Insights Engine

This pseudocode implements the coaching insights engine that converts shot data into actionable feedback. The **generate_for_shot** function provides real-time, rule-based coaching for individual swings, while **generate_aggregated_insights** performs session-level statistical analysis to identify long-term performance trends. Together, these methods support both immediate visor feedback and post-session performance evaluation.

```t
FROM modules.data_models IMPORT Session, Shot, Insight
FROM datetime IMPORT datetime
FROM uuid IMPORT uuid4

CLASS InsightsEngine:

    FUNCTION generate_for_shot(shot: Shot, session: Session) -> Optional[Insight]:
        # Simple rule-based example:
        IF shot.smash_factor < threshold_low:
            message = "FOR EXAMPLE: Increase center-face contact to improve smash factor."
            category = "Contact Quality"
        ELIF shot.spin_rate too high:
            message = "FOR EXAMPLE: Spin is high; consider adjusting attack angle."
            category = "Spin Optimization"
        ELSE:
            RETURN None  # No per-shot insight needed

        RETURN new Insight(
            insight_id = uuid4(),
            session_id = session.session_id,
            shot_id = shot.shot_id,
            insight_type = category,
            insight_value = message,
            created_at = current datetime
        )

    FUNCTION generate_aggregated_insights(sessions: List[Session], shots: List[Shot]) -> List[Insight]:
        INIT empty list aggregated_insights

        # Compute aggregate metrics
        CALCULATE average ball_speed, dispersion, spin ranges, etc.

        IF average carry_distance below expected:
            ADD insight recommending focus on clubhead speed and launch angle

        IF variability in launch_angle is high:
            ADD insight suggesting work on consistency in setup and ball position

        # Other patterns and recommendations can be added here

        RETURN aggregated_insights
```




## Flowchart

<p align="center">
  <img src="https://hackmd.io/_uploads/ByX0DYnWZl.png" alt="ApplicationFlowchart_DetailedDesign" width="700">
  <br>
</p>
<p align="center">
Figure 1: Flowchart showing back-end decision making, within the application.
</p>

<p align="center">
  <img src="https://hackmd.io/_uploads/HJom3Fhb-x.png" alt="ApplicationInteractionsFlowchart" width="700">
  <br>
</p>
<p align="center">
    Figure 2: Flowchart showing application interactions with the end-user, other subsystems, and the cloud database.
</p>

## BOM

The Application subsystem of this project is pure software and will not require any physical materials or components other than the end-user's smartphone.

## Analysis

The **Application Subsystem** for the Smart Golf Visor was evaluated in depth to verify that the proposed web-based architecture satisfies all functional requirements, meets subsystem constraints, and integrates correctly with the launch monitor, visor, and communications subsystems. This analysis examines performance, reliability, security, data integrity, and overall system feasibility.

### **1. Functional Performance Analysis**

The primary function of the application subsystem is to collect shot data from the launch monitor, process and store those metrics, generate coaching insights, and transmit simplified values to the visor in real time. The proposed **Web Application** architecture meets these requirements through the following mechanisms:

- **Real-time Data Ingestion:**  
  BLE notifications from the launch monitor provide continuous shot data. Analytical timing models and published Web Bluetooth specifications indicate that notification intervals on the order of 10–30 ms are achievable on Chromium-based browsers [4], [5], [6], which is more than sufficient for golf-shot events that occur several seconds apart.

- **Data Parsing and Validation:**  
  The application uses a fixed schema for shot data—including type enforcement, range checking, and timestamp assignment—ensuring that noisy or malformed packets do not propagate into analytics modules or visor output.

- **Real-Time Visor Updates:**  
  Simplified metrics (ball speed, carry distance, launch angle, spin rate, smash factor) are encoded into compact binary packets (typically ≤ 10 bytes) and transmitted via the visor’s custom GATT characteristic. Packet size analysis confirms compliance with BLE’s 20-byte payload limit [1], [3], and analytical BLE timing constraints indicate that update rates on the order of 10–20 Hz are feasible under stable conditions [11], [12].

- **Coaching Insight Generation:**  
  The application processes normalized metrics using rules-based or statistical heuristics. Because all data is timestamped and normalized upon reception, downstream modules can reliably produce insights immediately after each shot.

This confirms that the architecture is capable of meeting latency, processing, and data-transfer requirements.

---

### **2. Reliability and Robustness Analysis**

Golf practice environments involve variable wireless conditions, mobile device limitations, and potential browser inconsistencies. The application mitigates these factors through:

- **Browser Support Handling:**  
  The subsystem detects available BLE capabilities at runtime and disables unsupported features on iOS Safari [14]. Fallback logic prevents application crashes when required characteristics cannot be accessed [4], [5], [6].

- **Automatic Reconnection and State Recovery:**  
  The app stores current device handles, last-known metrics, and GATT characteristic mappings so that a reconnection attempt can be performed without user intervention if the visor or launch monitor temporarily disconnects [2], [3], [11], [12].

- **Packet Loss Mitigation:**  
  The system reuses the most recent parsed metric values whenever a downstream BLE transmission fails. This prevents data gaps on the visor display without corrupting historical data [11], [12].

- **Cloud Sync Reliability:**  
  All uploads to the cloud database occur over HTTPS. If a connection fails, the app temporarily buffers data in memory and retries the upload when connectivity returns [7], [8], [9], [10].

The system therefore maintains operational stability even under constrained wireless or browser conditions.

---

### **3. Security and Compliance Analysis**

The application must comply with security requirements for BLE, cloud communication, and user data privacy:

- **BLE Security:**  
  The Web Bluetooth API enforces encrypted connections at the browser level and requires explicit user consent for every device pairing action [4], [15]. Unauthorized device access is prevented because the browser exposes only user-approved GATT handles.

- **Cloud Security:**  
  All cloud transactions use **TLS-encrypted HTTPS sessions**, ensuring safe transfer of user metrics, session history, and metadata [7], [8], [16].

- **Data Privacy:**  
  Only essential numerical shot metrics are collected. No direct personal identifiers are transmitted; however, all behavioral performance data linked to a user identifier is treated as sensitive user data and protected accordingly.

- **Standard Compliance:**  
  The system respects BLE GATT standard structures and adheres to browser security requirements for device permissions [15], [16].

These security measures fully satisfy the constraints defined for the subsystem.

---

### **4. Data Integrity and Schema Compatibility Analysis**

All stored data follows a consistent, structured schema to ensure interoperability with the cloud database, historical analytics, and coaching insight generation modules. Enforcing this schema guarantees that shot data can be reliably processed, compared, and retrieved across devices and sessions.

**Data Schema:**


<p align="center">
  <strong>Table III</strong><br>
  "Sessions" data schema:
</p>

<center>

| Field | Type | Description |
|-------|-------|-------------|
| session_id | UUID | Unique identifier for the session |
| user_id | UUID | Identifier linking the session to a specific user |
| start_time | Timestamp | When the session started |
| end_time | Timestamp | When the session ended |
| device_type | Text | Launch monitor model used |
| notes | Text | Optional metadata |
    
</center>

<p align="center">
  <strong>Table IV</strong><br>
  "Shots" data schema:
</p>

<center>
    
| Field | Type | Description |
|-------|-------|-------------|
| shot_id | UUID | Unique identifier for each shot |
| session_id | UUID (FK) | Links the shot to a session |
| timestamp | Timestamp | When the shot was recorded |
| ball_speed | Float | Speed of the golf ball (m/s or mph) |
| launch_angle | Float | Launch angle (degrees) |
| carry_distance | Float | Estimated carry distance |
| spin_rate | Float | Spin rate (rpm) |
| smash_factor | Float | Efficiency ratio |
| raw_packet | Binary/Text | Optional storage of original Bluetooth data |
    
</center>

<p align="center">
  <strong>Table V</strong><br>
  "Insights" data schema:
</p>

<center>

| Field | Type | Description |
|-------|-------|-------------|
| insight_id | UUID | Unique identifier for each insight |
| session_id | UUID (FK) | Links insight to a session |
| shot_id | UUID (FK) | Links insight to a specific shot (if applicable) |
| insight_type | Text | Description of insight category |
| insight_value | Text | Generated advice or metric summary |
| created_at | Timestamp | When the insight was generated |
    
</center>

Schema enforcement—using validation routines—prevents corrupted data from being logged or transmitted downstream.

---

### **5. BLE Performance Constraints and Feasibility**

Bluetooth Low Energy (BLE) communication is constrained by protocol-level and browser-imposed limits, including:

- **Maximum ATT payload:** 20 bytes per GATT write (default MTU = 23 bytes)  
- **Connection interval:** typically 7.5 ms ≤ T_conn ≤ 4 s  
- **Supervision timeout:** ≥ (1 + latency) × T_conn × 2  
- **Browser-managed BLE scheduling**

The visor transmission payload is analytically bounded to ≤ 10 bytes, consisting of integer representations of ball speed, carry distance, launch angle, and spin rate. Since 10 bytes < 20 bytes, each update fits within a single ATT packet, eliminating packet fragmentation and minimizing transmission latency [1], [3]:

$$
T_{tx} ≈ T_{conn} ≤ 50 ms
$$

Launch monitor data arrival is event-driven and occurs once per golf swing. With worst-case swing repetition rates on the order of 0.2–0.3 Hz (one shot every 3–5 s), the required average data rate remains:

$$
R_{data} ≪ 1 kB/s
$$

which is orders of magnitude below BLE throughput limits (≈ 100–300 kbit/s under ideal conditions [11], [12]).

Web Bluetooth implementations on Chromium-based browsers enforce protocol timing automatically [4], [5], [6], ensuring compliance with connection interval, supervision timeout, and notification pacing without developer-level access to the controller stack. Because the system relies on low-duty-cycle transmission of short packets rather than continuous streaming, timing jitter and browser scheduling overhead do not violate real-time visor display requirements.

These analytical bounds verify that the proposed BLE architecture satisfies packet size, latency, and update-rate constraints without exceeding BLE protocol or browser limitations.

---

### **6. Integration Analysis with Other Subsystems**

The application integrates cleanly with:

- **Launch Monitor Subsystem:**  
  It subscribes to its GATT characteristics, normalizes values, and handles manufacturer-specific encoding.

- **Visor Subsystem:**  
  It formats and writes metrics to the visor’s GATT characteristic with correct byte ordering, update timing, and acknowledgment checking.

- **Communications Subsystem:**  
  It uses Web Bluetooth as the transport layer, ensuring secure, permission-based wireless interaction.

No architectural conflicts exist, and all data pathways are compatible with subsystem definitions.

---

### **7. Overall Feasibility Conclusion**

Based on detailed evaluation, the proposed **web-based application** satisfies all:

- Functional requirements  
- Environmental and technical constraints  
- Performance and latency needs  
- Reliability expectations  
- Security standards  
- Integration and interoperability requirements  

The analysis strongly supports the selection of a **Streamlit web-app architecture** as the optimal solution for the Smart Golf Visor system [9], [10]. It provides the best balance of cost, portability, real-time performance, and ease of deployment while maintaining compatibility with existing subsystems.

---



## References

[1] Bluetooth SIG, Bluetooth Core Specification, Version 5.4, 2023. [Online]. Available: https://www.bluetooth.com/specifications/bluetooth-core-specification/

[2] Bluetooth SIG, Generic Attribute Profile (GATT) Specification, 2023. [Online]. Available: https://www.bluetooth.com/specifications/specs/generic-attribute-profile/

[3] Bluetooth SIG, Attribute Protocol (ATT) Specification, 2023. [Online]. Available: https://www.bluetooth.com/specifications/specs/attribute-protocol/

[4] W3C, Web Bluetooth API — Editor’s Draft, World Wide Web Consortium, 2024. [Online]. Available: https://webbluetoothcg.github.io/web-bluetooth/

[5] Google Developers, “Web Bluetooth API,” Chromium Platform Documentation, 2024. [Online]. Available: https://developer.chrome.com/docs/web-platform/bluetooth/

[6] Google Chrome Platform Status, “Web Bluetooth,” 2024. [Online]. Available: https://chromestatus.com/feature/5264933985976320

[7] IETF, The Transport Layer Security (TLS) Protocol Version 1.3, RFC 8446, Aug. 2018. [Online]. Available: https://datatracker.ietf.org/doc/html/rfc8446

[8] IETF, Hypertext Transfer Protocol Secure (HTTPS), RFC 2818, May 2000. [Online]. Available: https://datatracker.ietf.org/doc/html/rfc2818

[9] Streamlit Inc., “Streamlit Cloud Documentation,” 2024. [Online]. Available: https://docs.streamlit.io/streamlit-community-cloud

[10] Streamlit Inc., “Deployment Architecture and Secrets Management,” 2024. [Online]. Available: https://docs.streamlit.io/streamlit-community-cloud/deploy-your-app

[11] Texas Instruments, Bluetooth Low Energy Data Throughput Application Report, SWRA478B, 2021. [Online]. Available: https://www.ti.com/lit/an/swra478b/swra478b.pdf

[12] Nordic Semiconductor, Bluetooth Low Energy Throughput Optimization, White Paper, 2022. [Online]. Available: https://infocenter.nordicsemi.com/pdf/nwp_027.pdf

[13] Espressif Systems, “ESP32 Bluetooth Low Energy Overview,” 2023. [Online]. Available: https://docs.espressif.com/projects/esp-idf/en/latest/esp32/api-guides/ble/index.html

[14] Apple Developer Documentation, “Bluetooth Restrictions on iOS Web Browsers,” 2024. [Online]. Available: https://developer.apple.com/bluetooth/

[15] Bluetooth SIG, Bluetooth Core Specification, Version 5.4, 2023. [Online]. Available: https://www.bluetooth.com/specifications/bluetooth-core-specification/

[16] IETF, “The Transport Layer Security (TLS) Protocol Version 1.3,” RFC 8446, Aug. 2018. [Online]. Available: https://datatracker.ietf.org/doc/html/rfc8446
