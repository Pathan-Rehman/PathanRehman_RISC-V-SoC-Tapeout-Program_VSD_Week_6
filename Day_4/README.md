# Table of contents

- [Sky130 Day 4 - Pre-layout timing analysis and importance of good clock tree](#sky130-day-4---pre-layout-timing-analysis-and-importance-of-good-clock-tree)
- [Lab steps to convert grid info to track info](#lab-steps-to-convert-grid-info-to-track-info)
- [Lab steps to convert magic layout to std cell LEF](#lab-steps-to-convert-magic-layout-to-std-cell-lef)
- [Introduction to timing libs and steps to include new cell in synthesis](#introduction-to-timing-libs-and-steps-to-include-new-cell-in-synthesis)
- [Introduction to delay tables](#introduction-to-delay-tables)
- [Delay table usage Part 1](#delay-table-usage-part-1)
- [Delay table usage Part 2](#delay-table-usage-part-2)
- [Setup timing analysis and introduction to flip-flop setup time](#setup-timing-analysis-and-introduction-to-flip-flop-setup-time)
- [Introduction to clock jitter and uncertainty](#introduction-to-clock-jitter-and-uncertainty)
- [Lab steps to configure OpenSTA for post-synth timing analysis](#lab-steps-to-configure-opensta-for-post-synth-timing-analysis)
- [Clock tree routing and buffering using H-Tree algorithm](#clock-tree-routing-and-buffering-using-h-tree-algorithm)
- [Crosstalk and clock net shielding](#crosstalk-and-clock-net-shielding)
- [Lab steps to run CTS using TritonCTS](#lab-steps-to-run-cts-using-tritoncts)
- [Setup timing analysis using real clocks](#setup-timing-analysis-using-real-clocks)
- [Hold timing analysis using real clocks](#hold-timing-analysis-using-real-clocks)

# Sky130 Day 4 - Pre-layout timing analysis and importance of good clock tree

# Lab steps to convert grid info to track info

This lab focuses on the extraction and verification of a LEF file from a custom standard cell (provided as a Magic `.mag` file), following guidelines required for integration with automated place-and-route (PnR) in open-source hardware flows. The main aim is to:

- Prepare and analyze the layout for proper port placement.
- Extract the necessary boundary and port information to generate a LEF file.
- Verify alignment of input/output ports to routing tracks as dictated by process guidelines.
- Demonstrate port/grid verification within Magic using actual PDK routing track specifications.

## Lab Steps

### 1. Examine the Provided Magic File

Start Magic using your `.mag` file (e.g., `sky130_inv.mag`).

```bash
magic -T sky130A.tech sky130_inv.mag &
```
<img width="729" height="160" alt="image" src="https://github.com/user-attachments/assets/99729d1b-79e2-4a55-932a-b908dab944c8" />


- Explore the loaded layout in Magic.
- Inspect boundary, power/ground rails, and port locations (input/output).
<img width="1854" height="870" alt="image" src="https://github.com/user-attachments/assets/97868cfd-4d5e-464c-b68c-42fe830ee894" />


### 2. Identify Required Data for LEF Extraction

LEF file must include:

- **Boundary box (inner box)**
- **Power and ground rails**
- **Input and output port locations**

Logic details and cell internals are not necessary for LEF export.


### 3. Review PDK Track Definitions

Locate the routing track file in the OpenLane PDK path:
```
/home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/openlane/sky130_fd_sc_hd
```
<img width="735" height="513" alt="image" src="https://github.com/user-attachments/assets/6168e340-5546-4daa-a207-304f953a6b87" />


Review track parameters, focusing on Metal1 (Li1) for standard cell ports:

- **Horizontal Track (X Direction):**
    - Offset: $0.23$ microns
    - Pitch: $0.46$ microns
- **Vertical Track (Y Direction):**
    - Offset: $0.00$ microns
    - Pitch: $0.34$ microns

### 4. Place Ports at Track Intersections (Standard Cell Guidelines)

**Standard cell requirements:**
- Input and output ports must be located at intersections of horizontal and vertical tracks.
- Cell width (horizontal) should be odd multiples of the horizontal pitch.
- Cell height (vertical) should be odd multiples of the vertical pitch.

For example:  
**Width** $= n_{X} \times 0.46$ microns (where $n_{X}$ is odd)  
**Height** $= n_{Y} \times 0.34$ microns (where $n_{Y}$ is odd)

### 5. Verify Grid Alignment in Magic

**Activate the layout grid in Magic:**

Press `G` in Magic to show/hide grid overlays.
<img width="430" height="718" alt="image" src="https://github.com/user-attachments/assets/5eb1de5c-104d-48a2-aa2f-4e84ded9f74d" />
<img width="386" height="707" alt="image" src="https://github.com/user-attachments/assets/de3ea4df-11df-44f7-8936-bcf448952602" />


**Adjust the grid to match routing tracks.**
Use the grid command in Magic:

```tcl
grid 0.46um 0.34um 0.23um 0.17um
```
Where:
- `0.46` = X spacing (pitch, microns)
- `0.34` = Y spacing (pitch, microns)
- `0.23` = X origin (offset, microns)
- `0.17` = Y origin (offset, microns)

<img width="981" height="343" alt="image" src="https://github.com/user-attachments/assets/f506e8b2-8450-48aa-bb87-911f82ae6d40" />
<img width="1852" height="872" alt="image" src="https://github.com/user-attachments/assets/2c3a773a-7b67-4aac-bb8b-ff4b0cc2ca3c" />

Zoom in and check boxes overlay on the layout.  
Verify that input and output ports coincide with intersections of the horizontal and vertical grid lines.  
Proper alignment ensures PnR tools can route to the ports directly.

<img width="1853" height="870" alt="image" src="https://github.com/user-attachments/assets/5e868cdd-cac2-4c75-890e-6192c422f916" />

---

# Lab steps to convert magic layout to std cell LEF

This lab demonstrates how to prepare and configure a standard cell layout for compatibility with PNR (Place and Route) tools by following industry requirements for cell dimensions, port definition, and LEF file extraction from Magic. The main steps involve configuring the cell width and height to standard grid multiples, defining ports and their roles, extracting the LEF file, and organizing design files for integration into a digital flow.

## Lab Steps

### 1. Check Standard Cell Width in X Pitch Multiples

- Verify that the **cell width** is an odd multiple of the X pitch for the layer (here, $X_{pitch} = 0.46\ \text{μm}$).
- Inspect the PR (Placement Region) boundary: count the number of inner grid boxes, confirming the width fits the odd multiple rule.
- Repeat this check for the **cell height** by counting grid boxes along the Y direction.

<img width="1851" height="893" alt="image" src="https://github.com/user-attachments/assets/a9dadde1-881a-4efc-976c-efead08da02f" />


### 2. Open Layout in Magic

- Load your standard cell layout in the Magic layout tool.

### 3. Define Ports (Pins) in Magic

For each port (e.g., A, Y, VPWR, VGND):

1. **Select the region:** Click the area where the port is to be defined.
2. **Select the layer:** Ensure the correct layer is active (e.g., locali for signal ports, metal1 for power/ground).
3. **Go to cell properties:**
   - Enter `Edit Cell` mode.
   - Choose `Text` to create the label.
4. **Add label:**
   - Set the text to your port name (e.g., `A`, `Y`, `VPWR`, `VGND`).
   - Set text size (e.g., 5.14 μm).
   - Attach label to the correct layer (e.g., stick label for signal pins on locali).
5. **Enable port:**
   - Check the `Port Enable` option.
   - Assign the **port number** (e.g., 0 for first, 1 for second, etc.). The port number determines order in the LEF file.

<img width="1854" height="867" alt="image" src="https://github.com/user-attachments/assets/647d806e-af29-410c-b667-7e04899e59c6" />
<img width="813" height="453" alt="image" src="https://github.com/user-attachments/assets/c0350504-ee93-44f7-9d43-c682c859d7a5" />

- Repeat for all required ports.

### 4. Define Port Purpose: Class and Use

For each port:

- **Select the port** (label) again.
- Specify the function:
  - **For output port Y:**
    - Set `Port Class` to `output`.
    - Set `Port Use` to `signal`.
  - **For input port A:**
    - Set `Port Class` to `input`.
    - Set `Port Use` to `signal`.
  - **For power port VPWR:**
    - Set `Port Class` to `power`.
    - Set `Port Use` to `power`.
  - **For ground port VGND:**
    - Set `Port Class` to `ground`.
    - Set `Port Use` to `ground`.
   
  <img width="715" height="636" alt="image" src="https://github.com/user-attachments/assets/c3b2cd52-1144-46ba-ab85-cb73554f1ce5" />

### 5. Save Design with Custom Name

- Save your cell with a descriptive name (e.g., `sky130_vsdinv.mag` or similar).

<img width="730" height="676" alt="image" src="https://github.com/user-attachments/assets/55ec268a-b5bb-4090-a07f-8edd74a907c2" />


### 6. Open/Load the Saved Cell in Magic

- Confirm your saved cell appears in the Magic tool with the correct name and all modifications.

<img width="730" height="512" alt="image" src="https://github.com/user-attachments/assets/ac59213a-203a-4dcb-bffe-7e54e3ece08b" />


### 7. Extract LEF File from Magic

- With your cell loaded, run the LEF extraction command:
  
  ```
  lef write
  ```
<img width="729" height="677" alt="image" src="https://github.com/user-attachments/assets/aebc2fcc-9b8e-4c54-a37b-50d762d61646" />

- If no name is specified, the LEF file will be saved with the same name as your cell layout (`sky130_vsdinv.lef`).

- Verify creation of the LEF file.

### 8. Inspect LEF File

- Open and review the generated LEF file to confirm:
  - All pins (ports) are present.
  - Properties (port order, class, use) match your definitions.

<img width="1021" height="807" alt="image" src="https://github.com/user-attachments/assets/a90ef2eb-0375-4002-98b9-e4f5066fcecd" />

All lab steps are now complete up to the integration phase for your digital design flow.

---

# Introduction to timing libs and steps to include new cell in synthesis

This lab demonstrates how to include a custom standard cell into the OpenLane digital design flow. The primary aim is to successfully integrate a newly characterized cell (with timing and .lef data) and verify its use through synthesis. The main steps involve preparing necessary library files, configuring OpenLane, running synthesis, and checking cell mapping.

## Lab Steps

### 1. Copy the Custom LEF File

Copy your custom LEF file to the target directory for OpenLane to access.

<img width="366" height="395" alt="image" src="https://github.com/user-attachments/assets/0b77b334-702b-422d-9744-34cbcc255d9b" />
<img width="1007" height="195" alt="image" src="https://github.com/user-attachments/assets/b148c13b-722c-407f-a865-79729101f806" />


### 2. Verify and Prepare Library Characterization Files

Ensure that your cell is fully characterized: create separate `.lib` files for fast, slow, and typical process corners. These files should specify all relevant cell timing and power parameters.

- Typical library: Both PMOS and NMOS are in typical state, defined at 20°C and 1.8V.
- Slow library: Both are slow, at 100°C and 1.6V.
- Fast library: Both are fast, at -40°C and 1.9V.

Check your `.lib` files for correct naming, process corners, temperature, and voltage.

```
ls PATH_TO_LIBRARY_FILES/*.lib
```
<img width="1188" height="125" alt="image" src="https://github.com/user-attachments/assets/9037a298-a215-4082-9d1a-a8ca9c47e8f2" />
<img width="1250" height="850" alt="image" src="https://github.com/user-attachments/assets/2822a493-41b4-49a4-b4e8-fa340ef7963e" />


### 3. Copy the Characterized Libraries

Copy the required `.lib` files (fast, slow, typical) into the appropriate directory in your OpenLane flow.

```
/home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src
```
<img width="830" height="221" alt="image" src="https://github.com/user-attachments/assets/76343ad3-4708-4879-94f3-adf4a9fb1bb9" />

### 4. Update the OpenLane Configuration

Edit your `config.tcl` (or equivalent configuration file) to include the new LEF and Liberty files.

- Add or update entries for:
    - `LEF_SYNTH` (LEF for synthesis)
    - `LIB_SYNTH` (Liberty for synthesis)
    - `LIB_MIN`, `LIB_MAX`, and `LIB_TYPICAL` (Liberty files for timing analysis)
    - Include an additional LEF as needed, for your custom cell

Ensure the path references are correct and point to the newly copied files, e.g.:

```tcl
set ::env(LEF_SYNTH) "$::env(DESIGN_DIR)/src/lef/NAME_OF_YOUR_CUSTOM_LEF.lef"
set ::env(LIB_SYNTH) "$::env(DESIGN_DIR)/src/lib/NAME_OF_YOUR_CUSTOM_TYPICAL.lib"
set ::env(LIB_MIN) "$::env(DESIGN_DIR)/src/lib/NAME_OF_YOUR_CUSTOM_SLOW.lib"
set ::env(LIB_MAX) "$::env(DESIGN_DIR)/src/lib/NAME_OF_YOUR_CUSTOM_FAST.lib"
set ::env(LIB_TYPICAL) "$::env(DESIGN_DIR)/src/lib/NAME_OF_YOUR_CUSTOM_TYPICAL.lib"
```
<img width="931" height="495" alt="image" src="https://github.com/user-attachments/assets/561a3144-c9d1-4df1-9adf-a7c28927a37b" />

Add any custom LEF files as extra includes if required by your configuration (e.g., for placement/routing).

### 5. Verify Directory Structure

Ensure your `src` folder contains:

- The design Verilog file(s)
- The SDC constraints
- The LEF file(s)
- The characterized Liberty files

```
ls $DESIGN_DIR/src/
```
<img width="984" height="269" alt="image" src="https://github.com/user-attachments/assets/ec24a6d9-885d-4337-a659-72874fbb8d1e" />


### 6. Modify the Configuration: Extra LEF Inclusion

If your flow requires an "extra LEF," add the path to your custom LEF in the config file under the correct variable.

Example:

```tcl
set ::env(EXTRA_LEFS) [list "$::env(DESIGN_DIR)/src/lef/NAME_OF_YOUR_CUSTOM_LEF.lef"]
```

### 7. Start the OpenLane Docker Environment

Invoke the OpenLane Docker container:

```
docker
./flow.tcl -interactive 
```
<img width="950" height="496" alt="image" src="https://github.com/user-attachments/assets/070b8a9f-ef84-4f5f-b295-f42e15f01fd7" />


### 8. Prep the OpenLane Design Flow

Navigate to the OpenLane prompt and prepare/package the design:

```
package require openlane 0.9
prep -design picorv32a
```

- This creates a run directory for the design.

#### To reuse an existing directory and overwrite results:

```
prep -design picorv32a -tag 31-10_10-51 -overwrite
```
<img width="932" height="554" alt="image" src="https://github.com/user-attachments/assets/47bfe2a7-38e9-4c37-ab8b-8d1e21a8d0bf" />
<img width="687" height="160" alt="image" src="https://github.com/user-attachments/assets/61f97bac-5fd0-4445-9eb4-8596c8675e2c" />


### 9. Run the Synthesis Step

Run the synthesis step to invoke mapping:

```
run_synthesis
```
<img width="649" height="710" alt="image" src="https://github.com/user-attachments/assets/e32241eb-134b-44fb-9f43-2f68f346c46e" />


- The synthesis will use the Liberty file specified for ABC mapping.

### 10. Check Mapping and Results

After synthesis completes, examine the synthesis logs/reports to ensure your custom cell was mapped.

Look for output indicating instances of your cell:

```
# output from synthesis logs
<NUMBER> instances of <CUSTOM_CELL_NAME>
```

- Example: "2201 instances of our custom cell"

### 11. Review Timing Reports

Check the timing reports for slack and any violations (such as setup/hold violations).

- Large negative slack indicates a violation that must be corrected, either in the constraints or by improving the cell or flow setup.

```
# output from timing report
Slack: -VALUE (VIOLATION)
```

---
# Introduction to delay tables

This document explores the **theoretical foundation of clock gating** in digital circuits, examining how AND and OR gates can be utilized to control clock signal propagation through the clock tree. It discusses the rationale, benefits, and implications of clock gating, as well as the critical timing characteristics and modeling of buffering elements within a typical clock tree.

<img width="879" height="539" alt="image" src="https://github.com/user-attachments/assets/f4ff5176-937f-4eba-b7f7-83a439c92f77" />


## Core Concepts

### Clock Gating with Logic Gates

- **AND gate as a clock enable:** If the enable pin of the AND gate is set to logic 1, the clock signal passes through; if set to logic 0, the clock is blocked. This mechanism selectively enables or disables clock propagation based on logic conditions.

- **OR gate as a clock block:** In contrast, for an OR gate, when its enable pin is at logic 0, clock flows through; if at logic 1, the clock is blocked and does not propagate further. Both gates serve as key elements for controlling dynamic power consumption in synchronous digital systems.

<img width="1281" height="500" alt="image" src="https://github.com/user-attachments/assets/b0ccbb83-543c-47d4-a0f7-bdb5417bcc6e" />


### Power Savings through Gating

- By controlling clock distribution, **clock gating** minimizes unnecessary switching events and short-circuit currents, effectively reducing dynamic power consumption during periods when parts of the circuit do not require clock activity.

- This technique is widely adopted in complex integrated circuits to maximize energy efficiency and reduce heat generation by limiting clock signal reach to only the required functional blocks.

<img width="1547" height="888" alt="image" src="https://github.com/user-attachments/assets/f205f120-e045-4aec-850d-294209276310" />


### Impact on Clock Tree Structure

- In typical clock trees, buffers are used to drive fanout loads. Previous designs may have split loads among multiple buffers for optimal performance and load balancing.

- Introducing a gating element (e.g., replacing a buffer with an AND gate) demands careful consideration:
  - Clock gating must not compromise critical properties like **skew**, **pulse width**, and **latency**.
  - Maintaining signal integrity across the clock tree levels, after gating insertion, is essential for reliable circuit operation.

- The discussion highlights the need to review timing characteristics before replacing traditional buffers with gating elements.


### Buffer Timing Characteristics

- Each buffer in a clock tree has associated **input transition times** and **output load capacitance**:
  - These values are not constant across the tree and can vary significantly, e.g., output capacitance at node A may be 60 femtofarads, while nodes B and C are 50 femtofarads.

- Inputs to buffers experience transition time variations (e.g., ranging from 10 picoseconds to 100 picoseconds), while output loads may also span a wide range (e.g., 10 to 100 femtofarads across the circuit).

- Because delays depend on these parameters, **delay diversity arises**, making it necessary to formally characterize buffer behavior.


### Delay Table Modeling

- **Delay tables** are constructed to capture the relationship between buffer delay, input transition, and output load:
  - For each buffer type (specified by physical size and transistor threshold voltage), delay is measured across multiple input transition times and output load scenarios.

- A delay table is thus a 2D matrix:
  - One axis: input transition times (e.g., 20 ps, 40 ps, ...)
  - Other axis: output load capacitance (e.g., 10 fF, 30 fF, 50 fF, ...)
  - Each matrix element contains the delay observed for a specific transition/load pair.

- This table serves as the **timing model** of the buffer, essential for simulation, synthesis, and accurate timing analysis.

- For buffer size $S$, threshold voltage $V_{th}$, and input transition $t_{in}$, output load $C_{load}$, the delay $D$ is conventionally modeled as:

  $D = f(S, V_{th}, t_{in}, C_{load})$


## Key Points

- **Clock gating** allows conditional propagation of the clock, enabling dynamic control over which parts of the circuit are active, resulting in significant power savings.
- Strategic use of **AND/OR gates** for clock gating can block or enable the clock signal based on an enable/control signal.
- Inserting gating logic should not degrade clock tree metrics such as **skew**, **pulse width**, and **latency**; these must be verified post-modification.
- **Buffer timing characteristics** depend on both input transition times and output load capacitance. These factors vary throughout the clock tree.
- **Delay tables** are essential for precise timing modeling, as they record buffer delays over a range of conditions, forming the basis for static timing analysis in digital design.

--- 
# Delay table usage Part 1

This documentation explains the **theoretical concept of delay tables** in digital circuit design, focusing on their purpose, construction, and relationship with gate sizing and performance. Delay tables are fundamental structures used to model the timing characteristics of logic cells, especially buffers, as a function of **input transition (slew)** and **output load (capacitance)**. Understanding delay tables is crucial for static timing analysis and for optimizing digital circuit paths.

## Core Concepts

### Delay Tables: Purpose and Structure

**Delay tables** represent the propagation delay of standard cells (such as buffers, AND, OR, XOR gates, etc.) under varying electrical conditions. Each cell has a unique delay table that maps:
- Different **input slew rates** (the speed of the input signal transition)
- Different **output load capacitances** (the capacitive load driven by the cell output)
to distinct delay values.

The entries of the delay table are typically organized as a grid:
- **Rows**: various input transition times (in picoseconds)
- **Columns**: various output load capacitances (in femtofarads)
- **Table entry**: delay value corresponding to a specific combination

When simulating or analyzing a circuit, the delay for a transition is determined by locating the corresponding row and column in the delay table.

<img width="1513" height="882" alt="image" src="https://github.com/user-attachments/assets/0b4c4a43-092b-40e6-9813-3e66bff5ce80" />


### Relationship Between Cell Size and Delay

**Cell sizing** directly impacts delay characteristics. Buffers (and other cells) can come in multiple sizes (e.g., size 1, size 2, up to size 10).
- Increasing the **width (W)** of the transistors (PMOS and NMOS) inside a cell reduces their resistance, which, in turn, affects the overall RC time constant and thereby the delay.
- Each unique size of a cell has its own delay table, as the intrinsic resistance ($R$) and capacitance ($C$) parameters change when device dimensions change.

Delay for a cell can be generally characterized by the RC delay formula:
- $Delay = k \cdot R \cdot C$
where $k$ is a proportionality constant, $R$ is resistance, and $C$ is capacitance.

Thus, larger buffers (greater $W/L$ ratio) have lower $R$ but might drive larger $C$, so the delay/size relationship is non-linear and must be empirically characterized.


### Table Lookup and Extrapolation

In practice, the exact **input slew** and **output load** for a cell may not exist as a direct entry in the precomputed table.
- If the required values **fall between two known table points**, techniques such as **linear interpolation** or more advanced numerical methods are used to estimate the delay.
- The process involves constructing an interpolation equation using the nearest data points and substituting the required input slew and output load values to derive the estimated delay.

This approach is essential for accurate, automated timing predictions within EDA tools and timing analysis engines.


## Key Points

- **Each logic cell** (e.g., buffer, AND, OR) is characterized by its own delay table, indexed by input transition and output load.
- **Device size** modifications alter intrinsic resistance and capacitance, thus necessitating a dedicated delay table for each size variant.
- The **delay value** at any given point is acquired by table lookup, and if an exact match is unavailable, **interpolation or extrapolation** techniques deduce the correct value.
- The aggregate **latency** at a path’s endpoint in a circuit is obtained by summing up the respective delays of each cell in the path according to their delay tables.
- Accurate modeling of delay tables is fundamental for static timing analysis, robust digital design, and optimal path selection.

---

# Delay table usage Part 2

This document explains the theoretical foundations and key concepts behind calculating **buffer delay, latency, and clock skew** in digital circuits. It describes the importance of **consistent loads and identical buffers** at each stage of a clock tree, illustrating how deviations can lead to nonzero skew and impact timing integrity. The discussion also introduces the idea of **power-aware clock tree synthesis**, where inactive sections can selectively gate their clock for improved efficiency.

<img width="1512" height="881" alt="image" src="https://github.com/user-attachments/assets/03eb9da0-915e-4c48-9f50-152f45ea2abe" />
<img width="1566" height="881" alt="image" src="https://github.com/user-attachments/assets/cf46dd70-3633-4de5-aefe-861618509435" />
<img width="827" height="459" alt="image" src="https://github.com/user-attachments/assets/e8357a3c-7af2-426d-988f-3ffe9bddf822" />


## Core Concepts

### Buffer Delay and Transition

- **Buffer delay** is determined by both the input transition time and the output capacitance (load) that the buffer drives.
- The buffer's output transition time itself is a function of its input transition and the output load.
- Delay characteristics for standard cells are typically described in a **delay table**, with axes for input transition and output capacitance. The appropriate delay value is found at the intersection corresponding to these parameters.
- If two buffers of identical type and load both receive the same input transition, their delays will be equal, simplifying analysis and design considerations.

$Delay = f(input\ transition,\ output\ load)$


### Latency and Path Analysis

- **Latency** in a path is the sum of cell (buffer) delays along that path, assuming wire (interconnect) delays are neglected for the theoretical analysis presented.
- For identical structures and conditions, two symmetric paths will exhibit the same total latency, leading to balanced timing.

$Latency_{path} = Delay_{buffer1} + Delay_{buffer2} + \ldots$


### Clock Skew

- **Clock skew** is defined as the difference in clock arrival times at two endpoints.
- If all buffer types and loads are identical at every tree level, the skew between endpoints remains zero.
- If load or buffer type differs between branches, delays diverge and skew becomes nonzero.

$Skew = Latency_{endpoint1} - Latency_{endpoint2}$

Example:
- If both paths use the same delay value $Y_{15}$, $Skew = 0$
- If one uses $Y_{15}$ and the other $Y_{17}$, $Skew = Y_{15} - Y_{17}$


### Importance of Identical Loads and Buffer Types

- **Identical loads at each level:** Ensures all branches of the clock tree propagate signals at the same rate, minimizing skew.
- **Identical buffer types at each level:** Required so that delay tables yield equivalent values under matched conditions, which would not be the case if mixed buffer sizes/types were present.

Deviation from these conditions at any level propagates mismatches downstream, greatly increasing complexity and timing errors in large designs.


### Power-Aware Clock Tree Synthesis

- Certain circuit sections may only need to be clocked under specific conditions. To save power, the clock can be gated (disabled) for inactive branches.
- This methodology is key in large-scale designs with blocks that are often idle or have special operating states.


## Key Points

- **Buffer delay is jointly determined by the input transition and output load.**
- Use delay tables to find exact delay for given input transition and load combinations.
- For minimum clock skew, ensure every path in a clock tree:
  - Has the same sequence and type of buffers.
  - Drives the same load at every analogous node.
- Non-idealities (e.g., mismatched loads or buffer types) result in nonzero skew, which can accumulate into major timing issues.
- At the scale of millions of clocked elements, even small mismatches become significant, impacting static timing analysis through setup and hold violations.
- Designing for power efficiency involves clock gating: disabling clock delivery to inactive blocks to minimize dynamic power usage.

---

#  Setup timing analysis and introduction to flip-flop setup time

This documentation focuses on **setup timing analysis** with an emphasis on *ideal clocks* and *single-clock scenarios* in synchronous digital circuits. It explains essential timing constraints, fundamental concepts of sequential element behavior, and the formation of core setup timing equations, all relevant to static timing analysis (STA) in open-source hardware design.

---

## Core Concepts

### Setup Timing Analysis

**Setup timing analysis** evaluates whether data can reliably propagate from a launch flip-flop (flop) through combinational logic to a capture flop within a single clock period, considering various sources of delay.


#### Ideal Clock Assumption

An *ideal clock* scenario assumes the clock distribution network does not introduce any additional delay or skew. The clock edges reach all synchronous elements (flops) instantaneously and simultaneously.

<img width="1499" height="893" alt="image" src="https://github.com/user-attachments/assets/b2c56a95-8530-48a9-bd92-4c88c19e8fd0" />


#### Single-Clock Timing Path

Focus is given to the simplest STA scenario: **both launch and capture flops are triggered by the same clock**. For a clock frequency $f$, the clock period $T$ is $T = 1/f$. For example, a 1 GHz clock results in $T = 1$ ns.

* **Launch Flop**: Where data departs at the clock edge.
* **Combinational Logic**: The logic gates and interconnects where data propagates.
* **Capture Flop**: Where data must arrive and be sampled at the next clock edge.
  
<img width="1593" height="562" alt="image" src="https://github.com/user-attachments/assets/9ff4ddf7-b179-4ee0-8225-cbce04eca64b" />

#### Timing Window

The timing analysis occurs in the time window between two successive clock edges (typically from time $0$ to $T$). The data launched at the first edge must arrive and settle at the capture flop before the subsequent clock edge.

### Sequential Element (Flip-Flop) Internals

Flip-flops are not perfect black boxes; **internal logic** such as multiplexers (MUXes), MOSFET-based gates, and internal feedback create real, measurable delays.

- **MUX Delay**: Data passing through the internal MUXes of a flop adds propagation delay.
- **Resistive and Capacitive Effects**: Contribute to the overall delay from input ($D$) to output ($Q$).

---

## Key Points

### Primary Setup Time Constraint

The most basic setup constraint ensures **combinational delay doesn't exceed the clock period**:

$combinational \ delay < T$

If the path delay exceeds $T$, the circuit cannot function at the target frequency.


### Effect of Flip-Flop Setup Time

**Setup time ($S$)** is the *minimum time interval before the active clock edge* during which data at the $D$ input must be stable for correct sampling.

- The **effective time available** for data propagation from launch to capture is reduced by the setup time of the destination flip-flop.
- The setup timing constraint is refined to:

$combinational \ delay < T - S$

Where:
- $T$ = clock period
- $S$ = setup time required by the capture flop

This adjustment ensures that data is settled and the capture flop can reliably latch the input.


### Practical Example

For a $1$ GHz clock ($T = 1$ ns) and a capture flip-flop with $S = 10$ ps:

- Maximum allowable combinational delay: 
$combinational \ delay < 1 \ ns - 0.01 \ ns = 0.99 \ ns$

Any increase in required setup time directly reduces the margin for combinational delay, potentially impacting the maximum system frequency.


### Next Steps

The analysis here introduces only the basic setup constraint and the addition of the flip-flop’s setup time. Further practical complexities (such as clock skew and hold time concepts) are to be considered in subsequent extensions.

---
# Introduction to clock jitter and uncertainty

This document explains the **theoretical foundations of setup timing analysis** in digital circuits, focusing on the calculation of acceptable combinational delay and the practical implications of clock jitter and uncertainty. The concepts detailed here provide the basis for post-implementation static timing analysis and help designers ensure correct functionality at the intended clock speed.

## Core Concepts

### Setup Timing Constraint

- The **setup timing constraint** ensures that the data at the input of a flip-flop is stable for a minimum period before the arrival of the clock edge.
- The nominal condition for setup timing can be expressed as:
  - $combinational\ delay < clock\ period - setup\ time$
- This equation defines the maximum allowable delay for all logic and routing elements between two flip-flops to ensure correct data sampling.

<img width="1562" height="882" alt="image" src="https://github.com/user-attachments/assets/9f424e69-f1ae-411b-afa2-512e79744b8f" />


### Clock Jitter and Uncertainty

- **Clock jitter** refers to temporary variations in the timings of clock edges caused by imperfections in the clock generation circuitry, such as phase-locked loops (PLLs). Jitter causes clock edges to occasionally arrive slightly earlier or later than expected.
- These variations affect the precise durations between clock edges, effectively reducing the available time window for reliable data transfer.
- To model both jitter and other sources of clock edge variation, the concept of **uncertainty** is introduced as a parameter in the timing equation.

- The modified setup timing constraint incorporating uncertainty becomes:
  - $combinational\ delay < clock\ period - setup\ time - setup\ uncertainty$

<img width="1572" height="882" alt="image" src="https://github.com/user-attachments/assets/357b9f5e-a00f-459a-a2db-6ecc62bfadb3" />


### Combinational Path Delay

- The **combinational path delay** represents the time taken for a signal to propagate through logic elements and wires connecting the launch and capture flip-flops.
- This delay is the sum of:
  - Delay from the launch flip-flop clock to its output ($clock\_to\_Q\ delay$)
  - Wire/routing delays between cells
  - Logic cell delays ($cell\ delay$)
  - Delay from the last logic element to the capture flip-flop

- The requirement for reliable operation is that the total combinational path delay satisfies the setup timing constraint equation.

<img width="1733" height="886" alt="image" src="https://github.com/user-attachments/assets/68e35443-6d9b-4133-a3c1-22844330b87b" />
<img width="1625" height="891" alt="image" src="https://github.com/user-attachments/assets/bad8c349-6262-43d4-b28d-47059d3e7197" />
<img width="1657" height="879" alt="image" src="https://github.com/user-attachments/assets/09b4bc7a-e363-43ed-a3bf-4a8caa1e84c8" />


## Key Points

- **Setup timing analysis** ensures that signal propagation between flip-flops completes before the next clock edge, considering setup time and clock period.
- **Clock jitter** and other non-idealities in clock generation require designers to account for additional timing margins using an uncertainty parameter.
- The refined timing equation for setup analysis is:
  - $maximum\ path\ delay < clock\ period - setup\ time - uncertainty$
- When performing timing analysis, the specific values of clock period, setup time, and uncertainty (e.g., $1 ns$, $10 ps$, $90 ps$) directly determine the allowable maximum combinational path delay, as in:
  - $combinational\ delay < 1 ns - 10 ps - 90 ps = 0.9 ns$
- For single-clock domains, this equation guides how timing paths should be validated for correct placement and routing.
- For multiple clock domains or more complex scenarios, additional considerations such as asynchronous clock skew and inter-domain jitter must be addressed (to be discussed in subsequent sections).

---

# Lab steps to configure OpenSTA for post-synth timing analysis

This lab aims to **verify and reduce timing slack violations** after place-and-route (PNR) using the open-source OpenLane VLSI flow and perform static timing analysis (STA) using OpenSTA. You will prepare configuration files, define design constraints such as units, libraries, SDC (Synopsys Design Constraints), and run timing analysis to observe and interpret slack values. Key steps involve editing configuration files, confirming correct source files, updating constraint values, and executing STA commands to report slack and path information.

## Lab Steps

### 1. Prepare Your Timing Analysis Environment

- Navigate to your OpenLane project directory.
- Identify or create a file for pre-layout STA setup (commonly named `pre_sta.conf` or `pre.conf`).

```sh
ls
```

- Open `pre_sta.conf` for editing.

```sh
gvim pre_sta.conf
```
<img width="1309" height="256" alt="image" src="https://github.com/user-attachments/assets/892bd0aa-69e2-463f-87b2-e51f38c70194" />


- Example contents to initialize:
  - Set time units.
  - Read maximum and minimum timing libraries.
  - Specify Verilog netlist location, as created by the synthesis stage.
  - No extra netlists are created after placement but before CTS (Clock Tree Synthesis).

```tcl
# Set time units
set_timing_units -time ps 

# Read maximum (slow) and minimum (fast) libraries
read_liberty max_library.lib
read_liberty min_library.lib

# Read synthesized Verilog netlist
read_verilog ./results/synthesis/top.v
```

### 2. Understand Netlist and Synthesis Artifacts

- Synthesis results in one netlist Verilog file, found under synthesis results.

```sh
ls ./results/synthesis
# Should show top.v or similar netlist file
```

- After placement, no new netlist is produced. New netlists are only generated at later steps (e.g., in CTS when buffers for the clock tree are inserted).

### 3. Set Up and Inspect SDC File

- Locate SDC (constraint) file. This could be named `my_base.sdc` or similar.

```sh
ls ./constraints
nano ./constraints/my_base.sdc
```

- Check and edit the SDC file to define clock, driver cells, capacitive load, and timing exceptions as needed.

- Ensure all necessary variables are defined, since analysis is being run outside of OpenLane's internal environment.

```tcl
# Example: Define driving cell
set_driving_cell -lib_cell INVX1 -pin Y

# Example: Set input capacitance (make sure units align with your timing libraries)
set_load 17.65
```
<img width="822" height="515" alt="image" src="https://github.com/user-attachments/assets/b0a01c9d-54c5-41a9-a1bc-bbceefc880e7" />


### 4. Update Capacitance Load

- Determine the correct input capacitance for your standard cell (e.g., an inverter).
- Find the input capacitance value in the Liberty file: typical input pin might have $0.017$ (pF) or $17.65$ (fF).
- Update SDC or config file with this value:

```tcl
set_load 17.65
```

### 5. Confirm Consistency With Synthesis

- Replicate synthesis-stage settings in your analysis setup for accurate comparisons.
- Compare and reference constraints used by OpenLane.
- Define all relevant environmental and analysis variables in your pre-layout config and SDC files.

### 6. Run Static Timing Analysis (STA)

- Use the OpenSTA command-line interface or appropriate invocation (depends on your tool setup):

```sh
sta pre_sta.conf
```
<img width="733" height="117" alt="image" src="https://github.com/user-attachments/assets/6c6fc040-9666-4667-aa59-1e7cb541f0c4" />

### 7. Inspect Reported Slack

- Use report commands to check slack and timing path data.

```tcl
report_checks
report_trans
```
<img width="90" height="44" alt="image" src="https://github.com/user-attachments/assets/109e552d-9542-4f6c-afec-494997057cb3" />


- You should observe a slack value close to $-3$ (units depending on timing setup).

---

# Clock tree routing and buffering using H-Tree algorithm

Clock Tree Synthesis (CTS) is a critical stage in the physical design flow of integrated circuits, primarily focused on distributing the clock signal efficiently and precisely to all sequential elements such as flip-flops. Its main objectives are minimizing clock skew, balancing insertion delay, and maintaining signal integrity throughout the design. Proper CTS ensures the clock arrives at each clock sink nearly simultaneously, which is crucial for meeting timing requirements and achieving reliable circuit operation.

## Core Concepts

### Physical Clock Network and Skew

In digital designs, the clock signal must reach multiple flip-flops. If this distribution is not carefully planned, signal paths may have different physical lengths and electrical properties, causing significant variations in clock arrival times, known as **clock skew**. Clock skew can be expressed as:

$skew = T_{arrival, max} - T_{arrival, min}$

where $T_{arrival, max}$ and $T_{arrival, min}$ are the latest and earliest clock arrival times at different flip-flops.

Minimizing skew is essential because excessive skew can lead to setup and hold violations, undermining circuit functionality and performance.

<img width="1689" height="845" alt="image" src="https://github.com/user-attachments/assets/39be2f33-d7c1-4116-ad68-33608d8d0bd1" />


### Naïve vs. Structured Clock Routing

A naïve clock routing approach simply connects the clock source to all flip-flops without considering path delays, resulting in large and unpredictable skews due to variations in wire resistance and capacitance. This can cause unbalanced timing and unreliable circuit operation.

Structured approaches, such as the **H-Tree algorithm**, are used to achieve balanced clock distribution. In the H-Tree, the clock path is split symmetrically at each stage, ensuring that signals reach all endpoints (flip-flops) at nearly the same time. The geometric center and recursive partitioning guide the tree’s construction, consistently minimizing wire length and balancing electrical properties.

<img width="1381" height="714" alt="image" src="https://github.com/user-attachments/assets/6c174129-251a-4306-a602-8519a8a45523" />
<img width="1685" height="819" alt="image" src="https://github.com/user-attachments/assets/5eec01ef-af8b-484f-a1f9-43afc888c120" />


### Clock Tree Buffering

Long interconnects introduce resistance ($R$) and capacitance ($C$), forming an **RC network** that distorts and attenuates the clock signal. The delay ($t_{delay}$) through an RC line can be approximated as:

$t_{delay} \approx 0.38 \cdot R \cdot C$

As clock signals travel longer distances, the associated RC load increases, causing poor signal transitions and degraded waveform integrity. To address this, **buffers (repeaters)** are inserted along the clock paths. Buffers restore signal strength and reproduce clean transitions, ensuring that the clock waveform remains sharp and accurate as it reaches distal endpoints.

A key requirement for clock buffers (as opposed to standard data-path buffers) is *symmetric rise and fall times* to maintain the duty cycle and avoid introducing additional skew or jitter.

<img width="1643" height="823" alt="image" src="https://github.com/user-attachments/assets/008fc68a-25e4-4d36-93f3-272a1e88f8f7" />
<img width="1683" height="773" alt="image" src="https://github.com/user-attachments/assets/5e081492-fd39-4912-8a37-3f9a20b22a9e" />
<img width="1674" height="847" alt="image" src="https://github.com/user-attachments/assets/ebdaed83-2f20-4ac3-9262-5643563eabb2" />


### Clock Tree Synthesis Steps

The primary steps in CTS involve:

- **Clustering**: Grouping flip-flops based on their physical locations to help guide balanced tree construction.
- **Balancing**: Using algorithms like the H-Tree to ensure signal timing is uniform to all sinks.
- **Buffer Insertion**: Placing buffers at strategic points to drive loads and regenerate the clock waveform.
- **Skew and Insertion Delay Minimization**: Iteratively adjusting buffer placement and tree shape to reduce skew and overall propagation delay.
- **Signal Integrity**: Ensuring clock edges are preserved without excessive distortion due to interconnect parasitics.


## Key Points

- **Clock skew** is the difference in arrival times of the clock edge at various flip-flops and should be minimized for reliable, high-performance circuits.
- **Naïve clock routing** leads to unpredictable and potentially large skews due to different path lengths and electrical loads.
- Structured tree algorithms, especially **H-Tree**, are commonly used to achieve balanced signal distribution.
- **Buffers** are essential to overcome wire resistance and capacitance, maintaining sharp and reliable clock transitions throughout the network.
- Clock buffers specifically require **matched rise and fall times** to ensure waveform symmetry and reduce duty cycle distortion.
- The CTS process involves placement-aware tree generation, clustering, balancing, buffer insertion, and skew minimization.
- The quality of the clock tree directly impacts both **timing closure** and power consumption in the design.

---
# Crosstalk and clock net shielding

This documentation covers theoretical aspects of **clock net shielding** and the implications of crosstalk in digital VLSI physical design, with a focus on clock tree robustness and timing integrity. Key concepts include clock tree synthesis (CTS), clock skew, crosstalk phenomena, and shielding techniques to protect critical nets like clocks.

## Core Concepts

### Clock Tree and Zero Skew

A **clock tree** is strategically constructed to distribute the clock signal to synchronous elements with minimal latency difference (skew). The goal is zero skew, defined as:

$skew = (latency_{clk \rightarrow FF1}) - (latency_{clk \rightarrow FF2})$

This ensures consistent timing for data launch (source flip-flop) and capture (destination flip-flop).

### Crosstalk and the Need for Shielding

**Crosstalk** refers to unintended interactions between nearby nets, typically caused by capacitive coupling. Clock nets, being critical for timing, are susceptible to crosstalk, which can undermine the zero skew achieved during clock tree synthesis. Crosstalk can be exacerbated if other signals (aggressor nets) are switching near the clock (victim) net.

If the coupling between the victim (clock net) and aggressor (adjacent net) is significant, unwanted behaviors such as glitches or timing (delay) perturbations can occur.

<img width="1687" height="832" alt="image" src="https://github.com/user-attachments/assets/67eb4a5a-243f-46b1-8518-3b7daa8b6916" />


#### Shielding Principle

**Shielding** involves routing non-switching nets (VDD or ground) on either side of the clock net, effectively "encapsulating" it and breaking the coupling path between aggressors and the victim. Shields are connected to fixed potentials to prevent signal-induced switching, reducing crosstalk susceptibility.


### Major Crosstalk-Induced Problems

1. **Glitch**  
   A glitch is a short, unintended voltage spike on the victim net due to switching in a neighboring aggressor net. If critical signals like reset or clock are glitched, it can lead to catastrophic failures, such as unintentional memory resets.

   - Glitches are hazardous in safety-critical or mission-critical ICs, where accidental changes can disrupt overall system operation.

   <img width="859" height="594" alt="image" src="https://github.com/user-attachments/assets/e0bddf0d-eea7-4153-9751-154924fb3699" />


2. **Delta Delay**  
   Delta delay is the incremental delay introduced when both the aggressor and victim nets switch. Coupling capacitance can either speed up or slow down the victim's transitions, altering cell and path delays.

   For a cell supposed to have delay $D$, after delta delay, it becomes $D + \delta$.

   This changes total path latency:
   $L_{total,after} = L_{original} + \delta$

   Skew is no longer zero:
   $skew_{after} = L_1 - (L_2 + \delta)$

   Repeated effects across many buffers amplify the timing error, potentially violating timing margins in large clock networks.

   <img width="1510" height="880" alt="image" src="https://github.com/user-attachments/assets/b33d3472-12e4-4fc2-a3c5-526bb50930c1" />


## Key Points

- **Clock trees are designed to ensure near-zero skew, minimizing clock arrival differences across sequential elements.**
- **Crosstalk due to capacitive coupling from adjacent switching nets can disrupt clock signal integrity, causing glitches or additional delays.**
- **Shielding with VDD or ground rails beside clock nets is an effective countermeasure, as these non-switching signals block coupling paths.**
- **Glitches can cause unintended activation of critical signals, leading to system failures, emphasizing the importance of robust shielding.**
- **Delta delay induced by crosstalk alters cell/proper delays, directly impacting clock skew and overall timing closure.**
- **It's not feasible to shield all nets due to routing resource limits; priority is given to critical nets like clocks and, where possible, essential data paths.**
- **A robust clock tree with adequate shielding forms the prerequisite for reliable timing analysis with real clocks.**

---

# Lab steps to run CTS using TritonCTS

This lab focuses on optimizing a digital design’s timing by modifying buffers to reduce the worst negative slack below 1, then integrating these changes into the design flow using OpenLane. The procedure includes identifying buffers causing excessive delay, replacing them with higher drive strength options, updating the design netlist, and re-running floorplan, placement, and Clock Tree Synthesis (CTS) to propagate these improvements.

Main steps:
- Identify buffers affecting slack.
- Replace buffers with higher drive strength versions.
- Verify slack improvement.
- Write modified netlist to file and update the synthesis output.
- Re-run floorplan, placement, and CTS to incorporate changes.
- Analyze changes to congestion, placement iterations, area, and quality of results after modifications.

## Lab Steps

1. **Analyze Current Worst Slack**

   Review the reported worst slack (e.g., $-1.05$). If above $-1$, look to improve further.
   
 <img width="635" height="702" alt="image" src="https://github.com/user-attachments/assets/9fbab736-3332-44b8-8fef-56a9608b3a2e" />


2. **Identify Buffer to Replace**

   Inspect specific delay figures. Locate a buffer (e.g., instance `47972`) with:
   - Fanout: $4$
   - High capacitance.
   - High delay contribution.
   
   <img width="576" height="91" alt="image" src="https://github.com/user-attachments/assets/c67c49f0-d5bc-4f89-a15c-902ca40df4dc" />


3. **Replace Buffer with Higher Drive Strength**

   Use the OpenSTA or tool interface to select and replace the chosen buffer with a higher drive variant (for example, from a lower to a BUFF8).

   ```shell
   replace_cell 47972 BUFF8
   ```
   <img width="364" height="42" alt="image" src="https://github.com/user-attachments/assets/65fe0844-9734-4512-a3a9-93029d54d702" />


4. **Check Slack After Buffer Replacement**

   Run timing report commands to check for updated slack.
   
   ```shell
    report checks -fields {net cap slew input_pin}
   ```
   <img width="411" height="19" alt="image" src="https://github.com/user-attachments/assets/14b23a14-9b9a-4cee-8d93-784cf0387d43" />

   Observe if the worst slack drops below $-1$ (e.g., to $-0.99$ or lower).

5. **Repeat for Additional High-Delay Buffers**

   - Find other buffers (e.g., another with fanout $4$, high capacitance).
   - Replace with higher drive (e.g., BUFF4, BUFF8).
   
   ```shell
   replace_cell <instance_name> BUFF4
   ```
   or
   
   ```shell
   replace_cell <instance_name> BUFF8
   ```
<img width="384" height="44" alt="image" src="https://github.com/user-attachments/assets/e4e80c82-4334-49e0-89b3-e916296f9c8d" />


   Rerun timing checks.

   ```shell
   report_checks
   ```

   Review improvement (e.g., worst negative slack from $-1.87$ to $-0.77$).

6. **Write Modified Netlist to File**

   Use the OpenSTA or tool command to export the new netlist with all buffer modifications.
   
   ```shell
   write_verilog ./runs/<run_folder>/results/synthesis/<filename>.v
   ```

   Overwrite previous synthesis Verilog file (e.g., `mybase.sdc` location).

7. **Verify Netlist Modifications**

   Search the modified netlist to confirm that the affected instances have the new buffer types.

   ```shell
   grep <instance_name> ./runs/<run_folder>/results/synthesis/<filename>.v
   ```

8. **Continue OpenLane Digital Flow**

   Return to OpenLane and continue from the state with improved slack.

   - Make sure the new netlist is present before proceeding.
   - Do **NOT** rerun synthesis or changes will be lost.

9. **Run Floorplan Step**

   Start floorplanning using the new netlist to ensure the design leverages the improved timing.

   ```shell
   run_floorplan
   ```
  <img width="297" height="98" alt="image" src="https://github.com/user-attachments/assets/2401225f-22ff-44d9-9e1b-d42f1b859c60" />


10. **Run Placement Step**

    Place standard cells according to the new netlist.

    ```shell
    run_placement
    ```

    Note:
    - The number of placement iterations may increase due to larger buffer sizes.
    - Observe if design congestion and core area increase.

   <img width="195" height="53" alt="image" src="https://github.com/user-attachments/assets/ddb51c0b-fd64-4a8b-a40c-e1f519818451" />

11. **Run Clock Tree Synthesis (CTS)**

    Run the CTS stage to insert clock buffers and balance the clock network.

    - Key parameters:
      - **Target Skew**: $20\,\text{ps}$
      - **Root Buffer**: e.g., `BUFF16` (high drive)
      - **CTS Tolerance**: $100$ (trade-off QOR/runtime)

    Confirm CTS settings are appropriate.

    ```shell
    run_cts
    ```
    <img width="253" height="47" alt="image" src="https://github.com/user-attachments/assets/606f4a45-58e0-4616-accb-3fe357c7feb0" />


    - The CTS process will add appropriate clock buffers and modify the netlist.

12. **Verify CTS Results**

    List files in the synthesis directory to confirm a new CTS netlist (e.g., `.csv` file) is generated and reflects the addition of clock buffers.

    ```shell
    ls -ltr
    ```

    Confirm that the CTS-modified netlist now contains additional clock buffers.


---

# Setup timing analysis using real clocks

This section discusses the impact of **real clocks** on timing analysis in synchronous digital circuits, focusing on the construction and behavior of the clock tree, and how clock network delays affect setup and hold time calculations. It introduces key theoretical concepts such as **clock network delay**, **clock skew**, **setup and hold time equations**, and **slack** in timing analysis. Visualizing these timing relationships is critical for understanding robust hardware operation at high frequencies.

<img width="1623" height="882" alt="image" src="https://github.com/user-attachments/assets/e7d55f0e-243e-45e1-84df-ef50ab7686ca" />


## Core Concepts

### Real Clock Trees and Clock Network Delays

In practical hardware, clock signals traveling from the source (like a PLL) to sequential elements (such as launch and capture flops) are delayed by **buffers** and **wires**—components of the **clock tree**. Unlike an ideal scenario where the clock edge arrives at all endpoints simultaneously, real circuits introduce delays at each stage due to these physical elements. 

The total delay for the clock to reach a flip-flop is termed the **clock network delay** for that point.

Let:

- $D_1$ = launch flop clock network delay
- $D_2$ = capture flop clock network delay


### Setup Timing Analysis with Real Clocks

**Setup timing analysis** ensures that data launched by a clock edge at one flop arrives and is captured correctly at another flip-flop by the next clock edge. The **combinational delay** ($\Theta$) is the time taken for the data to propagate through logic between the launch and capture flops.

When factoring in real clock tree delays, the **setup timing equation** is:

$\Theta + D_1 \leq T + D_2 - S - U$

Where:
- $\Theta$ = combinational (data path) delay
- $D_1$ = launch flop clock network delay
- $T$ = clock period
- $D_2$ = capture flop clock network delay
- $S$ = setup time of the capture flop
- $U$ = clock uncertainty (includes jitter and margins)

The shift in arrival times due to $D_1$ and $D_2$ changes the time window for valid data arrival.

### Clock Skew

The **clock skew** ($Q$) is defined as the difference in clock network delays between the launch and capture flip-flops:

$Q = D_1 - D_2$

Skew directly modifies the amount of time available for data to traverse the logic, influencing setup and hold margins.


### Data Required Time and Data Arrival Time

These terms are central to timing analysis:

- **Data Arrival Time:** The actual time at which data arrives at the destination flip-flop, incorporating all relevant delays.
- **Data Required Time:** The latest permissible time by which the data must arrive to be correctly captured by the destination flip-flop.

For robust design, it is required that:

$\text{Data Arrival Time} \leq \text{Data Required Time}$

The difference between these quantities is **slack**:

$\text{Slack} = \text{Data Required Time} - \text{Data Arrival Time}$

A positive or zero slack means timing is met; negative slack indicates a violation.


### Hold Timing Analysis

**Hold timing analysis** prevents data from changing too soon after a clock edge, which might lead to capture of incorrect data. Here, the aim is for the data NOT to arrive at the capture flop too early.

Key equation (for hold analysis) with real clocks:

$\Theta + D_1 \geq H + D_2$

Where:
- $\Theta$ = combinational delay
- $D_1$ = launch flop clock network delay
- $H$ = hold time of the capture flop
- $D_2$ = capture flop clock network delay

Hold time relates to the internal architecture of flip-flops—specifically the delay from internal circuitry (like a MUX) that must be respected before new data is allowed to propagate.

<img width="1480" height="876" alt="image" src="https://github.com/user-attachments/assets/0bbe0425-ecdf-42d8-a696-887b63294e3c" />
<img width="1382" height="502" alt="image" src="https://github.com/user-attachments/assets/9b3c23d5-f5cf-4b38-982f-9762c57f2424" />
<img width="1360" height="816" alt="image" src="https://github.com/user-attachments/assets/a1569f9e-b20c-4e55-948d-5e9cee50058f" />


## Key Points

- Real clock distribution introduces **network delays** that must be explicitly factored into setup and hold timing equations.
- The **setup equation** adjusts the ideal scenario by accounting for launch/capture edge arrival offsets, setup time, and uncertainty:  
  $\Theta + D_1 \leq T + D_2 - S - U$
  
- The **hold equation** ensures data does not arrive too early at the capture flop:  
  $\Theta + D_1 \geq H + D_2$
  
- **Clock skew** ($Q = D_1 - D_2$) is the net effect of difference in clock path delays between launch and capture endpoints, playing a pivotal role in timing margins.
- **Slack** quantifies the margin between data arrival and its required time; positive slack is safe, negative slack requires design correction.
- **Setup time** (S) and **hold time** (H) are intrinsic sequential element properties that, along with clock network delays and uncertainty, define the operational timing windows for all data transfers in synchronous circuits.

---

# Hold timing analysis using real clocks

This document explains the theoretical concepts of timing analysis in digital circuits, focusing on the distinction between hold and setup timing, the role of clock network delays, and how uncertainties and skew influence data stability. Key terminology is introduced, including combinational delay, clock network delays (D1 and D2), uncertainty, jitter, skew, and slack. Concepts are contextualized around single and multiple clock domains. 

## Core Concepts

### Combinational Delay and Timing Constraints

- **Combinational delay** is the time required for a logic signal to traverse a combination of gates and wires, traveling from the output of a launch flip-flop to the input of a capture flip-flop.
- **Timing constraints** ensure that data transitions are reliably captured. For hold time analysis, the combinational delay ($t_{comb}$) must be sufficiently large such that $t_{comb} > t_{hold}$, where $t_{hold}$ is the hold time of the capture flip-flop.

<img width="1655" height="884" alt="image" src="https://github.com/user-attachments/assets/66e86f6c-e840-496f-b2b4-c144de63ba06" />


### Clock Network Delays

- **Clock signals** experience different delays before arriving at the launch or capture flip-flops. These are modeled as D1 (launch clock network delay) and D2 (capture clock network delay).
- The clock edge experiences buffer and wire delays as it travels through the clock tree, with each buffer and wire segment contributing to the total delay observed at a flip-flop.


### Equations and Terminology

- For hold time, if D1 and D2 are equal, the fundamental hold condition remains: $t_{comb} > t_{hold}$.
- **Uncertainty** and **jitter** represent variation in clock edge arrival. In cases where the same clock edge is used for both the launch and capture paths, these uncertainties are consistent and can be considered less significant. However, for realism, a small uncertainty value is included.

- The **data required time** represents the minimum time data must be held stable at the capture flip-flop: 
  $data\ required\ time = t_{hold} + t_{uncertainty}$
- The **data arrival time** is determined by the launch edge, clock network delays, and combinational path:
  $data\ arrival\ time = t_{comb} + D1 - D2$
- **Slack** is defined for hold analysis as: 
  $slack = data\ arrival\ time - data\ required\ time$
  For correct operation, slack must be zero or positive.

<img width="1640" height="903" alt="image" src="https://github.com/user-attachments/assets/1fe23e0e-bc5e-4252-967b-554cda2f75b7" />


### Setup vs. Hold Timing

- In **setup timing analysis**:
  - Data must arrive and be stable **before** the capture clock edge: $t_{comb} + D1 < T_{clk} + D2$
  - Slack is positive when the data path is not too slow.
- In **hold timing analysis**:
  - Data must **not** change **immediately** after the clock edge: $t_{comb} + D1 > t_{hold} + t_{uncertainty} + D2$
  - Slack is positive when the data path is not too fast.

- The equations integrate the clock network delays (D1, D2), uncertainties, and, as appropriate, setup or hold times.
<img width="1572" height="880" alt="image" src="https://github.com/user-attachments/assets/56f91fbb-c9f4-4a68-95c5-c72ff0120882" />
<img width="1643" height="885" alt="image" src="https://github.com/user-attachments/assets/0d73c666-ffe7-43c3-9dc0-09199f37bc1e" />


### Skew and Its Impact

- **Skew** is the difference between clock network delays at the launch and capture flops: 
  $skew = |D1 - D2|$
- Skew can be beneficial or detrimental, depending on its direction; its absolute value is used to ensure only the magnitude impacts the timing equation.


### Delay Components in Clock Paths

- The delays (D1 and D2) are composed of multiple segments:
  - Real wire RC delay (resistive-capacitive)
  - Buffer delay
- These segments can vary between the launch and capture paths, depending on physical implementation and the use of different buffers or wire lengths.


### Pessimism in Timing Analysis

- Common clock paths to both launch and capture flops can introduce *pessimism*—an overestimation of timing uncertainties or variations.
- Advanced analyses incorporate techniques like on-chip variation (OCV) and certain derating factors to more accurately model real-world behaviors.


## Key Points

- **Hold analysis** ensures data does not change too early due to fast paths; any negative slack indicates a violation and necessitates a slower data path.
- **Setup analysis** ensures data arrives before it is captured; negative slack means the path is too slow and must be sped up.
- D1 (launch clock delay) and D2 (capture clock delay) are essential to accurate timing equations.
- The structure of clock trees, buffer sizing, and routing can make D1 and D2 unequal, directly impacting skew and timing closure.
- When analyzing multiple clock domains, complexity increases as each domain’s unique clock structure and delays must be considered.
- Realistic uncertainties (from jitter, process, voltage, and temperature variations) are modeled and included in timing margins, especially for robust analysis.

---
