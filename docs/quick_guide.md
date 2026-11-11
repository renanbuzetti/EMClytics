# EMClytics quick guide

## 1. Export the board (ODB++ or IPC-2581)

In your CAD tool, export the PCB as ODB++ or as IPC-2581 (revision B or C).

- **ODB++:** in Altium Designer, with the PCB open: **File › Fabrication Outputs › ODB++ Files**. The program has been checked with exports from Altium Designer, Siemens Xpedition and KiCad 9; other tools that export ODB++ 8.1 should open.
- **IPC-2581:** in KiCad, with the PCB open: **File › Fabrication Outputs › IPC-2581…**. KiCad's IPC-2581 carries the stack-up (thickness and dielectric constant of each layer), which its ODB++ does not, so prefer it in KiCad. The program has been checked with KiCad 9; other tools that export IPC-2581 should open.

For a complete analysis, the export needs to include:

- the copper layers, the drill data and the board outline;
- the net list and the components (EMClytics warns when they are missing);
- if possible, the dielectric layers, with thickness and dielectric constant (stack-up).

The ODB++ result is an `odb` folder (or a `.tgz`/`.zip`), usually inside `Project Outputs`. The IPC-2581 result is an `.xml` file (or a `.zip` with it inside).

## 2. Open it in EMClytics

On the start screen, **Open ODB++ folder** and choose the `odb` folder, or **Open ODB++ or IPC-2581 file** for the compressed ODB++ or the IPC-2581 file; the program recognizes the format from the content. You can also drag the folder or the file onto the window (with `project_emc.yaml` together, if you have one). The boards you opened last are in **Recent**. To try the program without exporting anything, use **Open the example board** (the LimeSDR Mini 2.5, with an illustrative design intent). The analysis takes a few seconds (longer for large boards), with the progress in the bar at the bottom; **Cancel** (or Esc) stops it, and the board that was on screen stays. Then the board appears with its layers. If the export has unreadable records (truncated or corrupted file), the board opens with a bar at the top that says in which files, and the affected data show as partial in the rules.

- **Layers** (left): turn layers, vias, refdes and markers on and off. With **Only the finding's layers**, the board shows only the layers of the chosen finding; **Restore layers** goes back to what was on.
- **Findings** (right):
  - at the top, the summary: violations, warnings and accepted findings, and one row per rule with the count and the Total, like a DRC Summary. A click on a row takes you to the rule's occurrences;
  - below, the findings grouped by rule, with a filter (open, all findings, violations only, warnings only, accepted);
  - at the end of the list are the rules with no finding, the checklists and the rules not verified.
- **Finding details** (bottom): what was found, the measurement and the limit, the confidence and how to fix it. For the rules with a calculation, the calculation done with the board's values. Why it matters stays collapsed, one click away. On the right, an animated illustration shows the problem and the fix; drag the edge of the panel to enlarge it. **Go to location** takes the board to the spot.

The other tabs:

- **Stack-up and recommendations:** the cross-section of the board, with the role of each layer (signal, 0V plane, PWR plane) and the reference plane of each signal layer, and the routing recommendations by signal type.
- **Interfaces and filtering:** each external connector, with the path from each pin to the IC and the filtering tips.
- **Zones:** the electromagnetic zone of each IC (interface, radio, analog, digital, high-speed digital, switching power supply), whether the current layout mixes the zones and a proposal over the board outline: the separated zones, the ICs and the connectors inside them and where shielding goes. **Save drawing…** writes the drawing as SVG to send to whoever does the layout. If the program gets a component's zone wrong, correct it in `zonas[].funcao` in `project_emc.yaml`.
  - **Fixed connectors:** when the connectors cannot move (enclosure, panel), this view keeps the connectors and antennas where they are and arranges the other zones around them.
  - **Grounds (0V):** the board's 0V nets (main, AGND/DGND, chassis, input, isolated), where the copper of each one is, what connects one to another and what to do with them.

The theme is in **View › Theme** (Follow Windows, Light or Dark). The language (Portuguese or English) is in the **Idioma / Language** menu; the change applies when the program restarts. The first time, the program opens in the Windows language.

## 3. Give the design intent

The export does not say what the highest frequency of interest is, which nets are critical or which connectors leave the board. This information goes in `project_emc.yaml`, next to the export. Until it is filled in, a notice at the top of the board shows how many rules are waiting for this data.

- In the **Design intent** tab, create the file and fill in with the form fMAX, critical nets, external connectors and the grounds (0V) kept apart on purpose, with the reason.
- The other fields (crystals, zones, manual stack-up, rule parameters, refdes prefixes) stay in the file itself; the commented template is [project_emc.example.yaml](project_emc.example.yaml). The keys of the file are in Portuguese in both languages: they are the file format.
- A newly created file starts as a **draft**: the findings that depend on it come out with lower confidence until you review it and change `rascunho: true` to `false`.

## 4. Read the results

| Status | What it means |
|---|---|
| Violation / Warning | The rule found the condition on the board |
| No finding | The rule ran and did not find the condition (this does not mean the board passes the tests) |
| Not verified | Some data is missing (from the export or from the intent); the reason is shown |
| Checklist | A rule that depends on judgment, mechanics or testing: check it by hand |

- **Severity** (high, medium, low): the weight of the EMC risk.
- **Confidence** (high, medium, low): how much the finding depends on a heuristic, for example, a net recognized only by its name.

## 5. Accept an exception

If a finding is acceptable in your project, select it and use **Accept as exception…**, with a justification. The exception goes into `project_emc.yaml`, with author and date, and the finding then appears as accepted.

## 6. Report

**Export HTML report** creates a file with the same summary as the screen, the findings of each rule, the rules not verified, the manual checklist, the rules with no finding and what the importer was able to read from the export. The report is in the program's language, shows the program version, the program that generated the export and an identifier of the rule base used, and follows the light or dark theme of the system of whoever opens it.

## 7. Command line

To create the report without opening the window (for example, in a script), use `EMClytics-cli.exe`, in the program folder:

```
EMClytics-cli --entrada <ODB++ folder or .tgz, or IPC-2581 .xml> --intencao project_emc.yaml --saida report.html
```

`EMClytics-cli --help` shows the options; they are in Portuguese, as are the keys of `project_emc.yaml`. The language is the same as the window's; to change it for one run only, use `--idioma en`. The exit code is 0 when the report was written, 2 when there is an error in `project_emc.yaml` and 3 when the board did not open.

To find out whether there is a new version, use **Help › Check for updates**: the versions page opens in the browser. The program itself does not use the internet.
