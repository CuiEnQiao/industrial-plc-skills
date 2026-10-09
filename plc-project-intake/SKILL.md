---
name: plc-project-intake
description: Use first for any new PLC/industrial automation request. Clarifies requirements, asks concise high-value questions, records assumptions, and defines deliverables before code generation.
---
# PLC Project Intake
Use this skill first for every new project or major change. Do not start coding until critical unknowns are identified.

## 1. Interview rules
- Ask in short, plain Chinese. The user is a beginner; explain technical terms briefly.
- Ask only questions that change hardware choice, control logic, safety, code structure, or acceptance criteria. Group related questions; avoid interrogating the user with a huge form.
- Ask one concise batch at a time, then update the requirements summary. Accept "unknown" and propose clearly labelled options.
- Never invent CPU order number, firmware, I/O addresses, sensor wiring, drive model, communication protocol, machine dimensions, safety category/PL/SIL, or timing values.
- Separate confirmed facts, assumptions, and unanswered questions.
- If a safe, useful draft can be made with assumptions, proceed with a clearly marked concept design; do not represent it as ready for real equipment.

## 2. Required intake
Confirm what is known:
1. Process goal and material/product flow.
2. Machine sequence: start condition, each action, completion feedback, end condition.
3. CPU: S7-1200/S7-1500 exact order number, firmware, TIA Portal version (default V17 only if confirmed).
4. Devices: sensors, cylinders/valves, motors/VFDs, servos, robots, vision, weighing, RFID, HMI.
5. I/O and interface: module/channel, signal type/polarity, device telegram/protocol, data format.
6. Modes: Auto, Manual/Jog, Single-step, Single-cycle, Continuous, Pause/Stop, Maintenance.
7. Faults: timeout, missing/contradictory sensor, overload, comms loss, recovery/reset.
8. Safety boundary: E-stop, guards, interlocks, safe torque off, pneumatic/hydraulic hazards, risk assessment owner.
9. Required deliverables: SCL blocks, importable source, I/O list, HMI tags, click-by-click tutorial, tests.

Ask only missing items relevant to the requested task.

## 3. Output before coding
Produce a compact "Design Basis":
- Confirmed facts
- Assumptions / TBD
- Process sequence
- I/O list with provisional symbols (never fake physical addresses)
- Mode and interlock summary
- Fault list
- Block architecture
- Test/acceptance criteria

For a sequence, ask about each transition: what starts it, what proves completion, and what happens on timeout/failure.

## 4. Change control
When the user changes a requirement, list affected blocks, tags, sequence states, alarms, tutorial steps, and tests. Preserve unrelated working behavior. Never silently remove features from an existing program.

## 5. Required final status
Label each deliverable as one of: Concept / Draft / Syntax-reviewed / Compiled by user / Simulated / Site-validated. Never claim to have compiled, downloaded, simulated, or validated a project unless evidence exists.
