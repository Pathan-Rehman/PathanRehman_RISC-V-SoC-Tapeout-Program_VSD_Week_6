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
---
# SPICE deck creation for CMOS inverter

This documentation explains the theoretical foundation behind voltage transfer characteristics in CMOS circuits and the foundational concepts of SPICE simulation as applied to CMOS inverters. It covers the symbolism and connectivity requirements for PMOS and NMOS devices, the reasoning behind component sizing, the significance of the load capacitor, and the definition and necessity of nodes within circuit netlists used by SPICE simulators.

## Core Concepts

### Voltage Transfer Characteristics of CMOS

In CMOS circuits, the *voltage transfer characteristic* describes how the output voltage responds as the input voltage changes. This relationship is key to understanding how CMOS inverters switch states and operate in digital logic.

<img width="916" height="432" alt="image" src="https://github.com/user-attachments/assets/b978c92c-742e-4bb2-80d6-054e10ed4c88" />


### SPICE Simulation Overview

**SPICE** (Simulation Program with Integrated Circuit Emphasis) simulators use mathematical models to approximate the electrical behavior of actual devices. Each component in a simulation (such as PMOS, NMOS, resistors, or capacitors) is defined by a mathematical model that captures its essential electrical characteristics. Simulators leverage these models to compute expected circuit behavior under specified conditions.

<img width="920" height="448" alt="image" src="https://github.com/user-attachments/assets/3b429e4c-6604-493b-8623-86aef7b46e3b" />


### Netlist and Connectivity

A SPICE simulation is based on a *netlist*—a textual description detailing all circuit components, their interconnections, and the configuration of the simulation (e.g., stimulus, output points). The netlist must communicate:
- The types and models of each device (e.g., PMOS, NMOS)
- The specific circuit instantiation parameters (width, length, etc.)
- All nodes (junctions where two or more components connect)
- Input signals, supply voltages, and outputs

The connectivity of the *substrate* terminal for MOSFETs must be explicitly defined, as it influences electrical characteristics like the threshold voltage.

<img width="499" height="147" alt="image" src="https://github.com/user-attachments/assets/411c8f5a-9815-475c-b449-53829b750cac" />


### Device Symbols and Substrate Pin

The PMOS symbol typically features an arrow pointing outward, while the NMOS symbol points inward. This arrow direction encodes the current/conventional flow and device type. The substrate pin is a key terminal that connects to either the highest or lowest potential in the circuit (VDD for PMOS, VSS for NMOS), and its voltage can alter device behavior by changing the threshold voltage.

<img width="149" height="316" alt="image" src="https://github.com/user-attachments/assets/7832dfa9-c27f-43c4-81b5-24096c004db8" />


### Component Sizing in CMOS Inverters

Sourcing and sizing:
- The *channel length* ($L$) and *channel width* ($W$) determine the current-driving capability.
- Typical values might be $W/L = 0.375/0.25$ for both PMOS and NMOS, but in standard practice, the PMOS is often made wider (e.g., $W_{PMOS} = 2 \times W_{NMOS}$) to balance the drive strength between p-channel and n-channel devices due to carrier mobility differences.
- $W$ and $L$ are typically given in microns or nanometers, reflecting fabrication technology.

For instance, $W = 375$ nm and $L = 250$ nm for both NMOS and PMOS in a demonstration, but more balanced inverters typically use a $W_{PMOS}$ twice or thrice that of the NMOS.

<img width="409" height="333" alt="image" src="https://github.com/user-attachments/assets/ed72986d-0e40-4201-af9e-ea8a74eb28d9" />


### Load Capacitance

The *output load capacitor* represents the capacitance seen at the inverter output. Its value is crucial for determining dynamic response and must be chosen based on the aggregate input capacitance of the driven circuitry. The value (e.g., 10 femtofarads) impacts how quickly the output can change state.

This capacitance is typically modeled as a lumped element, but in complex analysis, it derives from downstream gate capacitance and interconnect.

<img width="162" height="164" alt="image" src="https://github.com/user-attachments/assets/22528b14-56c1-4943-9116-5031172c4fd8" />


### Voltage Supply Scaling

The choice of supply voltage ($V_{DD}$) is often proportional to technology scaling. For example, a channel length of 250 nm may correspond to a $V_{DD}$ value of 2.5 V; smaller channel lengths would demand proportionally smaller supply voltages to ensure device reliability and optimal operation.

The input gate voltage is typically swept across the supply range during simulations to observe the inverter response.

<img width="119" height="301" alt="image" src="https://github.com/user-attachments/assets/b73ddda0-9b61-4c94-87e2-d5e55964f4c4" />


### Nodes and Node Naming

A *node* is a point where device terminals connect—each component is defined between two or more named nodes. In SPICE, proper node identification is essential, as components are referenced in terms of their connecting nodes (e.g., IN, OUT, VDD, 0).

For example, the output load capacitor would be defined as connected between the nodes OUT and 0 (ground).


## Key Points

- **SPICE Simulators** simulate electrical circuits using mathematical models attached to each component. These models approximate real-world device behavior.
- **Netlist construction** is the essential first step in simulation, encoding every component, connection, node, and necessary parameter.
- **PMOS and NMOS symbols** differ in arrow direction—the arrow out for PMOS, in for NMOS—encoding device type and current direction.
- **Substrate connection** must be specified for every MOSFET, as the substrate (or body) determines threshold voltage and can affect device behavior.
- **Component sizing** (i.e., width and length of MOSFETs) determines electrical strength. Typically, PMOS is sized wider than NMOS to balance drive strengths due to mobility differences.
- **Load capacitance** is fundamental in digital circuits, representing the output node’s total capacitance. While sometimes assumed for simplicity (e.g., 10 fF), it typically requires calculation from circuit topology and is crucial for timing analysis.
- **Voltage scaling** is tied to technology size; smaller geometries use lower supply voltages, commonly scaling $V_{DD}$ with channel length.
- **Nodes** define the connectivity in SPICE models. Each part must clearly indicate which nodes (IN, OUT, VDD, 0, etc.) it connects between. Consistent naming ensures correct simulation behavior.

---
# SPICE simulation lab for CMOS inverter
