---
name: industrial-machine-standards
description: Industrial machine process design, operating modes, safety boundaries, interlocks, device-level alarms, diagnostics and recovery requirements.
---
# Industrial Machine Control Rules
Apply to all real-machine designs. This is engineering guidance, not a substitute for applicable standards, risk assessment, safety design or qualified commissioning.

## 1. Separate process control from safety
- E-stop, guard locking, safety doors, safety light curtains, safe speed/position and STO are safety-related functions when required by risk assessment. Use an appropriately designed and validated safety circuit/controller and rated components.
- A standard PLC/SCL boolean, HMI button or ordinary output is not a safety function. Standard PLC logic may monitor safety status but must not replace the safety system.
- Never claim a machine is safe based on code review alone. Safety functions require risk assessment, design, validation and documented testing by qualified personnel.
- Define behavior for power loss, air-pressure loss, communication loss, PLC restart, safety reset and safety-device restoration. No unexpected restart after reset or recovery.
- Consider stored energy, gravity loads, pneumatic/hydraulic movement, braking, clamping, hot surfaces and hazardous zones. "All outputs OFF" is not automatically a safe state.

## 2. Buttons and HMI commands
- Start: require deliberate edge/request and all permissives; define whether held or pulsed.
- Stop: define controlled stop behavior for the machine; distinguish process stop, pause, abort and safety-related stop.
- E-stop: physical safety device designed and wired through the safety system as required; not merely an HMI/standard PLC bit.
- Reset/Acknowledge: acknowledge alarm separately from clearing fault; reset only after cause clears and reset conditions are met. Reset must not start motion.
- Manual/Jog: require appropriate mode selection, clear command ownership, hold-to-run where risk/design requires, movement limits and safety permissives. Manual mode never bypasses safety.
- Prevent simultaneous conflicting commands. Provide clear HMI state, active alarms, permissives not met and reset guidance.

## 3. Position and movement protection
Check applicable limits and feedback: home/limit switches, end-position sensors, encoder/position limits, brake feedback, drive ready/fault, guard/safety status and motion timeout. Use independent hardware/safety limits where required by risk assessment; software limits alone may not provide safety protection.
For reversing motors, use PLC logic plus suitable electrical/mechanical interlocking as required. Confirm zero speed or stop feedback before direction reversal when the hardware/process requires it.

## 4. Standard sequence design
Use explicit states, e.g. Idle -> CheckPermissives -> Execute -> WaitForFeedback -> Complete; fault states are explicit. Each state defines entry conditions, command, completion feedback, timeout, next state and fault response.
- A command is not proof of motion; a feedback signal is not proof of product quality.
- Define initial/home conditions, product-present conditions, station occupancy, part identity and cycle completion.
- Prevent repeated triggering from a held sensor/button. Use edge detection or a validated handshake where appropriate.
- Detect impossible sensor combinations and stale data.
- Avoid multiple blocks writing the same physical output. Centralize output ownership.
- Avoid hidden automatic restart after fault, power return, comms recovery or mode change.
- Define what happens to an in-process product after stop/fault; do not assume the sequence can safely resume at the same step.

## 5. Device-level fault requirements
For every cylinder, motor, servo, gripper, turntable, vision device, scale and RFID reader, define:
- Command and expected feedback
- Ready/permissive conditions
- Start timeout and, where needed, motion/complete timeout
- Contradictory/missing/stale feedback detection
- Fault code, alarm text, severity and operator action
- Latching/acknowledgement/reset criteria
- Recovery state and whether manual inspection is required

Examples:
Cylinder: extend/retract timeout, both end sensors active, neither sensor active when a known end state is required, low air pressure, valve/feedback mismatch.
Conveyor/motor: drive not ready, no run feedback, overload/drive fault, jam/blocked sensor, stop timeout, encoder implausibility.
Servo: enable failure, homing failure, positioning timeout, following error, limit reached, drive fault.
Gripper/vacuum: open/closed feedback mismatch, part not gripped, vacuum low, release timeout.
Vision: trigger timeout, busy timeout, invalid/stale result, no result, NG.
Weighing: unstable value, out of tolerance, invalid quality, comms timeout.
RFID/barcode: no tag, invalid/duplicate ID, read/write failure, stale response, timeout.

Do not add irrelevant alarms to every device; select based on actual hardware and risk.

## 6. Alarm model
Separate `Active`, `Acknowledged`, `Latched/Stored` and `ResetPermitted` concepts where needed. Record code, source/device, timestamp if available, severity, message and operator action. Alarm acknowledgement does not clear the cause. Fault reset must not bypass permissives.
Avoid clearing every fault with one unconditional reset. Preserve diagnostics long enough for troubleshooting; define retentive behavior intentionally.

## 7. Modes and recovery
Specify Auto, Manual/Jog, Setup/Maintenance, Single-step, Single-cycle and Continuous only as needed. Define mode-transition conditions and command arbitration. On a mode change, determine whether commands are dropped, sequence is held or a controlled stop is required.
Define recovery for timeout, sensor inconsistency, blocked product, communication loss and interrupted cycle. Recovery must be explicit and testable.

## 8. Production data and quality
Where relevant, track part ID, recipe, station, cycle result, OK/NG reason, counters, timestamps, operator acknowledgement and traceability status. Validate data before use; prevent stale vision/weight/RFID results being assigned to the next part. Do not claim MES/database integration until the actual interface and end-to-end test exist.

## 9. Required review
Before code release, review normal flow, every timeout, every impossible sensor state, stop/reset behavior, mode changes, startup/restart, communication recovery, output ownership, and safety-system boundaries. Real equipment must be tested under a documented commissioning plan by qualified personnel.
