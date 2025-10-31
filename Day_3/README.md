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

This documentation explains the theoretical principles and connectivity behind the SPICE deck for a **CMOS inverter**. It covers the structure and roles of PMOS and NMOS transistors within the inverter, the connection of typical components such as load capacitors and voltage sources, the concept and purpose of voltage transfer characteristics, and the significance of transistor sizing (width and length) in determining inverter behavior.

<img width="916" height="451" alt="image" src="https://github.com/user-attachments/assets/0c2e9aaa-35a5-420f-b210-fc43b62884c5" />


## Core Concepts

### CMOS Inverter Structure

A CMOS inverter consists of a PMOS transistor and an NMOS transistor, connected in series between the supply voltage ($V_{DD}$) and ground. The output is taken from the common connection point between the PMOS drain and NMOS drain. The gate terminals of both transistors are tied together as the input. The substrate (body) connections for PMOS and NMOS are typically connected to $V_{DD}$ and ground, respectively.

<img width="404" height="363" alt="image" src="https://github.com/user-attachments/assets/09046795-fa52-4c43-b9d7-65b53507e99f" />


### Signal Nodes and Connectivity

- **PMOS Connections**:
  - Drain: Connected to the output node
  - Gate: Connected to input node
  - Source/Substrate: Connected to $V_{DD}$
- **NMOS Connections**:
  - Drain: Connected to output node
  - Gate: Connected to input node
  - Source/Substrate: Connected to ground (node zero)

This specific arrangement produces inverting logic behavior: high input yields low output, and vice versa.


### Load Capacitance and Input/Output Voltages

The output node is typically loaded with a capacitor to simulate real circuit loads. The load capacitor ($C_{load}$) is connected between the output and ground and influences the dynamic response (slew rate, delay) of the inverter. The input and supply voltages are specified to establish operating conditions, commonly tying them to reference values, such as 2.5V.


### Voltage Transfer Characteristics (VTC)

The **voltage transfer characteristics** plot the output voltage ($V_{out}$) against the swept input voltage ($V_{in}$). This curve reveals the inverter's switching threshold and defines its digital transition region. To generate VTC:
- Sweep $V_{in}$ from $0 \text{ V}$ to $V_{DD}$ (e.g., $2.5 \text{ V}$), in defined steps (e.g., $0.05 \text{ V}$).
- Measure $V_{out}$ for each $V_{in}$.

This process is essential for analyzing inverter behavior, noise margins, and switching performance.


### SPICE Model Files

SPICE model files encapsulate the parameters governing transistor behavior. For each device type (NMOS and PMOS), the model includes critical process-dependent attributes such as:
- Oxide thickness ($t_{ox}$)
- Threshold voltage ($V_{th}$)
- Channel dimensions (Width $W$, Length $L$)
- Mobility and process coefficients ($K_1$, $K_2$, $K_3$, etc.)

These parameters reflect the fabrication technology (e.g., 250nm process), and are referenced by model names such as 'NMOS' and 'PMOS'.

### Sizing and W/L Ratio

The inverter's static and dynamic behavior is strongly affected by the dimensions of the PMOS and NMOS channels:
- $W$: Channel width
- $L$: Channel length
- $W/L$: Ratio influencing drive strength

For balanced operation, designers may set $W_{PMOS}/L_{PMOS} = W_{NMOS}/L_{NMOS}$, but commonly $W_{PMOS}$ is increased relative to $W_{NMOS}$ (e.g., 2.5$\times$) to account for lower carrier mobility in PMOS, shifting the switching point and improving symmetric voltage transfer.

Example calculation:
$W_{PMOS} = 2.5 \times W_{NMOS}$

<img width="739" height="121" alt="image" src="https://github.com/user-attachments/assets/5419cc30-abc6-4dcf-bf12-1fc79701cc7a" />
<img width="529" height="333" alt="image" src="https://github.com/user-attachments/assets/0a9695e5-4854-4b8c-97a2-268836027b12" />
<img width="387" height="236" alt="image" src="https://github.com/user-attachments/assets/31b13349-1fbb-4efa-bd86-81f0a2355cd4" />
<img width="686" height="481" alt="image" src="https://github.com/user-attachments/assets/ce2845d9-583e-4937-bf0c-db077836bad1" />

## Key Points

- **CMOS inverter operation relies on complementary switching of PMOS and NMOS:** high input turns off PMOS and turns on NMOS, pulling output low; low input turns on PMOS and turns off NMOS, pulling output high.
- **Transistor connectivity defines logic inversion:** drain connected to output, gates to input, sources/substrates to supply and ground.
- **Load capacitance accurately reflects output dynamics and is essential for transfer characteristic analysis.**
- **SPICE simulation involves sweeping $V_{in}$ to observe $V_{out}$:** $V_{in}$ is incremented in steps, output voltage is recorded, and VTC plotted.
- **Transistor sizing (W/L ratio) is tuned for optimal inverter switching performance:** A wider PMOS typically shifts VTC for improved noise margins and balanced output.
- **SPICE model files embed all key process parameters, defining detailed device behavior for accurate simulation.**

---
# Switching Threshold Vm

This documentation focuses on the **theoretical analysis of SPICE simulations for CMOS inverters**, particularly examining the impact of transistor sizing (PMOS vs NMOS) and the concept of **switching threshold**. Two key scenarios are considered: one where PMOS and NMOS have equal width-to-length (W/L) ratios, and another where PMOS is sized 2.5 times larger than NMOS. The analysis demonstrates the robustness of CMOS logic and explores how device sizing affects inverter characteristics such as switching threshold and current flow.

<img width="786" height="511" alt="image" src="https://github.com/user-attachments/assets/029c5bd5-a094-4760-b675-08a889ee09fb" />


## Core Concepts

### CMOS Inverter Sizing

- **Device Sizing Scenarios:**
  - **Equal W/L Ratios:** PMOS and NMOS have identical width and length, resulting in identical W/L ratios.
  - **Larger PMOS:** PMOS width is increased to be 2.5 times the NMOS width, while lengths remain equal. This changes the PMOS W/L ratio relative to NMOS.

- **Calculation of W/L Ratios:**
  - For NMOS: $W_{N} / L = 0.375 / 0.25 = 1.5$
  - For PMOS: $W_{P} / L = 1.9375 / 0.25 = 7.75$ (where $W_P = 2.5 \times W_N$)

- **Significance:** Different W/L ratios are chosen to contrast SPICE simulation results and highlight how sizing affects inverter performance.

<img width="900" height="453" alt="image" src="https://github.com/user-attachments/assets/42ae3330-16fc-44b0-b33e-85e6c4fdbea4" />

### Robustness of CMOS Inverter

- **Waveform Similarity:** The qualitative shapes of output waveforms for both sizing cases are almost identical despite minor glitches. The essential switching behavior is preserved across different device sizes.

- **Key Feature:** CMOS inverters maintain fundamental logic—when input is 0, output is high, and when input is high, output is low—across a broad range of sizing configurations.

- **Application:** This intrinsic robustness is a principal reason CMOS is widely used for digital logic design.

<img width="895" height="446" alt="image" src="https://github.com/user-attachments/assets/1a262c79-d320-463c-b13a-523b979183a8" />


### Switching Threshold

- **Definition:** The switching threshold is the input voltage ($V_{M}$) where the output voltage equals the input voltage ($V_{in} = V_{out}$).
  
- **Geometric Interpretation:** On the voltage transfer curve, the threshold is found at the intersection point where the output equals the input, typically visualized by drawing a 45° line ($V_{out} = V_{in}$).

- **Typical Observations:**
  - For equal W/L: Switching threshold found near 0.98 V.
  - For larger PMOS: Switching threshold shifts upward, near 1.2 V (not centered), reflecting the impact of width ratio on inverter symmetry.

- **Critical Region:** At the switching threshold, both PMOS and NMOS operate in the *saturation region* and are essentially turned on.

<img width="893" height="465" alt="image" src="https://github.com/user-attachments/assets/75062773-0ad8-4104-9ed3-e21b8e309daf" />
<img width="900" height="475" alt="image" src="https://github.com/user-attachments/assets/7c4991e7-fda1-4e40-b417-a3c949ef9573" />

### Saturation Region and Current Flow

- **Saturation Region:** At threshold, the gate-source voltage ($V_{GS}$) substantially exceeds the threshold voltage, driving both transistors into saturation.
  
- **Current Behavior:**
  - Both transistors conduct, potentially causing substantial current from power to ground (short-circuit condition).
  - Drain currents have inverse directions:
    - PMOS: $I_{DSP}$
    - NMOS: $I_{DSN}$
  - At the threshold point: $I_{DSP} = -I_{DSN}$

- **Equations:**
  - Operating point condition: $V_{GS} \gg V_{TH}$
  - Threshold operation: $V_{GS} = V_{DS}$

<img width="803" height="491" alt="image" src="https://github.com/user-attachments/assets/69dc1cf0-93fe-46e5-a138-3fc9979ecee7" />


### Analytical Relationships

- **Threshold Calculation:**
  - Knowing $W_N / L$ and $W_P / L$, the switching threshold $V_M$ can be analytically derived.
  - Conversely, to set a desired $V_M$, appropriate values for device sizing can be chosen.

- **Physical Significance:** Control over threshold by sizing enables optimization for different applications (speed, power, noise margin).

<img width="882" height="513" alt="image" src="https://github.com/user-attachments/assets/a1358267-53ce-4dc7-99c9-2255f55c2bcd" />


## Key Points

- **CMOS inverters remain robust across diverse sizing choices.**
- **Switching threshold ($V_M$) is a critical parameter determined by transistor sizing; it can be tuned via $W/L$ ratios.**
- **At threshold, both PMOS and NMOS are in saturation, creating a region of potentially high current flow.**
- **The direction and magnitude of currents in PMOS and NMOS are opposite but equal at the switching point ($I_{DSP} = -I_{DSN}$).**
- **Threshold and robustness analysis are foundational for optimizing logic gate design in physical circuits.**

---
# Static and dynamic simulation of CMOS inverter

This section explores the concept of **switching threshold** in CMOS inverters, its robustness against variations in PMOS/NMOS sizing, and fundamental methods to analyze delay characteristics using theoretical and simulation-driven approaches. The importance of the switching threshold, the construction and interpretation of DC transfer characteristics, and the core methodology behind transient (dynamic) analysis are covered.

<img width="869" height="474" alt="image" src="https://github.com/user-attachments/assets/f24f5a4f-3120-4adf-8898-5a86749041e9" />


## Core Concepts

### Switching Threshold in CMOS Inverters

- The **switching threshold** is the input voltage at which the output voltage of a CMOS inverter transitions between logic levels (high-to-low or low-to-high).
- At this threshold, the voltage at the input equals the voltage at the output: $V_{in} = V_{out}$.
- A common graphical technique to find the switching threshold is to plot the DC transfer characteristic and superimpose a $45^\circ$ line (i.e., $V_{in} = V_{out}$). The intersection point gives the threshold voltage.
- The robustness of the threshold refers to how stable this value remains when PMOS or NMOS transistor dimensions are changed.
- In device terms, the **switching threshold** influences noise margins and affects the overall reliability of digital logic circuits.


### DC Transfer Characteristics

- The **DC transfer characteristic** of a CMOS inverter plots output voltage ($V_{out}$) versus input voltage ($V_{in}$) under steady-state (DC) conditions.
- This curve typically shows a sharp transition from $V_{DD}$ (logic high) to $0$ V (logic low), centered around the switching threshold.
- The slope of this curve at the switching threshold is an important metric, as it relates to the inverter's noise immunity.
- Comparing how this characteristic shifts as PMOS and NMOS sizes are changed provides insight into inverter robustness and balancing.

<img width="580" height="469" alt="image" src="https://github.com/user-attachments/assets/eebbdde4-deae-4543-9c19-19818f681712" />


### Delay in CMOS Inverters

- **Delay** in a CMOS inverter refers to the time taken for the output to respond to changes in the input.
- Two important types:
  - **Rise Delay ($t_{PLH}$):** Time taken for the output to rise from low to high (usually measured at the $50\%$ points).
  - **Fall Delay ($t_{PHL}$):** Time taken for the output to fall from high to low (also at the $50\%$ points).
- Delay is determined by the intrinsic device capacitance, the drive strength (dependent on PMOS/NMOS sizing), and the load being switched.
- Delay values can be extracted by noting the time difference between the input and output waveforms crossing the $50\%$ voltage level in transient simulation.

$delay = t_{out,50\%} - t_{in,50\%}$

<img width="565" height="413" alt="image" src="https://github.com/user-attachments/assets/5f2ad26b-966b-463d-89c1-d33761b57228" />
<img width="707" height="498" alt="image" src="https://github.com/user-attachments/assets/e0660434-245f-4038-a097-87cfa1b62813" />


### Transient (Dynamic) Analysis

- **Transient analysis** studies circuit response over time to dynamic input conditions, such as a pulse or step.
- In simulations, an input pulse (often with specified rise and fall times, width, and period) is applied, and the inverter’s output is monitored.
- Key parameters for the input pulse:
  - **Rise time ($t_{rise}$)**
  - **Fall time ($t_{fall}$)**
  - **Pulse width**
  - **Period**

These parameters shape how quickly the input transitions and thus affect observed timing results.

- The transient simulation records output voltage over time, allowing measurement of **propagation delays**.

<img width="923" height="515" alt="image" src="https://github.com/user-attachments/assets/aa4eccc2-b9e9-44b1-838d-c541656b1467" />


## Key Points

- **Switching Threshold**: The input voltage where $V_{in} = V_{out}$; found using the intersection of the DC transfer curve and the $45^\circ$ line.
- **Robustness Assessment**: By sweeping PMOS/NMOS widths, the switching threshold’s stability gives insight into circuit robustness against sizing variations.
- **Delay Measurement**: Propagation delays are computed as the difference in time stamps where input and output signals cross $50\%$ of their logic swing.
- **Dynamic Simulation**: Transient analysis enables direct visualization of timing behavior (rise/fall delays) in response to realistic input pulses.
- **Importance for Design**: Both threshold and delay are central to noise margins, signal integrity, and overall digital performance in open-source hardware.

---
# Lab steps to git clone vsdstdcelldesign

This lab guides you through using a custom inverter cell within the OpenLane design workflow. The aim is to clone a dedicated GitHub repository containing the necessary inverter files and SKY130 model libraries, configure your environment, set up the technology file, and visualize the inverter layout with Magic. Each step and command is documented to give a reproducible workflow for open-source hardware design.

## Lab Steps

### 1. Clone the Custom Repository

<img width="1919" height="870" alt="image" src="https://github.com/user-attachments/assets/dfdf3795-94e4-4bb4-bb77-de501de96c89" />


Begin by cloning the provided GitHub repository, which contains the custom inverter designs and necessary model libraries.

```sh
git clone https://github.com/nickson-jose/vsdstdcelldesign
```
<img width="737" height="252" alt="image" src="https://github.com/user-attachments/assets/75481f31-6016-4540-8f86-66c37a051e73" />


This creates a folder named `vsdstdcelldesign` in your current working directory.

### 2. Verify Repository Contents

Navigate into the cloned directory and inspect the files to ensure all resources, including Mac files and model libraries, are present.

```sh
cd vsdstdcelldesign
ls
```
<img width="737" height="332" alt="image" src="https://github.com/user-attachments/assets/8326955e-7536-4a07-8bde-282a453aace1" />


You should see inverter `.mag` files, library folders, and model files similar to what you saw in the GitHub web interface.

### 3. Locate the Magic Tech File

Before opening layout files, ensure you have the appropriate Magic technology file (`.tech`) in your working directory. The file is typically inside another project or PDK folder.

Navigate to the directory containing the tech file (replace paths as appropriate):

```sh
cd /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic
ls *.tech
```
<img width="733" height="268" alt="image" src="https://github.com/user-attachments/assets/da87576c-ef95-4790-a340-bf6697e6797d" />


Identify the required technology file, e.g., `sky130A.tech`.

### 4. Copy the Magic Tech File

Copy the required tech file from its location into your current project directory.

```sh
cp /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign
```
<img width="730" height="164" alt="image" src="https://github.com/user-attachments/assets/521fcb25-ff1b-4146-a391-f4b4fb30f4fe" />


Verify that the tech file is now in your working directory:

```sh
ls
```

Ensure you see `sky130A.tech` listed.

### 5. Open the Inverter Layout in Magic

Launch Magic using the local tech file and open the inverter schematic or layout file (`.mag`). The `&` at the end of the command frees the terminal for additional commands.

```sh
magic -T sky130A.tech sky130_inv.mag &
```
- Replace `sky130_inv.mag` with your inverter’s `.mag` file if it has a different name.

<img width="977" height="642" alt="image" src="https://github.com/user-attachments/assets/37771079-39e6-4a4e-9b21-9c8212bc21f3" />


### 6. Explore the Layout in Magic

Once Magic opens:

- Move your cursor to the layout window.
- Use the `s` key to select.
- Use the `v` key to centralize or zoom into the selected area.

<img width="866" height="680" alt="image" src="https://github.com/user-attachments/assets/75f05b25-ea13-417d-ad58-5fc6784cb22f" />

You should see the layout for your custom inverter, ready for further actions like extraction or post-layout simulation.

---
# Inception of Layout – CMOS fabrication process

# Create Active regions

This section introduces the theoretical basis for the 16-mask (16M) process in semiconductor manufacturing, focusing on **substrate selection**, **substrate doping and resistivity**, the **formation of active regions**, and the critical concepts of **isolation using LOCOS (Local Oxidation of Silicon)**. Core fabrication ideas—including the use of materials like silicon dioxide, silicon nitride, and photoresist—are explained, with an emphasis on the underlying principles and reasons for each step. No implementation or procedural code is included.

---

## Core Concepts

### Substrate Selection

The fabrication process begins with choosing a **substrate**, which serves as the foundational material upon which the entire integrated circuit is built. The most common substrate in modern semiconductor devices is **P-type silicon**, characterized by its specific electrical and physical properties:
- **High resistivity**: Determines how easily electric currents can pass through the material.
- **Doping level**: Defined as the concentration of impurity atoms deliberately introduced to control the substrate’s electrical behavior. For P-type silicon, a typical doping concentration is $10^{15}$ atoms per cubic centimeter.
- **Crystal orientation**: A critical parameter that defines the atomic arrangement on the surface, affecting further fabrication steps and device performance.

The choice of doping level ensures that the substrate's doping is lower than that used subsequently for well formation (to maintain electrical separation between wells and substrate).

<img width="867" height="452" alt="image" src="https://github.com/user-attachments/assets/c5082078-8f48-4a66-a3c3-8fefc4a9d4cf" />

---

### Doping

**Doping** is the intentional introduction of impurity atoms (dopants) into the silicon substrate to modify its electrical properties. For P-type silicon, this means introducing elements (like boron) that create “holes” (positive charge carriers) in the lattice.
- The substrate doping level must remain **less than the well doping** for proper device isolation in later steps.

<img width="949" height="454" alt="image" src="https://github.com/user-attachments/assets/9d75f0cc-2a40-4773-b401-f54062038760" />

---

### Formation of Active Regions

Active regions are defined areas in the substrate where transistors (such as PMOS and NMOS) will be fabricated. These regions are isolated from each other to prevent electrical interference:

- **Active regions:** Physically distinct pockets within the substrate, reserved for building individual transistors.
- **Purpose of isolation:** Prevents electrical crosstalk between transistors, ensuring correct circuit functionality.

Creating these regions involves patterning and masking techniques to control where fabrication steps affect the substrate.

<img width="922" height="452" alt="image" src="https://github.com/user-attachments/assets/34ccfe7f-b32c-446a-abe3-b4a0fa87ce0e" />

---

### Isolation Using Silicon Dioxide and Silicon Nitride

To isolate active regions, layers of insulating materials are grown/deposited:

1. **Silicon Dioxide Layer**: Grown thermally on the substrate (e.g., 40 nanometers thick). Acts as an **electrical insulator**.
2. **Silicon Nitride Layer**: Deposited on top of the silicon dioxide (e.g., 80 nanometers thick). Serves as a **protection and masking layer** in following steps.

These layers together prepare the substrate for precise patterning and selective oxidation.

<img width="943" height="450" alt="image" src="https://github.com/user-attachments/assets/733cd142-8b90-40cc-aee9-ce08f30e1f19" />

---

### Photolithography and Photoresist

**Photolithography** is a key patterning process relying on a light-sensitive material called **photoresist**:

- **Photoresist**: A polymer film deposited (e.g., 1 micron thick) and then exposed to ultraviolet (UV) light through a patterned mask.
    - **Masks**: Define where the light hits and therefore where subsequent etching or processing occurs.
    - **Exposure/Reactions**: UV light exposure causes chemical changes in the photoresist, so only specific regions are affected during subsequent development (washing/etching).

- **Protected vs. Exposed Areas**: The mask shields some regions from light (thus “protecting” them), while others remain exposed and can be chemically altered.

This process precisely transfers patterns to the substrate, outlining where wells and active regions will form.

<img width="940" height="448" alt="image" src="https://github.com/user-attachments/assets/b4df2736-bb4a-4360-ab4d-e9576fce1a14" />

---

### Etching and Removal

After photolithography:
- **Etching**: Selectively removes the silicon nitride only from areas not protected by photoresist, exposing the underlying silicon dioxide.
- **Photoresist Removal**: Eliminates the remaining resist, leaving the protected silicon nitride to act as a durable mask during the next oxidation step.

This step ensures only targeted regions are exposed to further processing.

<img width="948" height="458" alt="image" src="https://github.com/user-attachments/assets/84476f94-4eb9-47ad-90c7-1887d3a6b2d0" />
<img width="934" height="380" alt="image" src="https://github.com/user-attachments/assets/847c4d9b-39c5-407a-be30-1db211a119a0" />
<img width="923" height="377" alt="image" src="https://github.com/user-attachments/assets/28122334-3a3b-4d07-b2ef-4b490989d023" />

---

### Local Oxidation of Silicon (LOCOS)

**LOCOS** is a critical technique for creating precise isolation between transistors:
- The wafer is exposed to high-temperature oxidation, growing a thick field oxide ($SiO_2$) only in unmasked areas.
- Regions under the silicon nitride mask remain protected, inhibiting oxide growth and thus defining the “active regions”.

A unique geometric effect called **“bird’s beak”** occurs at the edge of growing oxides, slightly intruding under the mask.

**Electrical Isolation**: The resulting thick oxide physically and electrically separates neighboring transistor regions to prevent leakage or crosstalk.

- **Field Regions**: With thick oxide, function as insulators.
- **Active Regions**: Protected from oxidation, reserved for transistor fabrication.

<img width="947" height="450" alt="image" src="https://github.com/user-attachments/assets/d71c96d4-8b98-48ad-b273-72b8fb873119" />
<img width="944" height="447" alt="image" src="https://github.com/user-attachments/assets/643b110a-e8cd-4ccd-9d41-896e56f1e3d8" />
<img width="930" height="372" alt="image" src="https://github.com/user-attachments/assets/1f418aad-31ca-480f-af99-723f1388a41f" />

---

## Key Points

- The **substrate** is almost always **P-type silicon** due to its favorable electrical properties and compatibility with modern device fabrication.
- **Doping concentration** for the substrate is commonly set at $10^{15}\ \text{cm}^{-3}$ to ensure optimal separation from higher-doped well regions.
- **Photolithography** and **masking** techniques enable precise patterning of the substrate for subsequent processing.
- **Isolation** between transistors is achieved by LOCOS, which grows thick oxide in designated areas, leaving others undisturbed for device fabrication.
- The term **"bird’s beak"** refers to the encroachment of the oxidation front at the boundary of the silicon nitride mask, a byproduct of the LOCOS process.
- **Successful electrical devices** critically depend on the integrity of these isolation and patterning techniques to ensure individual transistor performance and prevent undesired interaction.
- The development of **active and isolation regions** sets the foundation for all subsequent steps in the 16M process, including well formation for PMOS and NMOS transistors.
---
# 
