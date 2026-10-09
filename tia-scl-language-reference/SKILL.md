---
name: tia-scl-language-reference
description: Siemens SCL V17 syntax and data-type reference for S7-1200/S7-1500, including timers, edges, arrays, strings, analog scaling, state, HMI and communication.
---
# Siemens SCL Language Reference
Target: TIA Portal V17. Confirm exact CPU, firmware and instruction support. Do not infer Siemens syntax from another PLC brand's ST.

## 1. Types and conversion
Common types: Bool, Byte, Word, DWord, LWord, SInt, USInt, Int, UInt, DInt, UDInt, LInt, ULInt, Real, LReal, Time, Date, String[n], WString[n], Array, Struct, UDT, Variant. Availability varies by CPU/version.
Use explicit conversions where required, e.g. `INT_TO_REAL`, `DINT_TO_REAL`, `REAL_TO_DINT`. Check overflow, signedness and precision loss. Do not default every number to Int.
Use `:=` for assignment; `=` and `<>` for equality/inequality; `AND`, `OR`, `XOR`, `NOT` for logic. Local block variables use `#Name`; global DB access uses symbolic DB/member syntax.

## 2. Flow control
`IF ... ELSIF ... ELSE ... END_IF`; `CASE ... OF ... ELSE ... END_CASE`; `FOR ... TO ... BY ... DO ... END_FOR`; `WHILE ... DO ... END_WHILE`; `REPEAT ... UNTIL ... END_REPEAT`. Use `RETURN` only when early block exit is intended.
For complex machine sequences, prefer explicit CASE state machines and document invalid-state behavior.

## 3. Time and timers
Use units: `T#100ms`, `T#1s`, `T#5s`, `T#1m`; bare numbers are not time values.
IEC timer concept:
`#Timer_1(IN := #Start, PT := T#2s);`
`#Done := #Timer_1.Q;`
`#Elapsed := #Timer_1.ET;`
TON delays Q on a sustained TRUE input; TOF delays Q turning FALSE; TP produces a pulse. Confirm supported timer type/call style for the target. Timer instances must retain state across scans; FB Static/instance data is normally appropriate. Explain IN/PT/Q/ET to beginners.

## 4. Edge detection
`#RTrig(CLK := #Start); #StartEdge := #RTrig.Q;`
R_TRIG detects FALSE->TRUE; F_TRIG detects TRUE->FALSE. A level check is not an edge. Edge-instance state must persist and the instance must be called consistently. Add a separate busy/consumed guard when a held sensor could trigger another cycle.

## 5. Arrays, strings and UDT
Example fixed array: `ARRAY[1..10] OF INT`; validate bounds before indexed access. `LOWER_BOUND`/`UPPER_BOUND` and open arrays depend on supported CPU/version. For reverse loops verify bounds and step direction.
Siemens STRING/WSTRING are not C character arrays. Use only string operations supported in the installed V17/CPU environment.
Use UDT + DB + FB for repeated structured equipment data when useful. UDT is a data template, not executable logic.

## 6. Analog scaling
Pipeline: raw value -> channel status/range check -> engineering-unit scaling -> plausibility checks -> process logic.
`Eng=(Raw-RawMin)/(RawMax-RawMin)*(EngMax-EngMin)+EngMin`
Check `RawMax <> RawMin`, module range, current/voltage mode, channel configuration, units, out-of-range policy and data quality. Do not assume all modules use 0..27648.

## 7. Initialization, retentivity and state
Startup initialization (e.g. supported startup OB or validated first-scan mechanism) is not the same as resetting values every scan. Do not set a state variable to Idle every cyclic scan. Configure retentivity deliberately; do not assume all DB data persists across power cycles.
Define startup behavior for in-process parts and active commands.

## 8. Interlocks, alarms, modes and reset
Keep Auto and Manual command logic distinct; neither bypasses required safety permissives. For reversing motors use appropriate logic and hardware/electrical interlocking where required.
Separate alarm condition, active state, acknowledgement, latched history and reset permission. Reset only when cause is cleared and reset conditions are met; reset must not start motion.
Debounce only signals that need it. Use stable-time filtering or input hardware filters based on sensor and process behavior.

## 9. PID, HMI and communications
Before PID code, decide whether to use a supported Siemens PID Technology Object/PID Compact or a custom algorithm; do not claim a custom FB is equivalent to the official object. Explain required object configuration.
For PROFINET, Modbus TCP, S7 communication, drives, robots, vision, RFID and HMI, confirm CPU, peer device, protocol, hardware configuration, connection setup, data layout and supported blocks. Separate network setup from SCL data handling. PLC tags and HMI tags are distinct.
