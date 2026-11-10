# What's new in EMClytics

Each version lists what is new, what changed and what does not exist yet.
Version numbers: MAJOR.MINOR.PATCH. The 0.x series is a preview.

## [0.3.4] — unreleased

### Changed
- **New visual identity:** a new logo (four PCB tracks with vias, in petrol blue and copper) and a new icon, legible down to 16 px. Both themes have new colors: the light one on a cool gray with petrol blue buttons, the dark one on charcoal with copper buttons. The start screen shows the logo, the tabs and titles use Montserrat (included, under the SIL Open Font License), and in the dark theme the illustrations of the findings sit on a light card, so the drawing keeps its colors. The HTML report opens with the logo, which follows the light or dark theme of whoever opens it.
- **More assertive rules:** before reporting, each rule below checks what is around the finding and its dimensions. On the 12 real example boards, the findings of these rules went down from 402 to 314.
  - **RP-183:** a track that reaches a contact without a component (a loose pad, or a hole the size of the board's component lead holes, where a wire or pin goes) has a destination and is no longer a finding. With fMAX, a track up to λ/100 is left out too: it is a lumped load, not an antenna.
  - **RP-074:** each decap uses the via closest to it along the copper: the mesh of GND tracks under a BGA no longer makes a decap "share" its neighbor's via, and a pair that shares both the 0V via and the supply via is a single finding. A capacitor connected to a connector pin, a crystal or a clock is a filter, not decoupling, and is left out; on a net the program does not recognize as a supply, the finding comes with medium confidence and says why. The net after a ferrite coming from a supply is now a supply (also in RP-072 and RP-073).
  - **RP-109 to RP-112:** with fMAX, a via connected to a single pin by a track up to λ/100 is the pin's escape (the device connection), not a layer change in the middle of the route.
  - **RP-112:** when the slot near the via is merged antipads, the message says so, and whether the clearance of the via itself is part of them.
  - **RP-190:** only parallel stretches are reported, with the clock and the other track running together for longer than the minimum spacing itself, and the message shows the length of that stretch. An approach at an end or at a bend does not count.
  - **RP-169:** with no εr in the stack-up or in the design intent, it uses 4.2 (FR4) to leave out the planes whose resonance is above fMAX.
  - **Clock by name:** XIN and XOUT only as a whole word (RXOUTIP, TXININ and MUXOUT are not clocks), and a name with a supply word (AMS_CLK_VCC3V3) is not a clock. This applies to RP-190 and to the critical net suggestions.
- **Reference plane of each layer:** on a signal layer between two planes, RP-036 (track crossing a slot), RP-031 (critical track near the plane edge) and RP-109 to RP-112 look only at its reference plane: the nearer one, from the stack-up thicknesses. Before, a slot in the plane on the other side became an error. Without the thicknesses, the 0V plane counts, and the finding says so; to be sure, declare the reference in `referencias_camadas` in `project_emc.yaml` (e.g. `L3: L2`) or give the `stackup_manual`.

## [0.3.3] — 2026-11-10

### New
- **Command line in the executable:** `EMClytics-cli.exe`, next to `EMClytics.exe`, creates the HTML report without opening the window (for example, in a script): `EMClytics-cli --entrada <ODB++> --intencao project_emc.yaml --saida report.html`. The language is the window's (or `--idioma`), and the exit code tells whether it worked.
- **Progress and Cancel:** the bar at the bottom shows the layer being read and the rule being evaluated; Cancel (or Esc) stops the analysis, and the board that was on screen stays.
- **Check for updates** (Help menu): opens the versions page in the browser. The program still does not use the internet.
- **Refdes prefixes in the design intent:** in `componentes_especiais.prefixos_refdes`, give the type of the components of a prefix the program does not recognize (e.g. `LED: diodo`). It applies to the components without a type from the CAD.
- **Electromagnetic zones (Zones tab):** the program recognizes the zone of each IC from what is connected to it (connector, crystal, antenna, switching supply inductor, high-speed nets, net names) and says whether the current layout mixes the zones. It also draws a proposal over the board outline: the interface with all the connectors on one edge, the zones in bands, the ICs inside them and where shielding (BLS) goes, with the dimensions from fMAX. "Save drawing…" writes the drawing as SVG. In `zonas[].funcao` in `project_emc.yaml`, you set the zone of the components the program classified wrong.
- **Report with the zones:** the HTML report has a new "Electromagnetic zones" section, with the drawings of today and of the proposal, the conflicts, the zones that call for shielding and why each IC is in its zone. The drawings follow the light or dark theme of whoever opens the report.
- **Zones with the connectors in place:** the Zones tab has a third view, **Fixed connectors**, for when the connectors cannot move (enclosure, panel). The connectors and antennas stay where they are and the other zones are arranged around them, with the high-speed part as far as possible from the connectors and shielding recommended where the distance is not enough. A connector in the middle of the board becomes an island the components go around. The report has this third drawing.
- **Grounds (0V):** the Zones tab has a **Grounds (0V)** view, also in the report. It shows each 0V net with its role (main 0V; separated by function, like AGND/DGND; chassis ground; input 0V before the filter; isolated), where the copper of each one is, the components that connect one to another, the boundaries with the main 0V and the ICs with 0V on different nets. Then it says what to do, in the order of the rules: a single 0V with separation by zoning; if the split stays, stitching along the whole boundary; isolation with capacitors; chassis ground with RF bonds next to the connectors.
- **RP-008 and RP-023 checked:** off-board connectors on one edge, with no IC or decoupling capacitor between them and the same 0V; a declared zone larger than λ/10 at fMAX calls for BLS provision.
- **RP-043, RP-044 and RP-045 checked:** connectors on different parts of the reference plane; stitching of a kept split (pitch below λ/10 at fMAX and all positions populated the same way); stitching of galvanic isolation with capacitors.

### Changed
- **Faster import:** on a real reference board, the import went from 9 s to 6 s, with the same findings.
- **RP-036:** when the track crosses the gap between the plane and the copper of a signal net routed on the plane layer, the message speaks of a "gap in the plane" (before, it called the signal net a plane). This case is not mitigated by capacitors, which the rule provides only between two planes.
- **RP-042:** chassis ground and the input 0V before the filter no longer count as a split of the circuit 0V: they become one warning per net that explains the role (before, one violation per layer).
- **Clearer Interfaces and filtering tab:** each pin shows as a path from the connector to the IC, even when the IC sits after a series resistor or ferrite. Each part sits where it is on the board, closer to the connector or to the IC (by straight-line distance), with its symbol (resistor, ferrite, capacitor, TVS), and a badge gives the pin's status. Click a part to see it on the board. The text is shorter; how the tool reads the path is one click away.
- **Stack-up cross-section:** the copper shows as in a board cross-section (solid on planes, as tracks on signal layers), with the dielectric weave, L1, L2…, the total thickness and an arrow from each signal layer to its reference plane.
- **Fewer pointless alarms**, checked on 12 real example boards (the findings of these rules went from 514 to 300):
  - **RP-183:** a track that does not leave the component's pad (it is the pad itself) is no longer a finding; the measured length is only what leaves the pad.
  - **RP-012 and the Interfaces tab:** a single-pad component (test point, contact, wire pad) is not treated as a connector or as an IC.
  - **RP-029:** copper with no net or on 0V (the reference, chassis ground) does not need a plane underneath: mounting holes, connector shells and chassis-ground bonding capacitors are gone.
  - **RP-074:** the search for each decap's via no longer goes through another component's pad (before, a row of decaps, each with its own via, became one finding per via), and the vias shared by the same decaps come in one finding. A decap with no via of its own, using a neighbor's via, is still flagged.
  - **RP-169:** a plane piece smaller than 1 mm² is not evaluated; with fMAX given, neither is a plane whose 1st resonance is above fMAX.
  - **RP-190:** a power or control net with "CLK" in its name (VCC3P3_CLK, CLK_SEL) is not a clock; the pin escape between the IC's pads and the other line of the same crystal do not count.
  - **RP-109 to RP-112:** a via in the pin's pad and a pin escape under the component itself (BGA fanout) are the direct connection to the pin, which the rule allows.

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
