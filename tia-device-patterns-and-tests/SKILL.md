---
name: tia-device-patterns-and-tests
description: Reusable industrial PLC design patterns, device interface checklists, sample structures and test cases for motors, cylinders, conveyors, servos, grippers, vision, weighing, RFID and alarms.
---
# Industrial Device Patterns & Test Library
Templates are design starting points, not certified drop-in machine programs. Confirm real device manuals, wiring, process and safety design before use.

## 1. Standard device interface
Use a consistent contract where appropriate:
- Commands: `Cmd_Enable`, `Cmd_Start`, `Cmd_Stop`, `Cmd_Reset`, `Cmd_Extend`, `Cmd_Retract`
- Feedback: `Fb_Ready`, `Fb_Running`, `Fb_Done`, `Fb_Extended`, `Fb_Retracted`
- Status: `Sts_Idle`, `Sts_Busy`, `Sts_Done`, `Sts_Fault`
- Parameters: `Par_Timeout`, `Par_Speed`, `Par_Target`
- Diagnostics: `Alm_Active`, `Alm_Ack`, `Alm_Code`
Names are examples; use the project's naming convention.

## 2. Device patterns
### Cylinder
Inputs: extend/retract request, extend/retract limit feedback, permissives, reset. Outputs: valve command(s), busy/done/fault/code. Enforce mutually exclusive commands; check initial state, contradictory feedback, extend/retract timeout and reset only after cause clears. Match output behavior to actual valve type; do not assume every valve is de-energize-to-safe.

### Conveyor/motor
Inputs: start/stop, drive ready/fault, run feedback, jam/position/encoder as applicable. Outputs: run request, speed setpoint, status/fault. Check start feedback timeout, stop timeout, drive fault and jam. Time-based travel is not precise distance control. For length-based travel verify encoder scaling, roller circumference, gear ratio, slip, stopping distance and accuracy.

### Servo
Confirm drive/axis type and Technology Object first. Define enable, homing, absolute/relative move, target/actual position, in-position, limits, following error, drive fault and timeout. Use supported Siemens motion instructions only after verifying CPU/TIA compatibility. Do not use a standard output bit as a substitute for a motion axis.

### Gripper/vacuum
Check open/closed feedback, part-present/gripped confirmation, vacuum level, command timeout and release confirmation. Do not infer a part is held from the close command alone.

### Rotary table
Define current/target station, index request, drive method, position feedback, clamp/lock confirmation, busy/done and timeout. Prevent indexing while the process requires the table locked or stations occupied.

### Vision
Use a handshake: request/trigger -> busy -> done -> result-valid -> consume result -> acknowledge/reset interface as vendor protocol requires. Correlate each result to the current part. Handle timeout, invalid/stale result, NG and communication fault.

### Weighing
Confirm interface, units, range, status/quality and calibration. Apply stability window, tare rules and tolerance limits only when measurement is valid. Handle unstable/out-of-range/communication fault.

### RFID/barcode
Confirm vendor interface and data layout. Implement request/response, timeout, retry limit, validity checks, duplicate-ID policy and part/station association. Never invent byte offsets or claim MES upload without a tested interface.

## 3. Analog scaling pattern
Formula: `Engineering = (Raw - RawMin) / (RawMax - RawMin) * (EngMax - EngMin) + EngMin`.
Check `RawMax <> RawMin`, channel diagnostics, module range, units, overflow and out-of-range policy. Raw values and 4-20 mA representation vary by module/configuration; verify before use.

## 4. Alarm structure
For each alarm define unique code, source, condition, severity, operator text/action, active state, acknowledgement behavior, reset permissive and recovery steps. Distinguish active cause from latched record and acknowledgement. Do not clear all alarms unconditionally.

## 5. Minimum test matrix
Test normal cycle; missing part; stuck/held sensor; sensor contradiction; actuator timeout; drive not ready/fault; stop during cycle; mode change; reset while cause remains; reset after cause clears; power/PLC restart; communication loss/recovery; invalid/stale device result; repeated trigger; out-of-range analog; illegal state; and safe-system interaction per approved test plan.
For every test record initial conditions, injected condition, expected state/output/alarm, reset/recovery behavior and pass/fail. Never perform hazardous fault injection on live machinery without a qualified, approved safe test procedure.

## 6. Example library policy
When providing sample code, include complete declarations, target block type, required UDT/DB/instance dependencies, assumptions, call example and tests. Mark examples as illustrative until compiled on the target CPU/V17. Do not present old generic examples as production-ready without review; correct issues rather than preserving a faulty pattern for compatibility.
