## Meeting Summary (Transcript 5 — Business Only)

**Purpose:** Confirm ordering status and expectations, review optional feature ideas, assess sensor/PCB risks, align on IMU-trigger behavior, and plan next stakeholder check-ins.

---

### Key Points Discussed

#### 1) Ordering Status and Constraints
- Ordering is pending; team has not yet received confirmation that parts have been ordered/shipped.
- Purchasing coordinator indicated the order is unusually large for the current year, though comparable orders have occurred in prior years.
- Team expectation: once parts arrive, the coordinator will notify the team (email; relevant members likely CC’d).

---

#### 2) Assembly Planning (Once Parts Arrive)
- Plan is to begin assembling subsystems immediately after parts are received.
- Prioritize quick turnaround on:
- PCB population
- Initial bring-up tests
- Early 3D prints/enclosure iterations using available filament

---

#### 3) Optional Feature Discussion: Stroke/Swing Count
- Idea: implement a stroke/swing count feature.
- Risks/limitations:
- Accuracy may be poor if users do not use the device for short-game shots (putting/chipping).
- Head-motion-based counting introduces false positives and sensitivity tuning effort.
- Decision: keep as a **low-priority / optional software-only feature**, not requiring additional hardware at this time.

---

#### 4) Sensor/PCB Risk: Missing Coupling Capacitors
- Team identified that coupling capacitors were omitted on the PCB for a sensor path.
- Expected impact:
- Potential for noise/spikes and reduced signal quality.
- Likely still acceptable because the project does not require high-precision measurement.
- Mitigation:
- Validate functionality during bring-up; accept reduced accuracy if overall detection works.

---

#### 5) IMU Event Triggering and Data Flow
- Clarified that IMU-based detection should act as a **trigger for display/data handling**, not directly tied to Bluetooth transmission.
- Preferred approach:
- System remains idle or low-load.
- When event condition is met (e.g., swing detected), code triggers data acquisition and update cycle.
- Team has already demonstrated the ability to pass simulated data packets and render on an LCD.
- Next step: adapt BLE packet handling for visor-side needs.

---

#### 6) Team Status
- Project progress is primarily limited by parts arrival.
- Current work focuses on completing software integration and finalizing plans for rapid assembly/testing once shipments arrive.

---

#### 7) External Check-ins (Stakeholders)
- Discussed whether to meet with stakeholder (e.g., Matt).
- Conclusion:
- Defer meeting until the team has physical hardware to demonstrate.
- Short-term need may exist for schedule/expectation alignment, but technical review is more valuable after build progress.

---

### Action Items
- **Team Lead:** Follow up on order status if no confirmation is received soon.
- **Software:** Continue integrating BLE packet handling and event-driven IMU trigger logic.
- **Hardware:** Prepare bring-up checklist for first power-on (noise sensitivity test, IMU validation, display update test).
- **Optional Feature Owner:** Park stroke/swing count as a stretch goal and revisit after core integration works.

---

### Risks / Watch Items
- Delays in ordering or shipping.
- Signal integrity issues due to missing coupling capacitors.
- Optional features increasing scope/time without improving MVP success.

