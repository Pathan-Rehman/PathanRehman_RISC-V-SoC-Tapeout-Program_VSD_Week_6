# Table of contents

- [Labs for CMOS inverter ngspice simulations](#labs-for-cmos-inverter-ngspice-simulations)
- [IO placer revision](#io-placer-revision)
- [SPICE deck creation for CMOS inverter](#spice-deck-creation-for-cmos-inverter)
- [SPICE simulation lab for CMOS inverter](#spice-simulation-lab-for-cmos-inverter)
- [Switching Threshold Vm](#switching-threshold-vm)
- [Static and dynamic simulation of CMOS inverter](#static-and-dynamic-simulation-of-cmos-inverter)
- [Lab steps to git clone vsdstdcelldesign](#lab-steps-to-git-clone-vsdstdcelldesign)
- [Inception of Layout – CMOS fabrication process](#inception-of-layout--cmos-fabrication-process)
- [Create Active regions](#create-active-regions)
- [Formation of N-well and P-well](#formation-of-n-well-and-p-well)
- [Formation of gate terminal](#formation-of-gate-terminal)
- [Lightly doped drain (LDD) formation](#lightly-doped-drain-ldd-formation)
- [Source – drain formation](#source--drain-formation)
- [Local interconnect formation](#local-interconnect-formation)
- [Higher level metal formation](#higher-level-metal-formation)
- [Lab introduction to Sky130 basic layers layout and LEF using inverter](#lab-introduction-to-sky130-basic-layers-layout-and-lef-using-inverter)
- [Lab steps to create std cell layout and extract spice netlist](#lab-steps-to-create-std-cell-layout-and-extract-spice-netlist)
- [Sky130 Tech File Labs](#sky130-tech-file-labs)
- [Lab steps to create final SPICE deck using Sky130 tech](#lab-steps-to-create-final-spice-deck-using-sky130-tech)
- [Lab steps to characterize inverter using sky130 model files](#lab-steps-to-characterize-inverter-using-sky130-model-files)
- [Lab introduction to Magic tool options and DRC rules](#lab-introduction-to-magic-tool-options-and-drc-rules)
- [Lab introduction to Sky130 pdk's and steps to download labs](#lab-introduction-to-sky130-pdks-and-steps-to-download-labs)
- [Lab introduction to Magic and steps to load Sky130 tech-rules](#lab-introduction-to-magic-and-steps-to-load-sky130-tech-rules)
- [Lab exercise to fix poly.9 error in Sky130 tech-file](#lab-exercise-to-fix-poly9-error-in-sky130-tech-file)
- [Lab exercise to implement poly resistor spacing to diff and tap](#lab-exercise-to-implement-poly-resistor-spacing-to-diff-and-tap)

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
# Formation of N-well and P-well

## Summary

This documentation explains the **theoretical concepts** behind P well and N well (also known as "twin-tub") formation for CMOS fabrication, focusing on layout masks, photolithography, doping via ion implantation, and the role of drive-in diffusion. It emphasizes how selective processing and energy-driven doping create distinct regions for PMOS and NMOS transistors in integrated circuits.


---

## Core Concepts

### Masking and Photolithography

A **mask** is used during fabrication to selectively expose areas of a wafer. The process begins by depositing a photoresist layer, then defining patterns (using a mask) to protect or expose necessary regions. Photolithography allows precise control over which regions undergo further reactions.

- Only the areas exposed to **UV light** through the mask become chemically reactive.
- After exposure, the photoresist in the uncovered areas is removed, revealing regions for wells or active device formation.

<img width="1286" height="638" alt="image" src="https://github.com/user-attachments/assets/2723de45-35f4-4949-abc6-790d2f59c6ce" />
<img width="910" height="798" alt="image" src="https://github.com/user-attachments/assets/762d14d3-3f80-4373-925a-76c03867f1a6" />
<img width="1090" height="679" alt="image" src="https://github.com/user-attachments/assets/3ef6757e-3d71-4765-843e-d6595d85606b" />
<img width="1114" height="578" alt="image" src="https://github.com/user-attachments/assets/0b250851-faf1-4e4f-a778-4d730a3ac03e" />
<img width="1070" height="513" alt="image" src="https://github.com/user-attachments/assets/805b5cd9-4457-4550-bb13-fa47a3508c5a" />


### Wells Formation in CMOS Technology

In CMOS processes, **P wells** are used for fabricating NMOS transistors, and **N wells** (sometimes called anvil or twin-tub regions) are for PMOS transistors. Simultaneous processing is avoided by selectively masking and treating regions individually.

- **Ion Implantation** introduces dopants (boron for P wells, phosphorus for N wells) into the substrate using high-energy particles to penetrate the oxide layer.
  - Boron (P-type) requires energies around $200,000\ \text{eV}$ to diffuse through oxide and form the P well.
  - Phosphorus (N-type) is heavier and requires higher energy for the N well.

- The depth and area of wells are finalized by high-temperature **drive-in diffusion**, typically at $1100^{\circ}\mathrm{C}$ for $4$ to $6$ hours.

<img width="1428" height="680" alt="image" src="https://github.com/user-attachments/assets/2cd997bc-f952-4966-ad73-5f3ce72541c8" />
<img width="1261" height="646" alt="image" src="https://github.com/user-attachments/assets/1d8f25a9-0628-4828-9a4a-0a87ecce80db" />
<img width="1323" height="673" alt="image" src="https://github.com/user-attachments/assets/9b33b4c7-a139-41f7-8443-f82a33952de8" />
<img width="1065" height="546" alt="image" src="https://github.com/user-attachments/assets/f2b567b2-9720-430a-8764-a56fb6c1d9ab" />


#### Twin-Tub Process

The **twin-tub process** forms both N and P wells on the same substrate, enabling fabrication of both NMOS and PMOS transistors side by side. Each "tub" is created by the respective doping process, after which wells are diffused to occupy appropriate regions, providing clear pockets for transistor formation.

<img width="1195" height="689" alt="image" src="https://github.com/user-attachments/assets/25f99560-fb86-4cd9-beb7-f1b5cfcaac42" />


### Gate Region Preparation

Once the wells are defined and diffused, **gate formation** is prepared. The gate is a critical component of MOS transistors, interacting with both source and drain to control current flow. Separate gates are created for NMOS and PMOS devices in subsequent steps.

<img width="1057" height="461" alt="image" src="https://github.com/user-attachments/assets/87e36f81-cd15-44b2-abd7-ac0e2b0817b9" />


---

## Key Points

- **Selective Masking** is essential for separating NMOS and PMOS processes, preventing simultaneous doping in undesired areas.
- **Photoresist and UV exposure** enables controlled creation of active regions on the wafer by removing resist only from exposed areas.
- **Ion implantation energy ($E$)** is crucial. Boron (for P well) and phosphorus (for N well) both require sufficient kinetic energy to penetrate the oxide:
  - Typical implantation energy for boron: $E \approx 200,000\ \text{eV}$
  - Phosphorus, being heavier, needs even higher $E$.
- **Drive-in diffusion** ensures that both wells reach the required depth and occupy substantial substrate area, making room for the full transistor structures.
- The **twin-tub approach** allows simultaneous creation of NMOS and PMOS regions, supporting complete CMOS device integration on a single chip.
- After wells are defined, subsequent steps focus on **gate formation**, which will be detailed in later documentation.

---

# Formation of gate terminal

## Summary

The gate terminal of MOS transistors (NMOS/PMOS) plays a critical role in device operation, as it directly controls the **threshold voltage**, which determines when the transistor switches 'on'. The **threshold voltage** depends on factors such as **doping concentration** and **oxide capacitance**. Precise control over these parameters during fabrication ensures robust transistor performance. The process involves steps to fine-tune doping profiles, oxide characteristics, and polysilicon gate properties, all designed to achieve the desired electrical behavior.


---

## Core Concepts

### Gate Terminal and Threshold Voltage

- The **gate** is the controlling terminal for MOS transistors. By applying a suitable voltage to the gate, one can modulate the underlying channel and thus determine whether current can flow between source and drain.
- The **threshold voltage ($V_{th}$)** is the minimum gate voltage required to turn the transistor 'on'. It is defined by the fundamental equation:

  $$
  V_{th} = V_{FB} + 2\phi_{F} + \gamma\sqrt{2\phi_{F} + V_{SB}}
  $$

  where $\gamma$ (body effect coefficient) is influenced by the **substrate doping ($N_A$)** and **oxide capacitance ($C_{ox}$)**.

<img width="1642" height="900" alt="image" src="https://github.com/user-attachments/assets/bcd7af81-7e26-4016-989d-3be55d5f5b53" />


### Effect of Doping and Oxide Capacitance

- **Doping concentration** in the semiconductor substrate determines the electrical characteristics of the channel and directly affects $\gamma$.
- The **oxide capacitance ($C_{ox}$)** is dependent on the thickness ($t_{ox}$) and permittivity ($\epsilon_{ox}$) of the gate oxide:
  
  $$
  C_{ox} = \frac{\epsilon_{ox}}{t_{ox}}
  $$

- **Threshold voltage tuning** is achieved by carefully selecting the doping profile and gate oxide thickness during fabrication. Any change in these factors will impact $V_{th}$.

<img width="1624" height="844" alt="image" src="https://github.com/user-attachments/assets/48744a96-64ab-4896-9feb-47b0ef7c8726" />

### Controlling Doping Profiles in NMOS and PMOS

- For **NMOS** fabrication, a P-type substrate (or P-well) is selectively doped using **boron**, a P-type dopant. The energy and dose of the boron implantation are optimized so that the dopant penetrates only to the desired depth, ensuring precise control of surface doping and hence $V_{th}$.
- For **PMOS** transistor formation, N-type dopants such as **phosphorus** or **arsenic** are implanted into the N-well. The process is analogous, but with different materials and energy profiles.

<img width="1297" height="621" alt="image" src="https://github.com/user-attachments/assets/bc5ac7fd-b745-4c40-b6e4-c4979b11ddf2" />
<img width="1214" height="681" alt="image" src="https://github.com/user-attachments/assets/3b127b7a-deea-4cdf-b9a1-9a0483ec841e" />
<img width="1164" height="674" alt="image" src="https://github.com/user-attachments/assets/935a3c1b-830b-4f3d-919d-f0b85b3831cb" />
<img width="1193" height="650" alt="image" src="https://github.com/user-attachments/assets/b0037047-c39e-4e57-9149-051f7491be80" />
<img width="1073" height="677" alt="image" src="https://github.com/user-attachments/assets/e4d425ee-98fa-4f77-b3cd-b180b848d2bf" />
<img width="1073" height="678" alt="image" src="https://github.com/user-attachments/assets/44a4f01e-f6f1-4ece-aabb-bc10e729d8a5" />

### Gate Oxide Repair and Regrowth

- Ion implantation steps used for doping often damage the thin gate oxide.
- **Oxide repair** involves removing the damaged oxide using a chemical etchant (typically hydrofluoric acid) and regrowing a high-quality, thin oxide (on the order of 10 nm). The final oxide thickness is tailored to meet target $C_{ox}$ and threshold voltage requirements.

<img width="1428" height="573" alt="image" src="https://github.com/user-attachments/assets/d022af0b-174f-49ba-a688-d760596f783d" />
<img width="1383" height="571" alt="image" src="https://github.com/user-attachments/assets/3728a03a-de43-47c5-9914-8f4012111a95" />
<img width="1319" height="566" alt="image" src="https://github.com/user-attachments/assets/7c4c3574-8c5d-4754-ab01-9d9336982cc4" />


### Polysilicon Gate Deposition and Doping

- The gate electrode itself is formed by depositing a thick **polysilicon** layer (~400 nm).
- To reduce gate resistance, the polysilicon is heavily doped with N-type impurities (phosphorus or arsenic), ensuring low sheet resistance and high performance for fast switching.

<img width="1366" height="591" alt="image" src="https://github.com/user-attachments/assets/c384172f-bdd2-42df-9ce8-d008ac666143" />
<img width="1273" height="696" alt="image" src="https://github.com/user-attachments/assets/8f5e8d07-2674-4cdd-8a6a-f0db6084cb5a" />
<img width="1135" height="693" alt="image" src="https://github.com/user-attachments/assets/23ca0f7e-74d2-4727-8b73-fd7d85cf0880" />
<img width="1101" height="752" alt="image" src="https://github.com/user-attachments/assets/6d786653-82e5-4e28-b8e9-a1d63033d8db" />
<img width="1118" height="619" alt="image" src="https://github.com/user-attachments/assets/f8c1f5b7-c91c-41f6-8e06-254e23410ef3" />
<img width="1078" height="610" alt="image" src="https://github.com/user-attachments/assets/fc9d65bd-a569-4b52-9880-5dd4e4e75985" />
<img width="1077" height="488" alt="image" src="https://github.com/user-attachments/assets/ea4e0b2e-07b4-4adc-a6df-154edd59a7f4" />

---

## Key Points

- **Gate formation** critically determines the electrical properties of MOS transistors. Precise control of doping and oxide parameters ensures consistent threshold voltages for both NMOS and PMOS devices.

- **Threshold voltage ($V_{th}$)** is a function of body effect coefficient ($\gamma$), substrate doping ($N_A$), and oxide capacitance ($C_{ox}$). Key equations:
  - $V_{th} = V_{FB} + 2\phi_{F} + \gamma\sqrt{2\phi_{F} + V_{SB}}$
  - $C_{ox} = \frac{\epsilon_{ox}}{t_{ox}}$

- **Photolithography and masking** are repeated for selective doping, allowing targeted implantation of P-type (boron) for NMOS and N-type (phosphorus, arsenic) for PMOS.
  
- **Chemical etching** (hydrofluoric acid) removes damaged gate oxide, after which a new, high-quality oxide layer is grown to reach controlled thickness and capacitance.

- **Polysilicon gate** deposition creates the physical gate layer, which is subsequently doped to lower resistance, enhancing circuit speed and reliability.

- The described process enables **fine-tuning and control** over device threshold voltages and overall CMOS performance, vital for large-scale integration and high-speed digital circuits.

---

# Lightly doped drain (LDD) formation

## Summary

The Lightly Doped Drain (LDD) formation is a semiconductor device engineering technique primarily used in MOSFETs to manage electric field intensity, minimize hot electron effects, and suppress short channel effects. The LDD region is created by introducing a lightly doped extension between the heavily doped source/drain and the channel. This helps reduce reliability issues associated with device scaling and enhances performance in advanced CMOS fabrication.


## Core Concepts

### Doping Profiles in MOSFETs

- **Source/Drain Doping:**  
  For **PMOS** in an N-well, both source and drain are P-type (`P+`). The LDD region adjacent to these is `P-`, representing the lightly doped drain.  
  For **NMOS** in a P-well, both source and drain are N-type (`N+`), with the LDD region as `N-`.

- **Typical Profile:**  
  The resulting doping profile is either P+P-N (for PMOS) or N+N-P (for NMOS), where '+' indicates a heavily doped region and '-' indicates a lightly doped region.

<img width="1295" height="483" alt="image" src="https://github.com/user-attachments/assets/a427a08e-911f-4ef7-9c5d-18250cd7431f" />


### Purpose of LDD Structure

- **Hot Electron Effect:**  
  As device dimensions shrink, the channel length ($D$) reduces while the supply voltage ($V$) often remains unchanged. The electric field ($E = V/D$) thus increases, leading to excessive carrier energy.  
  These "hot" carriers may:
  - Break SI-SI bonds in the lattice, causing unwanted generation of electron-hole pairs.
  - Gain enough energy to cross the silicon conduction band to the SiO$_2$ band (barrier $\approx$ $3.2~eV$), entering the oxide and worsening device reliability.

<img width="1584" height="263" alt="image" src="https://github.com/user-attachments/assets/204fad61-0edc-4dec-a1b8-2a77653f0eba" />


- **Short Channel Effect:**  
  Reduced channel length allows the drain potential to penetrate into the channel, making it harder for the gate to control the current between source and drain. This deteriorates device behavior at microscopic scales.

<img width="1468" height="221" alt="image" src="https://github.com/user-attachments/assets/a8fb4887-a13f-4678-a883-9c0560fe0ac6" />


## Key Points

- **LDD Engineering:**  
  The LDD region is created by selectively implanting lighter dopants (e.g., phosphorus for N-type, boron for P-type) at carefully chosen doses that prevent deep penetration into the substrate, resulting in `N-` or `P-` regions flanking the channel.

<img width="1274" height="611" alt="image" src="https://github.com/user-attachments/assets/9d787d86-96ef-4983-855f-027785a0da20" />
<img width="1074" height="682" alt="image" src="https://github.com/user-attachments/assets/7cfb089f-c59d-4b51-a026-4c82bfff75a2" />
<img width="1112" height="661" alt="image" src="https://github.com/user-attachments/assets/ed755ea1-b98f-40e6-936d-46d441b98a88" />
<img width="1085" height="499" alt="image" src="https://github.com/user-attachments/assets/449ce5d8-74cc-41b5-a62f-47902d894d51" />


- **Role of Sidewall Spacers:**  
  After forming the LDD implant, sidewall spacers—usually made of SiO$_2$ or Si$_3$N$_4$—are formed using anisotropic (directional) etching.  
  - These spacers protect the lightly doped drain regions during subsequent heavy source/drain implantation, ensuring the LDD profile remains intact.
  - **Anisotropic etching** preserves material along the edges, forming the spacers exclusively on the sidewalls.

<img width="1363" height="574" alt="image" src="https://github.com/user-attachments/assets/e2d13726-b375-4455-b3ef-c5d644320745" />
<img width="1346" height="571" alt="image" src="https://github.com/user-attachments/assets/76b8cac5-20ae-4021-8ef1-108dc001c625" />
<img width="1370" height="574" alt="image" src="https://github.com/user-attachments/assets/054f50e4-9dd9-41a6-b1bd-818fdddb6010" />


- **Final Doping Profile:**  
  The combination of heavy (`N+`/`P+`) and lightly (`N-`/`P-`) doped regions, facilitated by spacers, yields a source/drain/channel/lateral-doping profile such as `N+N-N` or `P+P-N`.


- **Device Reliability:**  
  The LDD structure softens the electric field transition at the drain-channel junction, drastically reducing the likelihood of hot carriers entering the gate oxide, thereby increasing the breakdown voltage and improving reliability—especially in short channel and high-voltage scenarios.


- **Trade-offs:**  
  Although LDD improves reliability and scaling, it introduces additional series resistance in the device, which can slightly reduce the current driving capability and affect switching speed.

---

# Source – drain formation

This documentation section covers the theoretical background and key concepts behind the **source and drain formation** in MOSFET fabrication, including the prevention of channeling effects, the function of screen oxide, the significance of ion implantation for forming heavily doped regions, and the annealing process. It explains how these processes establish the desired source/drain structures for both NMOS and PMOS devices, and discusses major operational modes and relevant physical effects.

## Core Concepts

### Channeling and Screen Oxide

**Channeling** refers to the undesired deep penetration of implanted ions along crystallographic paths in a silicon substrate, which can occur if ions are aligned with rows of atoms, allowing them to travel further without significant scattering. To mitigate channeling, a thin **screen oxide** layer is grown over the substrate before ion implantation.

- The **screen oxide** randomizes the trajectories of incoming ions, promoting more uniform and controlled doping profiles and ensuring that ions stay within intended regions.

<img width="1338" height="626" alt="image" src="https://github.com/user-attachments/assets/5177258d-e611-4603-9a18-085abe93b185" />


### Ion Implantation for Source and Drain Formation

**Ion implantation** introduces dopants into defined regions of the silicon substrate to create heavily doped source and drain extensions.

- For **NMOS** devices, arsenic (a Group V donor) is implanted to form n+ regions. Typically, high energies (e.g., 75 keV) are used for deep penetration of heavier ions.
- For **PMOS** devices, boron (a Group III acceptor) is implanted to form p+ regions, requiring lower energies (e.g., 50 keV) due to the lighter atomic mass of boron.

The process is precisely controlled using photolithography masks, exposing only the desired implantation areas.

<img width="1220" height="674" alt="image" src="https://github.com/user-attachments/assets/3783489e-9c76-490b-90f8-67cae9a82d78" />
<img width="1231" height="675" alt="image" src="https://github.com/user-attachments/assets/bedbef00-9f6b-4133-9884-b76e4cc2d447" />


### Lightly Doped Drain (LDD) and Sidewall Spacers

**LDD structures** are employed to manage electric field gradients near the drain, reducing hot-carrier effects and improving device reliability.

- **Sidewall spacers** (typically made from silicon dioxide or nitride) are key to defining the separation between lightly and heavily doped regions, allowing sequential implantation for gradated doping concentrations.

- This results in a source/drain profile with n- (or p-) lightly doped extensions adjoining the primary n+ (or p+) regions.

<img width="1213" height="695" alt="image" src="https://github.com/user-attachments/assets/58947236-ede2-4d8c-a077-5db92b5041f9" />
<img width="1071" height="488" alt="image" src="https://github.com/user-attachments/assets/97e640e9-a96b-4a37-b7e3-89b5f22eb829" />

### High-Temperature Annealing

Following ion implantation, the wafer undergoes **annealing**—a high-temperature process (often around 1000°C).

- The annealing step:
  - Activates the implanted dopants by moving them into substitutional positions within the lattice.
  - Repairs crystal defects caused by the implantation process.
  - Slightly drives the dopants further into the substrate, achieving the desired junction depth and dopant profile.
    
<img width="1368" height="712" alt="image" src="https://github.com/user-attachments/assets/92d944cd-7313-41b3-b2e6-4794f9acbf82" />

### MOSFET Device Structure: Source, Drain, and CMOS Pairing

The completed structure includes:
- For **NMOS**: n+ source/drain, p-type substrate, and LDD n- regions.
- For **PMOS**: p+ source/drain, n-type substrate, and LDD p- regions.

These enable CMOS logic by pairing NMOS and PMOS devices, with each type requiring specific doping and masking steps.

<img width="1076" height="464" alt="image" src="https://github.com/user-attachments/assets/659240c8-b799-4feb-8d7f-5d862d3db849" />


## Key Points

- **Channeling is mitigated by the introduction of screen oxide**—randomizing ion direction and ensuring shallow, controlled junctions.
- **Photolithography masks define precise doping regions**, allowing selective formation of NMOS and PMOS transistors on the same substrate.
- **Ion type and implant energy** differ depending on whether n- or p-type regions are created: arsenic (high energy) for NMOS, boron (lower energy) for PMOS.
- **Sidewall spacers enable Lightly Doped Drain (LDD) regions**, reducing electric fields near the channel and improving performance and durability.
- **High-temperature annealing activates dopants and repairs damage**, finalizing the electrical properties of the source and drain.
- The overall process produces **heavily doped source/drain regions with lightly doped extensions**, critical for modern CMOS device performance and scaling.

---

# Local interconnect formation

## Summary

This section explains the theoretical foundations behind forming electrical contacts in semiconductor devices, focusing on their role, the materials involved, the physical processes used for deposition, and the distinction between local and higher-level interconnections. The concepts relate to how contacts enable electrical connectivity in integrated circuits, particularly for CMOS (complementary metal-oxide-semiconductor) technology.

## Core Concepts

### Role of Contacts in CMOS Devices

- **Contacts** are the only accessible interface points for users to control the electrical behavior of PMOS and NMOS transistors.
- They connect external circuits to the **source, drain,** and **gate** regions within each transistor.
- The performance and reliability of a device depend critically on the materials and processes used to form these contacts.

<img width="1067" height="453" alt="image" src="https://github.com/user-attachments/assets/c3a38aca-2a7d-4299-a75c-b847446076c0" />


### Surface Preparation for Contacts

- **Oxide layers** are deposited earlier during fabrication to prevent damage such as channeling, but must be selectively removed (etched) to expose regions for contact formation.
- The etching process typically uses **hydrofluoric acid (HF)**, which removes the oxide isotropically, exposing the silicon surface at the desired regions.

<img width="1353" height="619" alt="image" src="https://github.com/user-attachments/assets/06bf1e0b-0b2a-4686-acc1-3ea07be7d6fe" />


### Metal Deposition via Sputtering

- **Sputtering** is a physical vapor deposition method used to coat the exposed regions with a thin layer of metal, such as **titanium**.
- The process involves bombarding a pure metal (titanium) with argon ions, causing titanium atoms to eject and deposit onto the substrate.
- **Sputtering** provides good step coverage (uniformity) and is highly controlled, making it a preferred technique for depositing contacts.

<img width="1313" height="626" alt="image" src="https://github.com/user-attachments/assets/a5465809-caab-491c-915b-f73a0e0a63d2" />
<img width="1179" height="471" alt="image" src="https://github.com/user-attachments/assets/3529dfa7-6c0e-4381-973b-498a29c77263" />
<img width="1306" height="622" alt="image" src="https://github.com/user-attachments/assets/b65512ce-8472-4257-a4a8-ca6782e0f631" />


### Formation of Low-Resistivity Contacts

- After deposition, **titanium** reacts at high temperature (typically 650–700°C) in a nitrogen ambient with the underlying silicon to form **titanium silicide (TiSi₂)**.
- The key reaction is: $Ti + 2Si \rightarrow TiSi_2$
- **Titanium silicide (TiSi₂)** offers low electrical resistance, improving current flow between the silicon and the wiring layers.
- Simultaneously, titanium can react with nitrogen to form **titanium nitride (TiN)**, another conductive material often used for local interconnects due to its stability and specific resistivity.

<img width="1327" height="617" alt="image" src="https://github.com/user-attachments/assets/e22d97fc-05b6-4d18-beee-6eb3bfaa1a59" />
<img width="1313" height="616" alt="image" src="https://github.com/user-attachments/assets/017789d2-3569-4a9c-9cda-098f340ce7df" />


### Local Interconnects vs Higher-Level Metalization

- **Local interconnects**: Short-range connections (e.g., between the drain/source and adjacent gates) are formed using low-resistivity silicides and titanium nitride.
- **Higher-level metalization**: Longer connections or connections brought out of the chip use higher-level metal layers, which may be different in composition and optimized for longer wiring paths.
- Design decisions are required regarding which contacts are connected locally and which should be routed to higher metal layers for external connection.

<img width="1066" height="720" alt="image" src="https://github.com/user-attachments/assets/91afa3e9-894b-4447-8b15-f72bc0a9544d" />
<img width="1055" height="584" alt="image" src="https://github.com/user-attachments/assets/08545848-3e1e-4107-b886-0f438c21667f" />


### Cleaning and Patterning

- **Patterning** is achieved using photoresist and photolithography, followed by exposure to UV light and development.
- **RCA cleaning** is used to remove unwanted or excess titanium nitride after patterning. RCA solution usually contains deionized water, ammonium hydroxide, and hydrogen peroxide.
- Proper cleaning ensures only the required contact regions remain, supporting high device reliability and performance.

<img width="881" height="300" alt="image" src="https://github.com/user-attachments/assets/c8565f62-0065-4a3c-9757-2b0b0aa4a345" />
<img width="1258" height="673" alt="image" src="https://github.com/user-attachments/assets/4bdad670-9e6d-4664-983e-30e87ae6bc3f" />
<img width="1061" height="659" alt="image" src="https://github.com/user-attachments/assets/be695eb2-3240-4770-89d2-f21ebc777663" />


## Key Points

- **Contacts** are essential interfaces that allow electrical access to the source, drain, and gate of transistors in CMOS devices.
- **Oxide etching** with hydrofluoric acid exposes the regions where contacts will be made.
- **Sputtering** of titanium forms the initial metal layer for contacts.
- **Annealing** in nitrogen ambient at high temperatures causes titanium to react with silicon and nitrogen, forming **TiSi₂** and **TiN** respectively.
- **TiSi₂** provides low resistance pathways suitable for local wiring; **TiN** is also used for local interconnects due to its conductive properties.
- Selection of which contacts are connected locally versus routed to higher levels depends on circuit functionality (e.g., tying drains of PMOS/NMOS together or bringing outputs off-chip).
- **Photolithography and RCA cleaning** processes ensure that only the necessary regions maintain conductive contacts after fabrication steps.

---

# Higher level metal formation

This section elaborates on the theoretical aspects of higher-level metal formation within microelectronic fabrication, focusing on the need for planarization, dielectric deposition, barrier layers, and hierarchical interconnect design. The process ensures reliable metal interconnections across multiple layers by addressing surface topography, material stability, and electrical isolation.


---

## Core Concepts

### Non-Planar Surface Topography & Planarization

- After initial device fabrication steps, the wafer's surface is **non-planar**, containing "hills and valleys" due to underlying features.
- Such non-flat surfaces are unsuitable for directly depositing metal interconnects, as this can cause discontinuities and reliability issues.
- To address this, a **thick SiO₂ layer** is deposited as a dielectric. This oxide may be **doped with phosphorus** (for sodium ion barrier) or **boron** (to assist in high-temperature process control).
- **Chemical Mechanical Polishing (CMP)** is used to planarize the surface, creating a uniformly flat layer upon which metal can be deposited.

<img width="1305" height="705" alt="image" src="https://github.com/user-attachments/assets/db9a27f0-385d-405f-bdd0-fe67c040e088" />
<img width="1306" height="678" alt="image" src="https://github.com/user-attachments/assets/a80a142f-6e43-4399-9ff0-5f03868df9dc" />
<img width="1442" height="779" alt="image" src="https://github.com/user-attachments/assets/51ead3ef-6ea9-4914-877b-83c901a78983" />

---

### Contact Formation

- To enable electrical connections between different metal or active device layers, **contact holes (vias)** are created in the planarized dielectric using **photolithography** and **etching**.
- These vias require a sequence of **masking, exposure, development, and etching** steps, using specific "mask numbers" to designate process stages.
- The completion of a via exposes the underlying layer for subsequent metal deposition.

<img width="1061" height="644" alt="image" src="https://github.com/user-attachments/assets/3a710d60-74f6-4251-9f0e-af3baafd2c7f" />

---

### Barrier and Adhesion Layers

- A **thin titanium nitride (TiN) layer** is deposited to:
  - Promote strong adhesion between metal and SiO₂ dielectric
  - Act as a diffusion barrier, preventing intermixing of metals from adjacent layers

<img width="1139" height="643" alt="image" src="https://github.com/user-attachments/assets/3aa6f113-d38a-4a43-8394-d748786c687c" />


---

### Metal Deposition & Planarization

- A **blanket tungsten layer** is deposited to fill the vias, providing robust low-resistance contacts.
- Excess tungsten is removed by a second CMP step, leaving metal only within defined contact holes and resulting in a planar surface.

<img width="1307" height="644" alt="image" src="https://github.com/user-attachments/assets/5da906d3-307b-4c71-8eec-21b68f821f66" />
<img width="1295" height="657" alt="image" src="https://github.com/user-attachments/assets/2a018f3d-bd85-4b91-9370-0223292d3ef9" />


---

### Metal Interconnect Hierarchy

- **Aluminum is deposited** over the planarized surface to serve as the first major metal interconnect layer.
- Patterning of aluminum via photolithography and etching creates circuit connectivity at the desired locations.
- To **create higher-level interconnects**, the process repeats:
  - Deposit SiO₂ (or other dielectrics)
  - Planarize (CMP)
  - Pattern vias with corresponding mask
  - Add barrier layer and fill with tungsten
  - Deposit and pattern the next metal layer (often thicker at each level)

<img width="1289" height="643" alt="image" src="https://github.com/user-attachments/assets/30baf23c-fa06-4d63-843c-070694ba3c74" />
<img width="1386" height="751" alt="image" src="https://github.com/user-attachments/assets/ddbb3232-ffe9-4c40-a977-9c3bfd1fdc1c" />
<img width="1342" height="669" alt="image" src="https://github.com/user-attachments/assets/8a1cef99-a7f8-4fc2-985a-647425333894" />
<img width="1098" height="587" alt="image" src="https://github.com/user-attachments/assets/c48c9af7-1a08-4abe-b218-d4e6cda7666e" />
<img width="1327" height="675" alt="image" src="https://github.com/user-attachments/assets/eced812c-8378-4e49-845e-74136d3e634a" />
<img width="1350" height="690" alt="image" src="https://github.com/user-attachments/assets/b2ed48f3-f66c-42e5-a637-f8fe0ed1ccba" />
<img width="1295" height="679" alt="image" src="https://github.com/user-attachments/assets/4da0dffb-70de-4433-a1d7-ce1a33dd20ce" />
<img width="1297" height="707" alt="image" src="https://github.com/user-attachments/assets/7b66ef67-105e-4435-aad3-4a123e21ef3e" />

---

### Final Passivation and Protection

- The final fabrication step often uses a strong dielectric layer (e.g., **Si₃N₄**) to cover and protect the entire chip surface.
- This passivation prevents contamination and mechanical damage.
- Contact holes are finally patterned through this protective layer to allow connections to package pins or external circuits.

<img width="1557" height="828" alt="image" src="https://github.com/user-attachments/assets/ea2adebc-286f-4dbf-8667-b0d5b943a2fc" />
<img width="1591" height="803" alt="image" src="https://github.com/user-attachments/assets/18bc0bf8-166c-449a-a6a6-4e44d21d283a" />
<img width="1538" height="835" alt="image" src="https://github.com/user-attachments/assets/8d6d5169-fed4-432c-aa40-beb25fa36391" />
<img width="1050" height="835" alt="image" src="https://github.com/user-attachments/assets/e51e8959-c482-4c80-bacf-626ccad8476e" />

---

## Key Points

- **Planarization** is *crucial* for reliable multi-level metallization; it prevents discontinuities and supports high-density interconnects.
- **Dielectric layers** (e.g., SiO₂, sometimes doped) provide electrical isolation and structural support between metal layers.
- **Contact holes (vias)** are formed using photolithography and are essential for interlayer connectivity; each new level requires a new mask.
- **Barrier layers** such as titanium nitride are critical for adhesion and to prevent unwanted diffusion between metal layers.
- **Metal layers** are deposited sequentially, with thickness often increasing at higher hierarchical levels to accommodate more current and lower resistance.
- **CMP** ensures each layer begins with a flat surface, maintaining process integrity through each metallization level.
- The **final passivation layer** protects the finished chip from environmental factors and provides mechanical strength.

---

# Lab introduction to Sky130 basic layers layout and LEF using inverter

This lab guides you in constructing and verifying the layout of a **basic CMOS inverter** using standard VLSI design tools. You will learn how to identify and use the necessary process layers (local interconnect, Metal1, Metal2, etc.), select the correct diffusion regions for NMOS and PMOS devices, verify device presence and connectivity using layout tools, and understand the difference between physical layout and abstract cell representations (like LEF). The lab steps walk through detailed operations for selection, connectivity checks, and an explanation of the LEF abstraction.

## Lab Steps

### 1. Recognize and Identify CMOS Inverter Layers

- **Objective:** Understand and identify each layer required for CMOS inverter layout in the layout editor (e.g., magic or SkyWater).
    - The **local interconnect layer** (*local I*) is used for short routing.
    - **Metal1** (light purple) and **Metal2** (pinkish) are upper-level routing layers.
    - The **"anvil"** is represented by solid, slanting dashed lines.
    - **NMOS diffusion** is marked in green; **PMOS diffusion** in brown.
    - **Polysilicon** (often red) forms the gate.
    - Use the color palette within the tool to identify each layer by hovering the cursor and observing the legend.

<img width="1212" height="716" alt="image" src="https://github.com/user-attachments/assets/51485718-85d1-47b0-9b92-0f606c682e4d" />


### 2. Confirm Diffusion Regions and Polysilicon Intersections

- **Objective:** Ensure devices are correctly instantiated according to CMOS design rules.
    - Where **polysilicon crosses NMOS diffusion (green)**, an **NMOS transistor** is formed.
    - Where **polysilicon crosses PMOS diffusion (brown)**, a **PMOS transistor** is formed.

- Hover over the intersection area in the layout tool and press `s` to select the object beneath the cursor.

<img width="363" height="580" alt="image" src="https://github.com/user-attachments/assets/c6a66c77-22f8-443f-874b-b3d724cb98c1" />


### 3. Verify Device Structures

- With the area selected (after pressing `s`), use the tool's "what" function (sometimes invoked by a command or menu) to identify the selected structure.
    - Confirm that the intersection of **poly and n-diffusion** reports as **NMOS**.
    - Confirm that the intersection of **poly and p-diffusion** reports as **PMOS**.

<img width="998" height="607" alt="image" src="https://github.com/user-attachments/assets/0937a5cd-2d73-419b-99f6-b7701d7cbe75" />
<img width="1004" height="586" alt="image" src="https://github.com/user-attachments/assets/41e7e98e-614f-409f-a2d8-3ea2f29e5ede" />


### 4. Examine Gate Connections

- **Objective:** Verify both gates are connected to the inverter input.
    - The gates of both NMOS and PMOS are connected to the input net.
    - Select the gate poly line and observe that both transistor gates are electrically connected.

<img width="354" height="568" alt="image" src="https://github.com/user-attachments/assets/9b952f64-0aa7-4514-bcec-b41d0335bdbf" />


### 5. Check Output Connectivity (Drain Connections)

- **Objective:** Confirm that the drains of both MOSFETs form the inverter output.
    - To check connections, press `s` three times with the cursor on the OUT (Y) node:
        - First press: selects item under cursor (e.g., output net).
        - Second and third press: expands selection to all connected items.
    - The highlighted selection should include both NMOS and PMOS drains, confirming their connectivity at the output (Y node).

<img width="377" height="607" alt="image" src="https://github.com/user-attachments/assets/14a4c54e-43ee-445c-ad6d-4dae56761273" />

### 6. Verify Power and Ground Connections

- **Objective:** Ensure correct power architecture.
    - The **source of PMOS** must be connected to **VDD**.
    - The **source of NMOS** must be connected to **ground**.

- For PMOS:
    - Move the cursor over the VDD contact and press `s` to select it.
    - Press `s` again to expand selection to the entire region.
    - Confirm that the selected region correctly spans from the PMOS source to the VDD rail.

<img width="390" height="598" alt="image" src="https://github.com/user-attachments/assets/ff63a4db-32e5-4d67-8a1e-f036312a7e51" />


- For NMOS:
    - Move the cursor over the ground contact and repeat the selection process (`s`, `s`).
    - Confirm the source of the NMOS is connected to the ground rail.

<img width="331" height="571" alt="image" src="https://github.com/user-attachments/assets/b249884a-35b5-442d-a519-942623ce5d12" />


### 7. Reference: Building Inverter from Scratch

- An in-depth guide to building the inverter and further documentation is available on the referenced GitHub page.
- Review descriptions of **OpenLane** tools and the layout process, if additional information is required.

[Here](https://github.com/nickson-jose/vsdstdcelldesign)

### 8. Understand Abstract Cell Views (LEF)

- **Objective:** Grasp the abstraction provided by the LEF (Library Exchange Format) file.
    - LEF describes **layout boundaries, metal layers, and pin locations** but omits **logic implementation** details for IP protection.
    - Compare the detailed physical layout (showing transistors, wells, and polysilicon) with LEF (showing only cell edges and pin markers).

- In commercial tools, LEF may also be known as "frame view". LEF is essential for placement and routing, providing just the necessary physical cell information for automatic tools.

---

# Lab steps to create std cell layout and extract spice netlist

This lab demonstrates the **step-by-step creation of a standard cell** using the Magic VLSI layout tool. The aim is to define a fixed bounding box for the cell, lay down active layers (including wells, contacts, and metal interconnects), incrementally resolve design rule errors, and extract the cell for SPICE-level simulation.  
The workflow includes defining precise coordinates, drawing and connecting all relevant layers (such as N-well, local interconnect, and metal 1), ensuring the cell is design-rule clean, and extracting the layout for simulation.

## Lab Steps

### 1. Start Magic and Open a New Layout

- Open Magic and ensure the layout window is empty for a fresh cell design.
  
  <img width="992" height="671" alt="image" src="https://github.com/user-attachments/assets/56edcbf3-1b3a-4a48-9713-ca164ba40981" />


### 2. Define the Fixed Bounding Box (Bbox)

- Create a fixed bounding box to set your standard cell's boundaries.  
- Use command, specifying lower-left and upper-right coordinates (`llx`, `lly`, `urx`, `ury`):
    ```
    box 0 0 138 272
    ```
  - **Lower-left corner** is at (0,0).  
  - **Upper-right corner** is at (138, 272) (unit: lambda, or 1.38μm by 2.72μm if using 0.01μm units).
  
<img width="1008" height="692" alt="image" src="https://github.com/user-attachments/assets/41645373-3dc8-4de2-9023-ac38ca095853" />
<img width="791" height="513" alt="image" src="https://github.com/user-attachments/assets/9e39af8e-c48c-42a0-ad1b-0c9ee187b2b6" />


- *Tip: Typing `box` with the cursor shows the current box dimensions in the Magic terminal.*

### 3. Draw the N-well Region

- The dashed line represents the **N-well** for PMOS.
- Select the region corresponding to your PMOS and:
    ```
    paint nwell
    ```

### 4. Draw the Local Interconnect Layer (Poly or Locali)

- Use the paint tool to draw **Locali** (local interconnect) and connect between devices.
    ```
    paint locali
    ```

### 5. Draw Metal1 for Power and Ground Rails

- Standard cell rules require **power (VDD)** and **ground (GND)** rails to be on **Metal1**.
    ```
    paint metal1
    ```

### 6. Add Contacts Between Layers

#### a. N-well/Substrate Contact

- Needed to connect **nwell** and **locali** for PMOS.
    ```
    paint pcontact
    ```
  - Place where N-well and locali overlap.

#### b. Locali to Metal1 Contact (Licon)

- Needed to connect **locali** to **metal1**.
    ```
    paint licon
    ```
  - Place where locali and metal1 overlap.

#### c. For NMOS: P-substrate to Locali Contact

- Draw contacts between the **p-substrate** (no explicit well) and **locali** for NMOS.
    ```
    paint ncontact
    ```

### 7. Check Layout Design Rules (DRC) Interactively

- Magic's DRC engine flags all errors during layout.
- To inspect errors:
    - Go to the DRC tab and click **Find Next Error**.
    - Magic will zoom in on DRC violations.
    - Each violation is detailed in the terminal.
  
<img width="178" height="162" alt="image" src="https://github.com/user-attachments/assets/ec02a174-b982-4edf-92ff-874ffaa72a3f" />


- To fix DRC violations:
    - Select the area covering the error.
    - Paint the required layer (e.g., missing nwell or metal1).
    - When corrections are made, rerun the DRC check.
    - Errors should reduce as you fix issues.
  
  <img width="873" height="299" alt="image" src="https://github.com/user-attachments/assets/dd9f0aab-007c-4318-a93e-e69158649bf8" />
  <img width="688" height="629" alt="image" src="https://github.com/user-attachments/assets/dea1abfb-b825-4650-8935-2e3236bde730" />


### 8. Ensure DRC Cleanliness

- The final design **must have zero DRC errors** for manufacturability.
- Confirm by checking that the DRC count is zero in the Magic interface.
  
<img width="77" height="28" alt="image" src="https://github.com/user-attachments/assets/6357f659-706c-4a14-943f-b03375b2000f" />


### 9. Extract the Netlist for SPICE Simulation

- Open Magic's tkcon (Magic's TCL shell).
- Change directory to your working cell directory if needed.

#### a. Extract Layout to .ext File
    ```
    pwd
    extract all
    ```
- This creates an extraction file (e.g., `sky_inv.ext`).
  
 <img width="982" height="340" alt="image" src="https://github.com/user-attachments/assets/e6942771-b725-460a-8752-f23f87ab8cc1" />


#### b. Verify .ext File Creation
    ```
    ls -l
    ```
- You should see the new `.ext` file in the directory.

<img width="733" height="77" alt="image" src="https://github.com/user-attachments/assets/58e52230-98f8-45dd-a451-ede36063cbe5" />

#### c. Convert Extraction to SPICE Netlist

    ```
    ext2spice cthresh 0 rthresh 0
    ext2spice
    ```
- This generates a SPICE netlist (e.g., `sky_inv.spice`).  
- Check directory contents again to confirm file creation.

<img width="981" height="340" alt="image" src="https://github.com/user-attachments/assets/403cce83-46e1-475b-89a7-bca64ededed5" />
<img width="734" height="322" alt="image" src="https://github.com/user-attachments/assets/5e8ff6a0-4583-4c34-b815-dcb8fff4c724" />


#### d. View SPICE Netlist Contents

    ```
    cat sky_inv.spice
    ```
- Inspect the generated netlist for correctness.

 <img width="727" height="496" alt="image" src="https://github.com/user-attachments/assets/e98bc4ba-bf35-404a-aa55-ac3478bb12b0" />

---

- The cell is now ready for simulation in NGspice or other SPICE simulators.
- *Note: The SPICE netlist includes extracted parasitic capacitances for realistic simulation.*

---

# Sky130 Tech File Labs

# Lab steps to create final SPICE deck using Sky130 tech

This lab demonstrates the setup and simulation of a CMOS inverter circuit using SPICE. You will define device connections, model files, voltage sources, and perform a transient analysis using Ngspice. The steps cover schematic interpretation, SPICE deck creation, model file linking, input pulse definition, scaling adjustments, and running a transient analysis from 1 ns to 20 ns.

## Lab Steps

### 1. Analyze Schematic Nodes and Device Connections

- **Identify PMOS (M1000)**  
  - Model: P-shot  
  - Drain: connected to node Y  
  - Gate: connected to node A  
  - Source and substrate: connected to node VWR  
  - Node names (Y, A, VWR) designate connection points, not voltages

- **Identify NMOS**  
  - Model: N-shot  
  - Drain: connected to node Y  
  - Gate: connected to node A  
  - Source and substrate: connected to node VGD

<img width="731" height="363" alt="image" src="https://github.com/user-attachments/assets/4e7c87e1-31b3-4ff0-9d94-311a6b246fd3" />


### 2. Define Required Voltage Nodes

- **Connect VGD node to ground (VSS)**
- **Connect VWR to supply voltage**
- **Create nodes:**
  - Node '0' for ground
  - Node for VDD with a value of $3.3$ V
  - Node for input voltage (V_A), which will use a pulse source


### 3. Adjust Scaling for Layout Consistency

- **Set SPICE deck scale to match layout units**
- The default scale is $1$ dimension unit $= 10,000$ microns, but the required grid is $0.01$ units per dimension.
- For example, a width of $37$ in the SPICE file with a grid scale of $0.01$ equals $0.37$ microns:  
  $width = 37 \times 0.01 = 0.37$ microns

- Edit the `.scale` parameter in your SPICE deck:
```spice
.scale 0.01u
```
<img width="671" height="507" alt="image" src="https://github.com/user-attachments/assets/383c2209-7bcc-4089-89e1-04ac07e4bbb2" />

### 4. Include PMOS and NMOS Model Files

- Model files are typically inside the `libs/` folder.
- Include both model files in your netlist:
```spice
.include ./libs/pshort.lib
.include ./libs/nshort.lib
```
<img width="704" height="557" alt="image" src="https://github.com/user-attachments/assets/d2f81caf-a20e-4fe2-b909-819cdd9ffca9" />


### 5. Define Devices in the SPICE Netlist

- **PMOS definition** (ensure correct model reference):
```spice
M1000 Y A VPWR VPWR pshort.lib
```

- **NMOS definition**:
```spice
M1001 Y A VGND VGND nshort.lib
```
*Adjust device/model names as per actual filenames and netlist conventions.*


### 6. Define Power and Ground Sources

- **Supply Voltage (VDD) between VWR and ground:**
```spice
VDD VPWR 0 3.3V
```
- **Ground reference (VSS) between VGD and ground:**
```spice
VSS VGND 0 0V
```

### 7. Define the Input Pulse Source

- **Input pulse (V_A) between gate A and ground:**
```spice
Va A VGND PULSE(0V 3.3V 0 0.1ns 0.1ns 2ns 4ns)
```
- This describes:
  - Start at $0$ V
  - Pulse to $3.3$ V
  - Delay: $0$ ns
  - Rise time: $0.1$ ns
  - Fall time: $0.1$ ns
  - Pulse width: $2$ ns
  - Period: $4$ ns

<img width="660" height="469" alt="image" src="https://github.com/user-attachments/assets/e049877a-1c1b-4dbe-8f2e-db0047b83f7c" />

### 8. Set Up Transient Analysis

- **Configure transient simulation:**  
  Runs from $1$ ns to $20$ ns.
```spice
.tran 1n 20n
```
# Lab steps to characterize inverter using sky130 model files

This lab focuses on characterizing a standard inverter cell by analyzing its transient response and extracting key performance metrics such as rise/fall transition times and propagation delays ("cell delays") at room temperature. The steps include running SPICE simulations, plotting time-domain responses, adjusting load capacitance, and conducting detailed measurements on simulation results. All steps are illustrated with plot interactions and calculations.

## Lab Steps

### 1. Run Initial SPICE Simulation and Plot Output

```ngspice
ngspice sky130_inv.spice
```
<img width="730" height="682" alt="image" src="https://github.com/user-attachments/assets/4a568edf-ac60-4cd1-9a9a-cdf3c2a02287" />

- Open your SPICE simulation tool.
- Run the simulation for the inverter cell.
- Plot the **output voltage (Y)** against **time** to visualize the cell's response to the input.

```ngspice
plot y vs time a
```

<img width="741" height="730" alt="image" src="https://github.com/user-attachments/assets/c690b3fc-12a1-442a-b957-2987d55222a3" />


### 2. Adjust Load Capacitance

- Inspect the SPICE netlist for the output load capacitor, labeled as `C3`.
- The default value may be too low (e.g., `0.24fF`). Increase it for a more realistic load, such as `2fF`.

```spice
C3 OUT 0 2f
```
<img width="662" height="553" alt="image" src="https://github.com/user-attachments/assets/536ea558-db99-48e4-920e-c69fc7897d38" />
<img width="655" height="551" alt="image" src="https://github.com/user-attachments/assets/f3313728-58bf-45f0-ba21-c04f3e5984aa" />

- Rerun the simulation.

<img width="889" height="884" alt="image" src="https://github.com/user-attachments/assets/16951199-b7f4-4865-8d54-700ff893072a" />


### 3. Plot New Transient Output

- Plot the **output voltage versus time**, with the adjusted load capacitance.
- Observe the output for improved waveforms.

### 4. Identify and Measure Rise Transition Time

- The **rise transition** is defined as the time required for the output to transition from **20% to 80%** of its maximum value.
- Given the supply voltage $V_{DD} = 3.3V$:
  - $20\%$ of $3.3V$ is $0.66V$.
  - $80\%$ of $3.3V$ is $2.64V$.

- Use the plot:
  - Zoom in near the output rise edge.
  - Locate the time at which the output voltage first crosses $0.66V$ (let's say $t_{20\%} = 2.18\,ns$).
  - Locate the time at which the output voltage reaches $2.64V$ (let's say $t_{80\%} = 2.245\,ns$).

<img width="888" height="861" alt="image" src="https://github.com/user-attachments/assets/5efcae88-419b-4405-8612-ee30e65e9b0d" />

- Calculate the rise transition time:
  - $t_{rise} = t_{80\%} - t_{20\%} = 2.245\,ns - 2.18\,ns = 0.064\,ns$

### 5. Measure Fall Transition Time

- The **fall transition** is the time required for the output to fall from **80% to 20%** ($2.64V$ down to $0.66V$).
- Follow the same procedure as above by examining the output fall region.
- The value is to be determined as part of your assignment.

### 6. Measure Cell Propagation (Rise) Delay

- The **cell rise delay** is the time difference between the $50\%$ point of the input transition and the $50\%$ point of the output transition on a rising edge.
- Calculate $50\%$ of $3.3V$: $1.65V$.

- On the plot:
  - Find the time when the **output** rises through $1.65V$ (e.g., $t_{out,50\%} = 2.211\,ns$).
  - Find the corresponding time when the **input** crosses $1.65V$ on its rising edge (e.g., $t_{in,50\%} = 2.150\,ns$).

- Compute **cell rise delay**:
  - $t_{rise,delay} = t_{out,50\%} - t_{in,50\%} = 2.211\,ns - 2.150\,ns = 0.061\,ns$

### 7. Measure Cell Fall Delay

- The **cell fall delay** is the time difference between the $50\%$ point of a falling output and the $50\%$ point of the input transition.
- This step will be completed as part of your assignment.

### 8. Note the Simulation Temperature

- The characterization was performed at a temperature of **$27^\circ$C** (room temperature).

<img width="676" height="94" alt="image" src="https://github.com/user-attachments/assets/33ef55db-2b81-41cd-82be-efc7cad826d7" />


### 9. Prepare for Layout Extraction and OpenLANE Integration

- The current layout for the characterized inverter is displayed.

<img width="479" height="705" alt="image" src="https://github.com/user-attachments/assets/f009b05b-a0fd-408b-89bb-2d264772053d" />

- Next, create a **LEF (Library Exchange Format) file** from the layout for integration into OpenLANE.
- In future labs, you will use this custom cell within a larger PICO or RISC-V core design and validate OpenLANE tool acceptance.

---
# Lab introduction to Magic tool options and DRC rules

This documentation provides an overview of **Magic's Design Rule Checking (DRC) engine** as utilized in open-source EDA tool flows, specifically referencing the context of the Google Skywater PDK. It explains Magic’s approach to DRC, the structure and function of the technology file, and the categories of DRC rules available. Theoretical concepts behind electrical connectivity, rule types, and geometric manipulations within technology files are discussed, alongside the relevance of mask data formats.

<img width="531" height="95" alt="image" src="https://github.com/user-attachments/assets/1f336731-1e1d-4493-86ef-32971aec669a" />


## Core Concepts

### Magic’s DRC Engine

- **Design Rule Checking (DRC)** in Magic is essential for validating whether a physical layout meets the manufacturing constraints defined by a process technology. The engine analyzes geometric relationships between layers and flags violations based on predefined rules.
- Magic supports multiple DRC styles within a single Process Design Kit (PDK), enabling flexibility for different fabrication requirements.


### Technology Files

- The **technology file** in Magic acts as the central configuration for a given process technology. It contains comprehensive data including:
  - Definitions of layer types, colors, and patterns.
  - Electrical connectivity information.
  - DRC rules for layout validation.
  - Rules for GDS file generation, device extraction, and other process-specific operations.
- Technology files allow users to model process technologies in a highly customizable and detailed manner, making them foundational for open-source hardware workflows.


### Edge-Based DRC Rules

- **Edge-based rules** are the fundamental method by which Magic checks for DRC violations. These rules evaluate:
  - The relationship at boundaries between layers.
  - Geometric properties such as width, spacing, surround, extend, and overhang.
  - Generic edge circumstances that don't specifically fit named rule categories.

The typical spacing rule can be expressed as $d \geq d_\text{min}$, where $d$ is the measured distance and $d_\text{min}$ is the minimum allowed.


### Non-Edge-Based DRC Rules

- **Non-edge-based rules** relate to layer properties independent of edges, such as:
  - **Area rule:** Ensures the area $A$ of a polygon meets $A \geq A_\text{min}$.
  - **Maximum width rule:** Restricts the maximum possible width $w$ so $w \leq w_\text{max}$.
  - **Rectangular only:** Enforces polygonal shapes to be strictly rectangles.
  - **Angles:** Specifies allowable angles at polygon corners.


### Geometric Manipulation and Boolean Operations

- For rules too complex to model directly with edge or area rules, Magic allows for **sequential geometric manipulations** using operators such as:
  - **Boolean Operators:** $A \land B$ (AND), $A \lor B$ (OR), $\neg A$ (NOT)
  - **Grow/Shrink:** Morphs shapes by expanding ($r_\text{grow}$) or contracting ($r_\text{shrink}$) boundaries by specified radii.
  - **Bloat:** Applies more complex expansion logic.

The result of these operations becomes the basis for additional rule checking.


### Mask Data Representations

- **SIF (Caltech Intermediate Format):** A human-readable ASCII-based format for mask layouts. Each mask layer is named rather than numerically indexed.
- **GDSII:** The industry-standard, binary format for mask data used in fabrication. It is less human-readable but widely supported.

Both formats serve as outputs for layout designs, but only SIF is referenced for rule definition purposes in Magic's tech files.


## Key Points

- **DRC styles** in Magic are adaptable for various manufacturing needs and can target different rule sets within the same PDK.
- **Technology files** aggregate all settings required for both the layout editor and the design rule engine, uniting them in a single interface.
- **Edge-based rules** address relationships between adjacent layout features, such as minimum spacing, required surrounds, or permitted overlaps.
- **Non-edge-based rules** enforce overall polygon properties, including area constraints and geometric regularity.
- **Advanced rule checking** takes advantage of geometric and boolean manipulation, extending Magic’s sophisticated layout validation capabilities.
- **Documentation** for Magic’s syntax, command usage, and rule definitions is essential for the creation and amendment of technology files, supporting reproducible open-source hardware development flows.
- **Open-source process technologies** such as Google Skywater provide the foundation and transparency necessary for community-driven advancement of hardware design.

---

# Lab introduction to Sky130 pdk's and steps to download labs

This lab introduces the use of the **Skywater PDK** and the related documentation for understanding and applying design rules in integrated circuit layout with the **Magic** layout tool. The main aim is to familiarize participants with navigating the design rule documentation, opening and viewing Magic layouts related to these rules, and manipulating technology files for lab exercises. All necessary files, including layouts and technology scripts, are provided in a self-contained package. The lab will guide you through setup, navigation, and basic usage.

**Key steps:**
- Access Skywater PDK documentation and identify relevant design rules.
- Locate and examine lab layout files (.mag) using Magic.
- Review and modify technology files as needed.
- Start Magic and explore layout layers, including special handling for automatically-generated implant layers.

---

## Lab Steps

### 1. Open Skywater PDK Documentation

- Launch your browser and navigate to the **Skywater PDK documentation** at `[Click Here](https://skywater-pdk.readthedocs.io/en/main/)`
- Review the sidebar and locate the **Design Rules** section.
- Click through the rule sets and examine relevant illustrations.
  
<img width="1914" height="856" alt="image" src="https://github.com/user-attachments/assets/cf8a605c-f518-457e-8d39-82062c89c7f9" />
<img width="273" height="548" alt="image" src="https://github.com/user-attachments/assets/42396ee4-8e2b-4ebe-8071-0122535a40a8" />


### 2. Locate Magic Layouts for Lab Exercise

- Find the provided lab package for this exercise. It contains approximately two dozen `.mag` layout files along with a technology file and startup script.
- Unpack the provided tarball in your home directory. Use the terminal:

  ```sh
  wget https://opencircuitdesign.com/open_pdks/archive/drc_tests.tgz
  tar xfz drc_tests.tgz
  ```
<img width="894" height="310" alt="image" src="https://github.com/user-attachments/assets/a2191f13-ea0a-4423-9275-b73527569f59" />

### 3. Review Layout Files and Magic RC Script

- In `~/Desktop/work/drc_tests/` you should find:
  - `.mag` files (layouts for lab exercises)
  - `magicrc` file (Magic startup script)
  - Technology file for Magic

  <img width="732" height="490" alt="image" src="https://github.com/user-attachments/assets/35a2127c-335e-499d-9c72-c73e5d983d8c" />

### 4. Set Up and Start Magic Layout Tool

- Change into the lab directory:

  ```sh
  cd ~/Desktop/work/drc_tests/
  ```

- Start Magic with the recommended graphics option (for improved visuals):

  ```sh
  magic -d OGL
  # or
  magic -d XR
  ```

  *You may also simply run:*
  ```sh
  magic
  ```

<img width="852" height="767" alt="image" src="https://github.com/user-attachments/assets/cbb1721a-7b4d-4e18-b205-c3cf801d0729" />

- Magic will use the `magicrc` script to load the tech file.

### 5. Viewing and Highlighting Layers in Magic

- The documentation defines layers by mask/design type (e.g., metal lines, vias, Poly, diffusion, implants).
- Note: **Implant-like layers** are automatically generated and are not directly visible in the layout.

- To view these, use Magic’s `CIFC` command to highlight generated layers. For a given layer (as indicated by layout file header):

  ```tcl
  cif see <layer_name>
  ```

  *Example: Highlighting a "nplus" layer:*
  ```tcl
  cif see nplus
  ```


- Layout files may include top-of-file comments specifying which layers to visualize using this command.

### 8. Summary of Layer Groups

- In documentation and layouts, layer references split into:
  - **Drawn layers:** metal lines, vias, Poly, diffusion
  - **Implants:** nplus, pplus, threshold tuning
  - **Specialty rule sets:** e.g., MIM caps, extended drain MOSFETs

- For mathematical expressions or rule tabulations refer to documentation; if needed, format equations using dollar signs, e.g. `$W \geq 0.32\,\mu m$`.
---

# Lab introduction to Magic and steps to load Sky130 tech-rules

This lab demonstrates how to use the Magic VLSI layout tool to inspect and understand design rule checking (DRC) violations using **Metal 3 (M3)** layers in the Google SkyWater process. The steps cover starting Magic, loading a sample layout, interacting with DRC violations, visualizing derived layers (contacts), and measuring geometric requirements. The exercises teach how layout geometry, DRC feedback, and process rules interact, with specific focus on **M3 DRC rule M3.4** and the auto-generation of contact cuts.

## Lab Steps

### 1. Start Magic and Open the Metal 3 Layout

#### Open the Magic Layout File

From the command line, run:

```
magic -d XR met3.mag
```
<img width="848" height="742" alt="image" src="https://github.com/user-attachments/assets/68598079-680a-49d2-9894-003cf01c1440" />


Alternatively, start Magic and use the menu:
- Select **File** > **Open** and load the `met3.mag` file from the file dialog.
<img width="719" height="716" alt="image" src="https://github.com/user-attachments/assets/303d4b37-043c-49e5-8ec8-e9ca5f791463" />


#### Observe the Layout View

You should see a layout window displaying several independent example geometries for Metal 3. Each example is labeled with a rule number corresponding to the Google SkyWater documentation. Areas with DRC errors will appear as white patches.
<img width="891" height="884" alt="image" src="https://github.com/user-attachments/assets/11cc1362-9d91-4f65-8f80-7cae61b9c080" />


### 2. Exploring the Rule Documentation

- Access the SkyWater rules documentation online.
- Navigate to **Design Rules** > **Periphery Rules**.
- Find the **M3** section using the table of contents to reference rule explanations.


**Note:** Rules flagged with "CPU" refer to process variants not present in SkyWater Sky130 and can be ignored.

### 3. Using Magic's Console and Hot Keys

Most Magic commands can be entered through the console window. To reach the console while in the layout window, type `:` or `;`. Then enter the desired command and press Return.

- Position the cursor box over any example.
- Press `:` to activate console focus.
- Type:

```
drc why
```
<img width="651" height="340" alt="image" src="https://github.com/user-attachments/assets/76910805-2ce3-4e79-905f-cd934d2aa72b" />


- Press **Return**. The violation rule at the cursor location will display in the console window.

### 4. Investigate Rule M3.4 (Via 2 Enclosure)

**Rule M3.4:** "VIA2 must be enclosed by Metal 3 by at least $0.065$ microns."

- All visible drawn layers except M3.4 are direct in the layout.

#### Visualize Contact Cuts for VIA2

To visualize how Magic creates contact cuts for VIA2:

1. Draw a large area of M3 contact:
   - Use the `paint` command
     - Or, in the GUI, hover over the M3 contact icon and either click the middle mouse button or hit the `p` key.
<img width="888" height="671" alt="image" src="https://github.com/user-attachments/assets/74ad0e23-2da6-4355-9560-8630f2427146" />


2. With the cursor box still around the drawn area, open the console (`:`) and enter:

```
cif see VIA2
```

- Black squares will appear showing the **contact cuts** (they represent the mask layer for VIA2).

#### Dismiss the Feedback Overlay

To clear this overlay, enter in the console:

```
feed clear
```

### 5. Verify Enclosure Rule Enforcement

The VIA2 must be enclosed by M3 by at least $0.065$ microns. Magic ensures this automatically:

- The internal grid is typically set to $0.01$ microns.

To **snap the cursor box to a finer grid (e.g., 5 nm)**, enter in the console:

```
snap internal
```
or
```
snap int
```

#### Measure the VIA2-M3 Edge Distance

- Place the cursor box between a contact cut and the drawn via edge.
- Type `:` to focus on the console, then type:

```
box
```

(or simply press the `b` key while focused on the layout)

- The measured width will display in the console, verifying the minimum enclosure (e.g., $0.065$ microns or larger).

*Because Magic auto-generates and centers these cuts, the enclosure is always $ \geq 0.065 $ microns—this rule is correct by design.*

### 6. Next Exercise and Recap

At this point, you've learned:
- How to open and inspect layouts in Magic.
- How to interpret DRC feedback and rule mapping.
- How Magic visualizes and enforces VIA contact requirements (e.g., M3.4).
- How to measure geometry to confirm design rule satisfaction.

The next step will proceed to analyzing a layer with an incorrect but simple-to-fix rule.

---
# Lab exercise to fix poly.9 error in Sky130 tech-file

This lab exercise demonstrates how to identify, troubleshoot, and fix rule implementation errors in an unfinished version of the tech file for an open-source hardware design environment. The aim is to locate a flagged design rule violation, verify its documentation and schematic, inspect the related design rule check (DRC) entries in the tech file, and update them to properly enforce spacing requirements between Poly resistors and Poly, diffusion, or tap layers. The steps include loading files in Magic, checking rule definitions, editing the tech file, reloading it, and verifying error detection.

## Lab Steps

1. **Find a Flagged Rule Implementation Error**

   - Open Magic and load the file `Poly.mag` to view rules and flagged errors.
   - Locate a flagged rule violation. In this exercise, select rule **Poly.9**.

   <img width="884" height="740" alt="image" src="https://github.com/user-attachments/assets/3df72a2b-cea3-4064-a8bd-a2b992c0efe9" />
  <img width="1852" height="873" alt="image" src="https://github.com/user-attachments/assets/e894d147-f51a-4501-9ec8-cc4923b57c02" />

2. **Verify Rule Description**

   - Refer to the website for the rule description for **Poly.9**.
   - Description: "**Poly resistor, spacing to Poly or spacing, no overlap to diff tap. The rule distance is $0.4$ microns.**"

   <img width="1161" height="65" alt="image" src="https://github.com/user-attachments/assets/cc431cac-db46-4638-ab97-b4e6b33e49b6" />


3. **Identify Layer Types in the Layout**

   - In Magic, select any drawn figure with the **S** key.
   - Then type the command: `what`
   - Observe output for layer type, e.g., "type Poly res" and "type Poly".

   <img width="982" height="595" alt="image" src="https://github.com/user-attachments/assets/b06222d5-faab-48c7-8ff0-6036f26e940f" />
  <img width="978" height="694" alt="image" src="https://github.com/user-attachments/assets/54e34d0e-7a91-4773-b093-34b972ff04c4" />


4. **Measure the Distance Relevant to the Rule**

   - Use the cursor box in Magic to measure the spacing between a Poly resistor and regular Poly.
   - press `b`
   - Example measurement: box shows $0.22$ microns.

   <img width="975" height="735" alt="image" src="https://github.com/user-attachments/assets/894bef3b-1480-4910-a39f-105b7b8ed59f" />


5. **Detect Missing or Incorrect Rule Implementation**

   - Note the violation is not being flagging correctly and must be fixed.


6. **Edit the Tech File to Add/Fix Rule**

   - Open the `sky130A.tech` file using your preferred editor (e.g., `vim`, `vi`, `nano`).

   ```
   vim sky130A.tech
   ```

   - The **DRC section** begins around line 4702.
   - Search for `Poly.9` to check rule definitions.

   ```
   /Poly.9
   ```

   <img width="733" height="489" alt="image" src="https://github.com/user-attachments/assets/b4f758f2-5aa1-4da2-8817-6f3cc07663d5" />


   - Identify current rule entries:
     - Defined: NP res and NSD, XHR Poly and diffusion.
   - Missing: Poly resistor to regular Poly spacing rule.

7. **Add the Spacing Rule in the Tech File**

   - Copy an existing rule and modify all `diff` references to `Poly` (or more precisely, use the alias for "all Poly non res" as found in the aliases section).
   - Update both rules to include all Poly resistor spacing cases.

   ```
   width uhrpoly 350 "uhrpoly resistor width < %d"
   spacing xhrpoly,uhrpoly,xpc alldiff 480 touching_illegal \
	"xhrpoly/uhrpoly resistor spacing to diffusion < %d (poly.9)"

   width uhrpoly 350 "uhrpoly resistor width < %d"
   spacing xhrpoly,uhrpoly,xpc allpolynonres* 480 touching_illegal \
	"xhrpoly/uhrpoly resistor spacing to diffusion < %d (poly.9)"
   ```

   <img width="665" height="212" alt="image" src="https://github.com/user-attachments/assets/e649c362-8e57-4af9-9da3-d0c2a78a5aee" />
  <img width="664" height="385" alt="image" src="https://github.com/user-attachments/assets/9aba5919-9352-458f-9d50-b57c1736bba9" />


8. **Save Changes to the Tech File**

   - Save the updated `sky130A.tech` file in your text editor.


9. **Reload the Tech File in Magic**

   - Without restarting Magic, reload the tech file from the command line:

   ```
   tech load sky130A.tech
   ```

   <img width="989" height="532" alt="image" src="https://github.com/user-attachments/assets/fb09f9a0-9e13-4acd-883e-e2827dc87666" />


   - You may see a pop-up warning about editing the tech file while Magic is running; proceed if only adjusting DRC rules.


10. **Force DRC Re-Check**

    - Command the DRC engine to re-check all design rules:

    ```
    drc check
    ```

    <img width="981" height="801" alt="image" src="https://github.com/user-attachments/assets/fc58e474-fe38-4e66-bfe8-3b7e23d61a2d" />


    - Verify that the error for Poly resistor to Poly spacing is now flagged correctly.

11. **Check Rule Coverage**

    - Observe that you've fixed the Poly to Poly resistor spacing, but the rule also covers Poly resistor to diffusion and tap.
    - If a diagram for Poly resistor to diffusion or tap is missing, create or update it to ensure all violations are checked.

---

# Lab exercise to implement poly resistor spacing to diff and tap

This lab guides you through the process of copying and verifying different types of diffusion and tap regions in a layout for poly resistors using Magic, an open-source VLSI layout tool. The goal is to identify and resolve design rule violations related to distances between poly resistors and diffusion or tap regions, implement corrections in the technology rule file, and verify the fixes with design rule checks (DRC). The main steps include copying resistor instances, drawing diffusion layers with correct colors, correcting rule file issues, and verifying with DRC.

## Lab Steps

1. **Copy poly resistor structures**

   - Copy three resistor structures to work with different diffusion and tap types.
   - These include n diffusion, p diffusion, n tap, and p tap layers.

   <img width="769" height="350" alt="image" src="https://github.com/user-attachments/assets/8c731fad-13bc-4668-be6f-13e92e46f1b7" />


2. **Identify and name diffusion and substrate layers**

   - The main layers are:
     - **n diffusion** (`endif`)
     - **p diffusion** (`pdif`)
     - **substrate in diffusion** (`in SD`)
     - **p substrate p diffusion** (`PSD`)

   - n-type diffusions are drawn in **green**; p-type diffusions are drawn in **tan**.


3. **Draw diffusion layers near poly resistors**

   - Place the diffusion layers close to the poly resistor devices to test spacing and rule enforcement.


4. **Draw n-well under p diffusion and n tap**

   - Draw an in-well layer beneath the p diffusion and n tap.
   - This helps mask irrelevant errors in the DRC checker, focusing only on relevant spacing violations.


5. **Review design rule check (DRC) results**

   - Perform a DRC in Magic to check for violations related to spacing between poly resistors and diffusion/tap.

   ```
   drc check
   ```

   - Observe:
     - XHR and HR poly resistors correctly display spacing violations.
     - The n poly resistor only flags a distance violation to n tap, not n diffusion.
---
