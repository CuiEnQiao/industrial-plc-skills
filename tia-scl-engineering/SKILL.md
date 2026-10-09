---
name: tia-scl-engineering
description: Main engineering workflow for reliable Siemens SCL programs: architecture, interfaces, reusable FBs, source generation, syntax, code review and debugging.
---
# TIA Portal SCL Engineering
Target: TIA Portal V17 with S7-1200/S7-1500, subject to exact CPU/firmware support. Use industrial-machine-standards for safety/process rules, plc-project-intake for requirements, and tia-v17-tutorial for UI steps.

## 1. Mandatory workflow
1. Summarize process and unknowns.
2. Create I/O list and signal descriptions; unknown physical addresses stay TBD.
3. Design state/sequence table and interlocks.
4. Define block architecture and call order.
5. Define interface/data types and units.
6. Generate SCL in dependency order.
7. Review edge cases, scan-cycle behavior, output ownership and fault recovery.
8. Provide tutorial, compile/debug plan and test cases.
Never start with a monolithic OB1 for a multi-device machine.

## 2. Recommended architecture
- OB1: calls machine-level logic.
- FB per reusable/stateful device: cylinder, conveyor, gripper, station, vision, RFID, etc.
- FB for automatic sequence and alarm management.
- FC for stateless calculation/conversion.
- UDT for repeated device/alarm/product structures.
- Global DB for recipes/configuration/shared process data; instance DB for FB state.
Architecture is a recommendation, not a rigid rule; scale it to the machine.

## 3. Variable rules
- `VAR_INPUT`: command/data received from caller.
- `VAR_OUTPUT`: status/results returned to caller.
- `VAR_IN_OUT`: caller-owned data intentionally read/written by reference.
- FB `VAR`/Static: state retained in instance data between calls/scans.
- `VAR_TEMP`: scratch values only; do not use for persistent state.
- Constants should be constants where supported.
Use explicit types and units. Avoid implicit mixed-type arithmetic. Check conversion range, signedness and overflow.

## 4. Naming and interfaces
Use consistent names such as `I_PartPresent`, `Q_CylinderExtend`, `Cmd_Start`, `Sts_Running`, `Sts_Extended`, `Alm_ExtendTimeout`, `Par_ExtendTimeout`, `Step`, `FaultCode`. Names are examples, not physical addresses.
Keep command, output, feedback, status, parameter and alarm separate. Physical outputs should have one clear owner. Use symbolic access unless absolute addresses are specified and verified.

## 5. SCL details to verify
- Assignment `:=`; comparison `=`, `<>`, `<`, `<=`, `>`, `>=`; `IF/ELSIF/ELSE`, `CASE`, loops.
- Time constants carry units (`T#500ms`, `T#2s`).
- TON/TOF/TP require correct instances/call pattern and correct interpretation of IN/PT/Q/ET.
- R_TRIG/F_TRIG instances retain previous signal state; call consistently.
- A level condition is not an edge. Held commands/sensors can retrigger unless guarded.
- Do not reset state every scan. Startup initialization is different from cyclic execution.
- Arrays require bounds checks; analog scaling requires validated range/units; strings and date/time support must be verified for the target.
- Verify instruction names/signatures and CPU support in TIA V17. Never fabricate APIs.
- Avoid unsupported or ambiguous syntax in external source; confirm block source format against TIA V17.

## 6. External source vs block body
If user asks for a full `.scl` external source, provide complete valid source declarations and block endings, dependency order, filename/encoding guidance and expected generated objects. If user wants code for an existing block, provide only the block implementation body and explicitly say where it goes. Do not include Markdown fences inside the saved file. Warn before overwriting same-name blocks.
Never claim a `.scl` file is a complete `.ap17` project. Openness/XML, external source and a native TIA project are different artifacts.

## 7. Sequence logic
Prefer `CASE` state machines for multi-step processes. Each state must have documented action, transition condition, timeout and failure path. Use explicit numeric constants or an enum/UDT where supported and appropriate. Prevent unintended multiple transitions in one scan; choose and explain the transition strategy. Define invalid-state behavior without hiding faults by silently resetting to Idle.

## 8. Reusable device FB contract
For each FB, document:
- Purpose and assumptions
- Inputs/outputs/static state
- Command arbitration and permissives
- Expected feedback and done conditions
- Timeout/diagnostic conditions
- Fault latch, acknowledgement and reset conditions
- Startup/restart behavior
- Example call and instance creation
- Tests for normal and abnormal cases

## 9. Code review checklist
Check all variables declared; types match; timers/edge instances valid; FB has instance data; dependencies exist; outputs have one owner; no unintended latches; reset conditions are correct; no stale result use; no unbounded array access; analog division cannot divide by zero; timeout values have units; no unverified absolute address; unsupported CPU instructions are avoided.

## 10. Error handling
Ask for exact compiler text/screenshot, target block, declarations and 10-20 surrounding lines. Explain `error -> cause -> exact correction -> recompile check`. Check spelling/declarations, type/direction, block call parameters, DB/instance DB, UDT dependencies, timer instances, array bounds, CPU support and V17 compatibility. Never say code compiled unless the user confirms or a real compiler result is available.

## 11. Required deliverables
For substantial tasks provide: process summary, I/O list, block map/call order, variable table, full code, assumptions, V17 tutorial, compile/import steps, monitoring steps, normal and fault tests, known limitations. Keep the response concise but never omit safety-critical or integration-critical information.
