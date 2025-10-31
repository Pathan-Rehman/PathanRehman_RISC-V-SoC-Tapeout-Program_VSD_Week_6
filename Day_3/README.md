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
<img width="736" height="267" alt="image" src="https://github.com/user-attachments/assets/2e331de8-71b2-482c-905c-8f5127a141de" />

- Locate and open the `.def` file to check the current pin configuration.

```bash
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &
```
<img width="1052" height="786" alt="image" src="https://github.com/user-attachments/assets/1b2f7ba1-3ad7-4343-ab4d-4e3058b1f76b" />


  - Alternatively, view or copy the configuration from `.def` into a simpler editor (e.g., Notepad).

### 2. Read Current Pin Placement

- Use the LEF file for current pin data. If necessary, navigate up and into the temp directory to read the merged LEF.

```bash
cd ../../temp
magic lef read merged.tab.lef
```
<img width="1085" height="841" alt="image" src="https://github.com/user-attachments/assets/f72c8b7f-bdfd-45cb-b76c-3ac3f7985d13" />


- Check the pin arrangement visually in Magic. Pins are equally spaced after the initial floorplan and visible using left/right mouse actions.
<img width="486" height="643" alt="image" src="https://github.com/user-attachments/assets/7dafdc3b-cfd0-4f9c-ba4a-f61bc3520815" />


### 3. Change IO Pin Placement Strategy

- Enter the OpenLane configuration folder:

```bash
cd <openlane_project>/configuration
```

- Identify the IO placer strategy variable (usually `FP_IO_MODEL`) within the relevant floorplan configuration file.

```bash
gvim floorplan.tcl
```
<img width="964" height="773" alt="image" src="https://github.com/user-attachments/assets/1de803dc-8e15-4340-a424-52a12e7955b0" />

- Note the current mode (e.g., `FP_IO_MODEL = 1` for equidistant pins).

- Change the strategy by setting the variable to another supported mode (e.g., 2 for the Hungarian algorithm):

```bash
set ::env(FP_IO_MODE) 2
```
<img width="734" height="514" alt="image" src="https://github.com/user-attachments/assets/a2780a50-5cba-4851-81db-130cbb737db2" />


### 4. Rerun Floorplan with New Settings

- Run the floorplanning process again using the updated IO placer setting:

```bash
run_floorplan
```
*Or use your specific OpenLane CLI command to rerun the floorplan stage in the same run directory.*

<img width="731" height="521" alt="image" src="https://github.com/user-attachments/assets/dc6b2efa-b6c0-463a-86fe-45c0e5c0843b" />


- Wait for completion.

### 5. Verify Updated Pin Configuration

- Return to the results folder for the floorplan.

```bash
cd <openlane_project>/<latest_run>/results/floorplan
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &
```
<img width="862" height="678" alt="image" src="https://github.com/user-attachments/assets/357ac8d6-8813-45b7-a159-a9bd5306f741" />


- Optionally, check file timestamps to confirm updates.

```bash
ls -l
```
<img width="739" height="393" alt="image" src="https://github.com/user-attachments/assets/292dfd3e-3c9c-4898-96da-e2872f65c2f1" />


- Inspect the new pin configuration in Magic; pins should no longer be equidistant—now stacked per the selected algorithm (e.g., Hungarian).

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

