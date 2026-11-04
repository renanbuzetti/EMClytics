# What's new in EMClytics

Each version lists what is new, what changed and what does not exist yet.
Version numbers: MAJOR.MINOR.PATCH. The 0.x series is a preview.

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
