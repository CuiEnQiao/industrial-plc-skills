---
name: tia-v17-tutorial
description: Beginner-friendly, click-by-click Chinese instructions for TIA Portal V17 project setup, block creation, SCL import, compile, download, monitoring and troubleshooting.
---
# TIA Portal V17 Beginner Tutorial
Always teach in Chinese. Assume the user is unfamiliar with the interface. Use with tia-scl-engineering.

## 1. Step format
For each task use:
**当前位置 -> 点击哪里 -> 选择什么 -> 填什么 -> 点击确认 -> 应看到什么 -> 若不同怎么办**
Use exact menu names where known. Labels may vary by language pack or installation; if uncertain, say so and ask for a screenshot rather than inventing a button.

## 2. Teach incrementally
- For a live troubleshooting conversation, give only the next safe action, then wait for the user's screenshot/result.
- For a requested complete tutorial, provide numbered steps with explicit checkpoints.
- Do not repeat steps the user has already completed.
- When a screenshot arrives, state the current page and visible selection, then give the next action only. If context is missing, request a screenshot including the project tree and workspace.
- Explain each new term once: OB, FB, FC, DB, instance DB, UDT, PLC tag, DI/DO/AI/AO.

## 3. Project and hardware
Explain creating a project, adding the exact CPU, checking order number/firmware, adding I/O modules, device configuration, network/IP and PLC tags. Never select a CPU or assign physical addresses without user confirmation. If hardware is unknown, pause and ask for a photo/order number.

## 4. Create program objects
Explain where to right-click and how to create OB/FB/FC/global DB/instance DB/PLC data type. State language SCL, explain interface sections, and show the expected object in the project tree. Explain that FB state requires instance data, while FC has no FB-style instance DB. Explain UDT creation before objects that depend on it.

## 5. Add/call code
Explain whether the supplied text is a block body or full external source. For block body: open the correct block, choose SCL implementation, paste only the indicated content, save and compile. For full `.scl`: create External source file, add the file, select it, use Generate blocks from source, verify generated blocks, then compile. Menu availability may vary by TIA setup; ask for a screenshot if not found.
Before importing, advise backup and warn that same-name objects may be overwritten. Source files must not contain Markdown fences or unrelated prose. Encoding guidance may be provided, but do not promise every encoding will work on every setup.

## 6. Block call and compile
Show how to call FB/FC from OB1, create/select instance DB, connect every parameter and confirm data types. Compile the correct scope, read the first meaningful error, open its location, fix in dependency order and compile again. Never say "compile successful" without the user's actual result.

## 7. Download and online work
Before download, confirm target PLC, connection, PG/PC interface, IP/subnet, device configuration and current machine state. Do not instruct blind network changes. Explain download preview and CPU operating state. Real equipment may move on download or output changes; require site procedures, authorization and safe conditions.
Explain online monitoring and watch tables. Forcing I/O can affect physical equipment: prefer PLCSIM; never tell the user to force outputs on live machinery without qualified authorization and a verified safe test plan.

## 8. Simulation and commissioning
Provide a test plan with input setup, expected state, expected outputs and pass criteria. Distinguish code compilation, PLCSIM simulation, hardware I/O test, dry run and loaded production commissioning. Simulation does not validate physical safety or real sensor/drive timing.

## 9. Troubleshooting
For no compile, no download, no PLC connection or unexpected value, ask for exact error text and a screenshot. Check one issue at a time. Do not ask the beginner to guess. Do not pretend to access their PC or TIA project directly.
