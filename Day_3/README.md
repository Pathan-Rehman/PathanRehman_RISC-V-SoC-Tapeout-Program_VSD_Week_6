# Labs for CMOS inverter ngspice simulations

# IO placer revision

This lab guides you through advanced flows in the OpenLane digital design automation environment, focusing on post-floorplan pin configuration and cell characterization. The core objective is to explore, modify, and verify input/output (IO) pin strategies using OpenLane's IO placer, then prepare the cell for post-layout simulation and characterization—including rise time, fall time, and propagation delay—for an inverter example. Steps entail navigating OpenLane run directories, inspecting and editing configuration variables, rerunning flows, and examining changes, followed by setup for detailed characterization.

## Lab Steps

### 1. Inspect Completed Floorplan

- Ensure you are inside the latest OpenLane run directory (after synthesis and floorplanning).
- Open the results folder for the floorplan.

```bash
cd <openlane_project>/<latest_run>/results/floorplan
```
![PLACEHOLDER: screenshot/terminal-output/schematic]

- Locate and open the `.def` file to check the current pin configuration.

```bash
magic -T <your_tech_file> <your_layout_file>.def
```
![PLACEHOLDER: screenshot/terminal-output/schematic]

  - Alternatively, view or copy the configuration from `.def` into a simpler editor (e.g., Notepad).
![PLACEHOLDER: screenshot/terminal-output/schematic]

### 2. Read Current Pin Placement

- Use the LEF file for current pin data. If necessary, navigate up and into the temp directory to read the merged LEF.

```bash
cd ../../temp
magic lef read merged.tab.lef
```
![PLACEHOLDER: screenshot/terminal-output/schematic]

- Check the pin arrangement visually in Magic. Pins are equally spaced after the initial floorplan and visible using left/right mouse actions.
![PLACEHOLDER: screenshot/terminal-output/schematic]

### 3. Change IO Pin Placement Strategy

- Enter the OpenLane configuration folder:

```bash
cd <openlane_project>/configuration
```
![PLACEHOLDER: screenshot/terminal-output/schematic]

- Identify the IO placer strategy variable (usually `FP_IO_MODEL`) within the relevant floorplan configuration file.

```bash
nano floorplan_verilog.tcl
```
![PLACEHOLDER: screenshot/terminal-output/schematic]

- Note the current mode (e.g., `FP_IO_MODEL = 1` for equidistant pins).

- Change the strategy by setting the variable to another supported mode (e.g., 2 for the Hungarian algorithm):

```bash
set FP_IO_MODEL 2
```
![PLACEHOLDER: screenshot/terminal-output/schematic]

### 4. Rerun Floorplan with New Settings

- Run the floorplanning process again using the updated IO placer setting:

```bash
openlane --run-fp
```
*Or use your specific OpenLane CLI command to rerun the floorplan stage in the same run directory.*

![PLACEHOLDER: screenshot/terminal-output/schematic]

- Wait for completion.

### 5. Verify Updated Pin Configuration

- Return to the results folder for the floorplan.

```bash
cd <openlane_project>/<latest_run>/results/floorplan
magic -T <your_tech_file> <your_layout_file>.def
```
![PLACEHOLDER: screenshot/terminal-output/schematic]

- Optionally, check file timestamps to confirm updates.

```bash
ls -l
```
![PLACEHOLDER: screenshot/terminal-output/schematic]

- Inspect the new pin configuration in Magic; pins should no longer be equidistant—now stacked per the selected algorithm (e.g., Hungarian).
![PLACEHOLDER: screenshot/terminal-output/schematic]

### 6. Reset and Iterate as Needed

- Any time you need to update configuration:
  - Reset the variable value.
  - Rerun the corresponding OpenLane flow.

### 7. Preparing for Cell Characterization

- The next phase is focused on **cell characterization** (inverter as the example), to measure parameters:
  - Rise time ($t_{rise}$)
  - Fall time ($t_{fall}$)
  - Propagation delay ($t_{pd}$)
  - Rise propagation delay ($t_{pr}$)
  - Fall propagation delay ($t_{pf}$)

  *(Equations and detailed characterization will be conducted in following steps or videos. No commands in this step from the transcript.)*

### 8. Setup for Parallel Work

- Open a parallel terminal for characterization work in the OpenLane root directory.

```bash
cd <openlane_project>
```
![PLACEHOLDER: screenshot/terminal-output/schematic]

---

*Continue cell simulation and characterization from this point as directed in subsequent sessions.*
