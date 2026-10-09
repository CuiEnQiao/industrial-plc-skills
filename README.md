# Industrial PLC Skills for Claude Code
Target: Siemens TIA Portal V17, S7-1200/S7-1500, SCL. Eight focused skills keep context modular.

## Install
Copy all eight skill folders into your project's `.claude/skills/` directory (or your configured user-level Claude skills directory). Keep each folder and its `SKILL.md` intact.

## Skills
- `plc-project-intake`: requirements interview and design basis.
- `tia-platform-capabilities`: V17/CPU capabilities and compatibility checks.
- `industrial-machine-standards`: production rules, safety boundaries, interlocks, alarms and recovery.
- `tia-scl-engineering`: architecture, SCL workflow, source generation and debugging.
- `tia-scl-language-reference`: types, syntax, timers, edges, arrays, analogs, initialization, PID/HMI/comms.
- `tia-scl-example-library`: reviewed starting patterns for motor, scaling, ramp, stack light, arrays, shift register and recipes.
- `tia-v17-tutorial`: Chinese click-by-click beginner instructions.
- `tia-device-patterns-and-tests`: device interfaces, cylinders, conveyors, servos, grippers, vision, weighing, RFID and test matrix.

## Suggested workflow
New project: intake -> platform capabilities + industrial standards -> engineering -> device patterns/examples -> tutorial. For a syntax question, load the language reference. For UI work, load the tutorial. For any real machine, require review, compile/test evidence and qualified commissioning.
