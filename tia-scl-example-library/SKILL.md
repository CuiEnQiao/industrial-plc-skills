---
name: tia-scl-example-library
description: Reusable SCL code examples for Siemens TIA Portal V17. Use as reviewed starting points for motors, analog scaling, ramping, stack lights, arrays, shift registers and recipes.
---
# SCL Example Library
These are educational templates, not certified drop-in machine programs. Verify syntax against the exact CPU/TIA V17 installation, declare every dependency, compile and test before hardware use. Adapt safety, diagnostics, interfaces and reset behavior to the actual machine.

## 1. Motor run latch pattern (FB implementation body)
Required inputs: `StartEdge`, `Stop`, `DriveReady`, `DriveFault`, `RunFeedback`, `AutoRequest`. Outputs: `RunCmd`, `Running`, `Fault`. Persistent `RunLatch` and `FaultLatch` belong in FB Static.
```scl
IF #DriveFault OR NOT #DriveReady THEN
    #FaultLatch := TRUE;
END_IF;
IF #Reset AND #DriveReady AND NOT #DriveFault THEN
    #FaultLatch := FALSE;
END_IF;
IF #Stop OR #FaultLatch THEN
    #RunLatch := FALSE;
ELSIF #AutoMode AND #AutoRequest AND #DriveReady THEN
    #RunLatch := TRUE;
ELSIF NOT #AutoMode AND #StartEdge AND #DriveReady THEN
    #RunLatch := TRUE;
END_IF;
#RunCmd := #RunLatch AND #DriveReady AND NOT #DriveFault AND NOT #FaultLatch;
#Running := #RunFeedback;
```
This is a logic pattern only. Add start/stop feedback timeout, overload input, command arbitration and the actual drive interface. Safety-related stops must not depend on this standard logic.

## 2. Analog scaling (FC body pattern)
Inputs: `RawValue`, `RawMin`, `RawMax`, `EngMin`, `EngMax`; outputs: `ScaledValue`, `OutOfRange`.
```scl
#OutOfRange := (#RawValue < #RawMin) OR (#RawValue > #RawMax);
IF #RawMax = #RawMin THEN
    #ScaledValue := #EngMin;
    #OutOfRange := TRUE;
ELSE
    #ScaledValue := #EngMin
        + (INT_TO_REAL(#RawValue - #RawMin)
        / INT_TO_REAL(#RawMax - #RawMin))
        * (#EngMax - #EngMin);
END_IF;
```
Verify the actual module range and whether the channel is 0-10 V, 4-20 mA or another range. Define whether out-of-range values are clamped, rejected or alarmed; do not silently hide faults.

## 3. Ramp / soft-start pattern
For a command reference, calculate a ramped setpoint with a known task cycle `dt` in seconds. Clamp target and output to configured limits. Handle zero/negative ramp time explicitly. Do not use a homemade ramp as a substitute for a certified motor starter, VFD ramp or servo motion profile.
```scl
IF #RampTime_s <= 0.0 THEN
    #Output := #Target;
ELSE
    #Step := ABS(#Target - #Output) * #dt / #RampTime_s;
    IF #Output < #Target THEN
        #Output := MIN(#Output + #Step, #Target);
    ELSIF #Output > #Target THEN
        #Output := MAX(#Output - #Step, #Target);
    END_IF;
END_IF;
```
The task cycle must be defined and the ramp algorithm reviewed for constant-rate behavior, output limits and reversal.

## 4. Stack light pattern (FC/FB body)
Mode values must be defined in a UDT/constant scheme; flash signals should come from a validated clock source.
```scl
#Red := FALSE;
#Yellow := FALSE;
#Green := FALSE;
CASE #Mode OF
    0: ;
    1: #Green := TRUE;                 // Ready
    2: #Green := #FlashSlow;           // Running
    3: #Red := #FlashFast; #Yellow := TRUE; // Warning/fault policy defined by project
    4: #Red := TRUE;                   // Fault
    5: #Yellow := #FlashSlow;          // Maintenance
    ELSE
        #Red := TRUE;                  // Invalid mode indication
END_CASE;
```
Set signal semantics and colors according to the plant's standards; do not assume universal meanings.

## 5. Array maximum (FC body pattern)
For a fixed array `Data[1..N]`, ensure N>=1 and check bounds.
```scl
#MaxValue := #Data[1];
#MaxIndex := 1;
FOR #i := 2 TO #N DO
    IF #Data[#i] > #MaxValue THEN
        #MaxValue := #Data[#i];
        #MaxIndex := #i;
    END_IF;
END_FOR;
```
For open arrays, verify `LOWER_BOUND`/`UPPER_BOUND` support and parameter syntax in the installed CPU/V17 version.

## 6. Shift register / product tracking pattern
Use a persistent array and a rising-edge instance. Decide whether index 1 is newest or oldest, and define startup/reset behavior.
```scl
#ClockEdge(CLK := #Clock);
IF #Reset THEN
    #FillCount := 0;
    FOR #i := 1 TO 10 DO
        #Register[#i] := 0.0;
    END_FOR;
ELSIF #ClockEdge.Q THEN
    FOR #i := 10 TO 2 BY -1 DO
        #Register[#i] := #Register[#i - 1];
    END_FOR;
    #Register[1] := #DataIn;
    IF #FillCount < 10 THEN
        #FillCount := #FillCount + 1;
    END_IF;
END_IF;
#DataValid := (#FillCount >= 10);
```
Do not use zero alone to represent invalid product data; keep a validity/part-ID field where needed.

## 7. Recipe manager pattern
Define a `UDT_Recipe` with validated parameter fields and a recipe array in a DB. Validate recipe index, range and compatibility before activating a recipe. Save/load commands should be edge-triggered and access-controlled where required. Use an explicit `ActiveRecipe`, `Busy`, `Done`, `Error` and error code. Do not silently overwrite production parameters during an active cycle unless the process specification permits it.

## 8. Example acceptance checks
For each sample: declare all interface variables; confirm block type; check dependencies/instances; compile for target CPU; test normal path; test limits and invalid input; test reset with fault active; confirm output ownership; simulate before physical equipment. Mark unverified samples as examples, not production-ready code.
