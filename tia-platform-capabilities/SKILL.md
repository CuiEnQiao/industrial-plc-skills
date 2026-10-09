---
name: tia-platform-capabilities
description: Technical capability and compatibility reference for Siemens TIA Portal V17, S7-1200 and S7-1500, SCL, blocks, motion, I/O, communications and simulation.
---
# Siemens TIA Portal V17 / S7-1200 / S7-1500 Reference
Use with plc-project-intake and tia-scl-engineering. Confirm exact CPU, firmware, modules, licenses and V17 update before relying on a feature.

## 1. Core objects
- OB: event/program organization, e.g. cyclic OB1, startup OB, cyclic interrupt OB, hardware interrupt OB (availability depends on CPU).
- FB: stateful reusable logic; instance data stored in an instance DB or multi-instance.
- FC: calculation/logic without its own persistent instance state.
- Global DB: shared parameters/data. Instance DB: FB instance state.
- PLC data type (UDT): reusable structured data type.
- PLC tags: symbolic hardware/process variables. HMI tags are a separate HMI-side configuration.
- Technology Objects: configured objects for supported motion/PID functions; not interchangeable with an arbitrary SCL FB.

## 2. Common capabilities (CPU/firmware-dependent)
Digital/analog I/O; timers/counters; edge detection; state machines; scaling and engineering units; alarms/diagnostics; HMI interface; recipe/data handling; PROFINET IO; supported S7 communication; Modbus TCP or other protocols when supported/configured; drive control; technology objects and motion instructions on supported CPUs; web/server/security functions only where supported.
Never imply every CPU supports every instruction, technology object, axis count, communication service, memory size or safety feature.

## 3. S7-1200 vs S7-1500
Do not make blanket performance or feature claims. Compare exact order numbers and firmware using Siemens documentation. Check available memory, integrated ports, supported modules, technology functions, motion axes, communication resources, safety variant, cycle time and license requirements.

## 4. SCL and block constraints
- Use Siemens SCL syntax for the target TIA Portal V17 environment; do not copy another vendor's IEC ST dialect without verification.
- Distinguish block-body code from a complete external `.scl` source file. Do not mix Markdown fences into source files.
- Use symbolic tags by default. Use absolute addresses only when supplied/required and verified against hardware configuration.
- Use FB Static/instance data for values that must persist across scans; TEMP is not persistent.
- Timers and edge detectors need valid instances and correct call patterns. Do not reset state every scan.
- Confirm availability/signatures of instructions for the selected CPU and V17 installation before use.
- Check optimized access, data layout, HMI/communication access and external partner constraints before choosing DB layout.

## 5. Motion, drives and communications
Before generating drive/servo code, identify exact device, control method, telegram/PZD mapping, technology object, feedback signals, homing method, limits, units and error handling. Verify MC instruction support and parameters for the installed TIA/CPU.
For PROFINET/Modbus TCP/RFID/vision/robot interfaces, distinguish hardware/network configuration, connection setup, protocol data exchange and SCL parsing/state handling. Never invent device addresses, telegram layouts, byte offsets or vendor blocks.

## 6. Analog signals
Flow: raw value -> channel diagnostics/range check -> scaling -> engineering units -> plausibility checks -> process logic. Confirm module range and representation; do not assume 0..27648 universally. Guard against divide-by-zero, overflow, invalid quality and out-of-range values.

## 7. Verification
Use official Siemens manuals/help for the exact CPU and instruction. If a capability is uncertain, say what must be checked and where; do not fabricate certainty. PLCSIM availability/coverage depends on CPU, license, version and hardware features; simulation does not validate physical safety.
