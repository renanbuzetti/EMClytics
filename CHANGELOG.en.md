# What's new in EMClytics

Each version lists what is new, what changed and what does not exist yet.
Version numbers: MAJOR.MINOR.PATCH. The 0.x series is a preview.

## [0.3.2] — 2026-11-04

### Changed
- **Damaged export:** an unreadable record (truncated or corrupted file) no longer brings the analysis down. It is left out, a bar at the top of the board says in which files this happened, and the rules that use the affected data show that it is partial.
- **More protection when opening:** a corrupted `.Z` file, or one that would go over the safety limit in memory (1 GB per file), is refused with a clear message and the path of the file inside the export.
- **Messages without your folder:** opening errors show only the name of the chosen file or folder, not the path with your user name. The unexpected-error log and "Copy details" remove your personal folder in any form it appears in (before, it slipped through when it came inside the error message).

## [0.3.1] — 2026-11-04

### New
- **Start screen:** before you open a board, the program shows "Open ODB++ folder", "Open ODB++ file" and **"Open the example board"**, plus the boards you opened last.
- **Example board:** the LimeSDR Mini 2.5, by Lime Microsystems (Solderpad Hardware License v2.1, included), with an illustrative design intent. You can see findings, illustrations and the report without exporting anything. Also in File › Open the example board.
- **Recent boards:** on the start screen and in File › Recent.
- **Drag and drop:** drop the ODB++ folder or zip on the window to open it. Drop `project_emc.yaml` together to use the design intent.
- **Unexpected error:** instead of the program closing without warning, a window explains what happened and offers **Copy details** to open an issue. A local log (`%LOCALAPPDATA%\EMClytics\registro`) keeps the error, with no board data and without your user name.

### Changed
- **Failure to open:** the message says what to do, for example choose the export folder or export again with the netlist and the components.
- **Data safety:**
  - the temporary copy of the board (when opening a .tgz or .zip) is deleted even when opening fails;
  - leftovers from an interrupted import are cleaned when the program starts;
  - before uncompressing, the program checks the free space and a safety limit (4 GB and 200,000 items), against a malicious file that would fill the disk;
  - a corrupted file becomes a clear message.

## [0.3.0] — 2026-11-04 — first public preview (free)

### New
- **ODB++ import** (folder, .tgz or .zip): layers, stack-up, tracks, planes (including negative ones), pads, vias, components and BOM properties.
  - Checked with exports from Altium Designer and Siemens Xpedition. The program follows the ODB++ 8.1 specification: other tools that export ODB++ (Cadence, KiCad, Zuken and others) should open, but have not been tested yet.
  - Files in inches or millimeters.
  - Rigid-flex: the rigid and flexible zones are read, and a plane that only exists in the rigid parts does not become a "slot" in the flexible region. The coverlay goes into the stack-up.
  - HDI: blind and buried vias; stacked or staggered vias count as a single connection.
  - A hatched polygon, which ODB++ exports as hundreds of lines, is detected and reported. It does not become a plane, and the openings of the mesh do not count as slots.
- **Rule base:** 288 rules of PCB layout good practice for EMC: zoning, planes and return path, decoupling, interfaces and filtering, transmission lines, stack-up, crystals, system ESD and others. Each rule gives the physical mechanism and the suggested fix.
- **25 automatic checks:** RP-007, 012, 029, 030, 031, 036, 042, 047, 072, 073, 074, 109, 110, 111, 112, 116, 117, 120, 122, 158, 167, 169, 183, 190 and 192.
  - Manual rules appear as a checklist.
  - Rules that depend on missing data appear as "not verified", with the reason. Never as passed.
  - A rule that ran and found nothing appears as "No finding" (it does not mean the board passes the tests).
  - Each finding has a confidence (high, medium or low), separate from the severity, with the reason.
- **Findings:**
  - a summary at the top, like a DRC Summary: violations and warnings, one row per rule with the count and the Total; a click takes you to the occurrences;
  - a short detail: the problem, how to fix it and, for the rules with a calculation, the calculation done with the board's values. The rest is collapsed;
  - clicking a finding shows the spot on the board and, if you want, only its layers;
  - an animated illustration of the problem and the fix, side by side, with the current moving.
- **Design intent** (`project_emc.yaml`): fMAX, critical nets, external connectors, grounds kept apart on purpose, accepted exceptions and other data the export does not carry.
  - Editing through a form, without losing the comments in the file.
  - A notice on the board tab while the design intent has not been filled in.
- **Visual interface:**
  - board with layers, zoom, net highlight and search;
  - stack-up cross-section, with the role of each layer and the reference plane of each signal layer;
  - interfaces and filtering, per connector, with the path from each pin to the IC;
  - "Accept as exception", with justification, author and date;
  - light and dark theme (View › Theme), which can follow Windows.
- **Languages:** Portuguese and English, in the Idioma / Language menu (the change applies when the program restarts; the first time, the program opens in the Windows language). Interface, findings, rules and report in both languages.
- **HTML report** with the same summary as the screen and the same visual identity. It follows the light or dark theme of the system of whoever opens it.
- **Windows executable:** a folder with `EMClytics.exe`, no Python installation needed.
- **Privacy:** everything runs on your computer; the program does not use the internet.

### Not yet
- Other input formats (IPC-2581 and the rest).
- Exports from Cadence, KiCad and Zuken checked with real boards.
- A stack-up of its own for the flexible zone of rigid-flex boards (today, the analysis uses the rigid zone's).
- Integration with Altium (a script that exports the board and opens the program).
- Update notice inside the program.
- Pro version: PDF report, comparison between revisions, project history and more checks.
