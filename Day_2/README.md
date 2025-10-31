# Chip Floor planning considerations
---
# Utilization factor and aspect ratio

This section explains how to determine the width and height of the **core** and **die** in the context of physical integrated circuit (IC) design. It introduces fundamental concepts and theoretical principles required for estimating these dimensions, focusing on the roles of standard cells, netlists, utilization factor, aspect ratio, and the structural relationship between the core and die.

---

## Core Concepts

### Physical Design: Core and Die

- **Core**: The portion of the chip where fundamental logic and functional circuits (standard cells, registers, memory macros) are placed.  
- **Die**: The entire silicon chip area, including the core and additional peripheral regions such as IO rings, power rings, and scribe lines. The die encapsulates the core and provides physical containment for the whole circuit.

<img width="1090" height="887" alt="image" src="https://github.com/user-attachments/assets/84364c67-5636-4288-8de6-601ed7886e36" />


---

### Netlist and Standard Cell Dimensions

- The **netlist** specifies the connectivity between components such as flip-flops and logic gates (e.g., AND, OR gates).
- **Standard cells** are pre-designed logic blocks with defined physical dimensions. Their area is typically measured in units based on the manufacturing technology (e.g., $1 \text{ unit} \times 1 \text{ unit}$).
- The **flip-flops** and logic gates are considered standard cells for estimation purposes.

<img width="1130" height="775" alt="image" src="https://github.com/user-attachments/assets/ec2d15a3-8972-41f7-9e94-c262c821ba74" />


---

### Calculating Area

- The **area occupied by the netlist** is the cumulative area required to place all standard cells (ignoring wires at this stage).
- Placing all standard cells together provides a minimal bounding rectangle (or square) which determines the minimum area required for the design ($\text{Area} = \text{Length} \times \text{Width}$).

Example:  
- If four standard cells each of area $1$ unit $^2$ are arranged, the rectangle would measure $2$ units by $2$ units, yielding an area of $4$ units $^2$.

<img width="1139" height="456" alt="image" src="https://github.com/user-attachments/assets/67a4a4dc-c91f-4877-8a18-6b7f4ac535b3" />
<img width="447" height="360" alt="image" src="https://github.com/user-attachments/assets/72ac9340-5775-4989-bc0d-9dc1416c3b78" />


---

### Core and Die Relationship

- The **core** contains all the fundamental logic.  
- The **die** encompasses the core plus additional structures (pad rings for IO, power, corners, scribe lines).
- Multiple die are patterned on a silicon wafer for mass fabrication.

<img width="1609" height="614" alt="image" src="https://github.com/user-attachments/assets/443fb963-9751-450c-872c-bf6a20e59651" />


---

### Utilization Factor

- **Utilization factor** ($U$): Measures how much of the core area is occupied by the design logic.
- Defined as:  
  $\text{Utilization Factor} = \frac{\text{Area occupied by netlist}}{\text{Total core area}}$
- $U = 1$ indicates 100% occupancy; there is no spare room to add logic.
- Practical designs use a lower utilization (such as $U = 0.5$ or $U = 0.6$), allowing space for extra logic, wiring, and optimization.

Example:  
- If netlist area is $4$ units $^2$ and the core is also $4$ units $^2$, $U = 1.0$.
- If netlist area is $4$ units $^2$ but core is $8$ units $^2$, $U = 0.5$.

<img width="624" height="228" alt="image" src="https://github.com/user-attachments/assets/0c73897a-f2cc-4c6b-a988-17db49ec5de8" />
<img width="885" height="778" alt="image" src="https://github.com/user-attachments/assets/a8b788ae-5df4-4b93-b81e-c5b7492a5309" />
<img width="701" height="478" alt="image" src="https://github.com/user-attachments/assets/e554029d-1a76-4ac9-aa8f-59d9477f8ab4" />

---

### Aspect Ratio

- **Aspect ratio** ($AR$): Describes the **shape** of the core or die, not its area.
- Defined as:  
  $\text{Aspect Ratio} = \frac{\text{Height}}{\text{Width}}$
- $AR = 1$ implies a square.
- $AR \ne 1$ (e.g., $AR = 0.5$) implies a rectangle.

Example:  
- Core of $2$ units height & $4$ units width: $AR = \frac{2}{4} = 0.5$.

<img width="1311" height="811" alt="image" src="https://github.com/user-attachments/assets/ceab0e27-cab0-43de-9653-1fd2f25c8ef9" />

---

## Key Points

- Determining core and die dimensions starts with standard cell area estimates derived from the netlist.
- Netlist area is the sum of standard cell areas, excluding wire routing in early estimation steps.
- Core area must exceed the netlist area, factoring in practical utilization (usually not 100%).
- **Utilization factor** quantifies what fraction of the core is occupied; lower utilization allows for added logic, routing, and buffers during later design stages.
- **Aspect ratio** defines whether the core/die is square or rectangular, impacting floorplanning and packaging efficiency.
- The die includes both the core and additional regions needed for IO and scribe lines, and multiple die are fabricated per wafer.

---
# Concept of pre-placed cells

This documentation focuses on the concepts of core area definition, utilization factor, aspect ratio, and the modular approach of preplaced cells in digital hardware, emphasizing theoretical understanding rather than implementation details. Each section explains foundational principles in open-source hardware floor planning and modular circuit design.

## Core Concepts

### Core Area, Utilization Factor, and Aspect Ratio

The core of a chip refers to the central region where the primary functional circuit components are placed. The dimensions of the core are its width ($W_{core}$) and height ($H_{core}$).

**Utilization Factor:**  
The utilization factor measures the fraction of the total core area occupied by placed components ("netlist area") versus the total available core area.

$Utilization\ Factor = \frac{Area\ occupied\ by\ netlist}{Total\ core\ area}$

For example, if a netlist takes up $4$ square units in a $4 \times 4 = 16$ square unit core, the utilization factor is $4 / 16 = 0.25$, or $25\%$. This means only $25\%$ of the core is used, while the remaining $75\%$ is available, often reserved for routing and potential optimizations.

<img width="1403" height="822" alt="image" src="https://github.com/user-attachments/assets/d4d7734b-12e1-4e1f-96dc-f71603be57e2" />


**Aspect Ratio:**  
The aspect ratio defines the shape of the core and is calculated as:

$Aspect\ Ratio = \frac{Height}{Width}$

For a square chip, $Aspect\ Ratio = 1$. Maintaining certain aspect ratios can be critical for performance and manufacturability.


### Layering and Routing

Unused area in the core is not wasted. It provides space for routing signal wires between placed components. Routing often occurs across multiple layers above the placement layer, allowing for more intricate connectivity without increasing the planar area.

### Modular Circuit Design and Preplaced Cells

To manage complex designs and increase reusability, large logic blocks are modularized and can be separated from the main circuitry. These modules are often called **preplaced cells**.

**Granularization and Blackboxing:**  
A large combinational block (e.g., 100k gates) may be split into smaller independent modules (e.g., two 50k gate blocks). The internal structure of each module can be "black-boxed"—details are hidden from the top-level view, exposing only input and output (IO) ports.

This abstraction means that, from the perspective of the top netlist, the internal logic is invisible, and only the connectivity via IO pins matters.

<img width="1400" height="739" alt="image" src="https://github.com/user-attachments/assets/676df7b3-bcf0-4d45-95ad-d198314ed55d" />
<img width="704" height="837" alt="image" src="https://github.com/user-attachments/assets/5a8af175-43f6-4fac-bb94-1a37dd613ba5" />


**Reusability:**  
Once a module is defined and black-boxed, it can be instantiated multiple times within a design. There is no need to duplicate the logic physically; the block is implemented once and reused wherever needed. This is key for components like memories, arithmetic units, or other intellectual property (IP) blocks.

<img width="1136" height="833" alt="image" src="https://github.com/user-attachments/assets/e2c13f8a-e710-4e91-868f-1bd8456191be" />


**Preplaced Cell Placement:**  
These cells are physically placed at fixed locations on the chip before automated placement and routing. Their positions remain unchanged throughout further design flows, ensuring predictable timing and connectivity. The placement tools recognize these as fixed, immovable obstacles.

<img width="1243" height="741" alt="image" src="https://github.com/user-attachments/assets/71c38991-a30d-41ef-babb-a6a80bba3d2a" />


## Key Points

- **Utilization factor** quantifies how much of the chip's core area is actively used by logic cells, influencing efficiency and routing capacity.
- **Aspect ratio** of a core impacts the physical layout's effectiveness; a value of $1$ indicates a square, which is often optimal for symmetry and ease of design.
- **Unused area** is essential for routing interconnections; multiple routing layers are used for complex wire patterns without expanding the 2D area.
- **Preplaced cells** are large, reusable logic blocks (often IPs) placed at fixed positions pre-layout to ensure modularity and reliability.
- **Blackboxing** abstracts module internal details, exposing only IO pins for top-level interconnection, crucial for managing complexity in large designs.
- **IP reuse** reduces development cost and time by allowing blocks to be implemented once but instantiated multiple times within a circuit.
- **Fixed placement of preplaced cells** guarantees that critical blocks retain their location for optimal physical and timing characteristics, unaffected by automated tools during placement and routing.

---
# De-coupling capacitors

Preplaced cells are essential building blocks in digital hardware design, implemented as reusable logic modules (e.g., memories, IP blocks) that are instantiated multiple times within a chip. Their locations are fixed prior to automated placement and routing, ensuring optimal connectivity and predictable physical behavior. Effective integration of preplaced cells includes placing them strategically according to the overall design scenario and augmenting them with decoupling capacitors to ensure robust power delivery and noise immunity.


## Core Concepts

### Preplaced Cells (Macros, IPs, Memories)

- **Definition**: Preplaced cells refer to complex logic blocks—such as memories, clocking cells, or IP modules—that are designed once and reused multiple times in a chip.
- **Purpose**: These cells centralize complex functionality and enhance design reuse, reducing redundant effort and enabling modular design strategy.
- **Placement Characteristics**: Their locations are defined before general cell placement and routing. Once set, these positions are fixed and remain untouched by subsequent automated design tools.

<img width="1303" height="845" alt="image" src="https://github.com/user-attachments/assets/79b86501-4190-4322-91d3-53d5ac6f7ced" />


### Design-Driven Location Assignment

- **Design Scenario**: Placement of preplaced cells aligns with system-level connectivity. For example, blocks interacting heavily with input pins are positioned close to the input edge of the chip to minimize signal latency and congestion.
- **Immutability**: After placement, these block locations do not move throughout the rest of the design flow, ensuring stable timing and predictable power distribution.

<img width="1544" height="882" alt="image" src="https://github.com/user-attachments/assets/03061b4b-176a-4086-9221-f16097a4d5ca" />


### Decoupling Capacitors and Power Integrity

- **Purpose**: Decoupling capacitors are placed around preplaced cells to maintain stable voltage levels during circuit switching events, counteracting voltage drops caused by resistance and inductance in long supply wires.
- **Charging Dynamics**: When a digital circuit switches from logic 0 to logic 1, its internal capacitance demands a surge of current, which is ideally supplied by proximate decoupling capacitors to avoid voltage sag at the logic gate.
- **Voltage Drop and Noise Margin**: The supply voltage delivered over physical wire exhibits a reduction ($V_{DD}'$) due to resistance and inductance. If the voltage at a logic gate drops below the defined noise margin threshold, signal integrity is at risk. 
- **Noise Margin Diagram**: 
  - Logic '0' is represented by voltages between $V_{OL}$ and $V_{IL}$.
  - Logic '1' is voltages between $V_{IH}$ and $V_{OH}$.
  - The region between $V_{IL}$ and $V_{IH}$ is undefined, and signals falling here can result in unpredictable circuit behavior.
- **Supply Assurance**: By placing decoupling capacitors close to preplaced cells, the path resistance and resulting voltage drop are minimized, preserving the full $V_{DD}$ at critical switching moments.

<img width="1272" height="788" alt="image" src="https://github.com/user-attachments/assets/8a1767c8-69e8-4673-8ecb-4b533e9e442c" />
<img width="1560" height="745" alt="image" src="https://github.com/user-attachments/assets/8330c0ff-ee95-4220-92f7-08553b826fa3" />
<img width="1545" height="736" alt="image" src="https://github.com/user-attachments/assets/50bed69d-2cd0-42a8-b11c-06950f1f3b48" />

## Key Points

- **Preplaced cells** are complex, reusable logic blocks instantiated with fixed locations, which streamlines design, ensures consistency, and supports large hierarchical designs.
- **Fixed placement**: Once defined, the positions of preplaced cells are not modified during subsequent automated placement and routing steps.
- **Strategic positioning** is informed by connectivity to primary inputs/outputs and other high-traffic areas, which enhances both performance and routability.
- **Decoupling capacitors** are critical adjuncts, locally supplying charge during switching events and ensuring that core logic operates within defined noise margins.
- The interplay between **voltage drop, supply impedance, local capacitance, and noise margin** underpins the need for carefully engineered power delivery in proximity to preplaced cells.
- **Global power planning** and grid design (discussed separately) extend these principles to the chip-wide level, ensuring overall power integrity in addition to local delivery.

<img width="1072" height="870" alt="image" src="https://github.com/user-attachments/assets/05a1d503-365a-4791-8142-0fa09c9c5428" />
---

# Power planning

This documentation covers theoretical foundations and key concepts relevant to power planning in modern open-source hardware, focusing on the management of current demands across multiple circuit elements, the limitations of decoupling capacitors, phenomena such as ground bounce and voltage droop, and the use of distributed power meshes as a robust solution for voltage stability in integrated circuits.

## Core Concepts

### Current Demand and the Role of Decoupling Capacitors

Each electronic element or block within a circuit requires current to function. A **decoupling capacitor** is often incorporated to locally supply charge and mitigate voltage fluctuations caused by these varying current requirements. Decoupling capacitors help smooth out rapid transient demands by storing charge close to the load, ensuring local voltage stability during fast switching events.


### Scaling and Power Distribution in Complex Chips

When a given macro or block is repeated multiple times on a chip, the cumulative current demand increases proportionally. Direct decoupling for every element is infeasible due to area and cost constraints. Consequently, many elements rely on the central power supply or power distribution network, increasing the importance of effective **power planning** to deliver stable voltage across the entire chip.


### Signal Integrity and Voltage Drop

Signal lines (e.g., a 16-bit bus) require clean and consistent voltage levels to maintain signal integrity from driver to load. When the power supply point is physically distant from the signal path, the resistance and inductance of the interconnects may cause a **voltage drop**. As a result, the voltage delivered to the line ($V_{actual}$) will be less than the nominal supply voltage ($V_{DD}$):

$V_{actual} = V_{DD} - I \cdot R_{line}$

where $I$ is the instantaneous current demand and $R_{line}$ is the resistance of the supply path.

<img width="1077" height="825" alt="image" src="https://github.com/user-attachments/assets/c782526e-8e2c-4bf6-808d-fa0887556251" />


### Capacitive Switching and Power Rail Disturbances

Each bit line of a bus can be modeled as a capacitor. When a bus connected to an inverter switches state (all bits transition from 1 to 0 or 0 to 1), all associated capacitors discharge or charge simultaneously. This simultaneous switching results in large current spikes on either the power or the ground rails. 

- **Ground Bounce:** When many capacitors discharge to ground at once, the ground potential can temporarily rise above 0V, forming a "bump" or bounce. If this exceeds the designed noise margin, signal levels may enter undefined states.
- **Voltage Droop:** If many capacitors simultaneously charge from the supply rail, the supply voltage may momentarily sag below its nominal value—a phenomenon known as voltage droop.

If these effects exceed allowable thresholds, signal reliability is compromised.

<img width="1262" height="786" alt="image" src="https://github.com/user-attachments/assets/4a6f88d5-a53f-4b16-8f79-1b47544825f2" />
<img width="1269" height="777" alt="image" src="https://github.com/user-attachments/assets/b0974b5b-9415-4f95-aead-4a6672cdedc7" />

### Challenges with Single-Point Power Delivery

Distributing power from a single supply point causes significant voltage gradients and increases the risk of the above problems, particularly as chip size and current demand rise. The single supply must source all the current, resulting in drops and disturbances that propagate through the entire distribution network.

<img width="1280" height="765" alt="image" src="https://github.com/user-attachments/assets/43634267-5f95-4070-b7bf-6f8d14f1a40b" />
<img width="1073" height="855" alt="image" src="https://github.com/user-attachments/assets/d359243b-0523-479c-90e4-df2799d89df5" />


### Distributed Power Mesh Networks

The adoption of **power meshes**—interconnected grids of VDD and VSS (ground) lines running horizontally and vertically across the chip—enables each local region to draw current from the nearest supply rather than from a distant source.

Advantages:
- Markedly reduces voltage drop and ground bounce.
- Improves local voltage regulation.
- Enhances the scalability and robustness of large and complex chips.

Any logic block or interconnect can efficiently source or sink current to the nearest point on the mesh, leveraging both vertical and horizontal network structure for minimal path resistance.

<img width="1122" height="878" alt="image" src="https://github.com/user-attachments/assets/64e15a72-e898-4f13-bc93-7a46694bc80a" />
<img width="1087" height="811" alt="image" src="https://github.com/user-attachments/assets/851387c5-c0dd-4c93-a2e1-c7a1b24f7ddf" />

## Key Points

- **Each circuit element requires a predictable and stable local power supply for reliable operation.**
- **Decoupling capacitors** serve to locally buffer charge but cannot feasibly be deployed for every element in large-scale designs.
- Large, simultaneous switching events (such as on bus lines) can induce voltage anomalies: **ground bounce** and **voltage droop**.
- The magnitude of voltage drops or bounces is proportional to the total current being switched and the impedance of the power path: $V_{drop} = I_{switch} \cdot Z_{path}$.
- If these disturbances exceed the noise margin, digital circuits may operate unpredictably or erroneously.
- **Distributed power mesh networks** (grids of interconnected VDD and VSS lines) fundamentally mitigate these risks by reducing average current path lengths and distributing the load across multiple supply points.
- Modern VLSI chips utilize such meshes, leading to multiple power pins and entry points distributed throughout the silicon to maintain tight voltage regulation and reduce parasitic effects.
- Effective power planning and the deployment of power meshes are necessary to ensure both local and global voltage stability, signal integrity, and overall reliability in high-performance, densely packed integrated circuits.

---
# Pin placement and logical cell placement blockage

This documentation introduces the **theory and conceptual framework** behind pin placement and connectivity in digital circuit design, using a multi-section flip-flop-based design as a case study. It covers the relationship between netlist connectivity, pin placement strategies, floorplanning, and the critical role of clock signal integrity for high-speed sequential circuits.

![PLACEHOLDER: diagram/slide/concept-visual]

---

## Core Concepts

### Pin Placement in Circuit Design

**Pin placement** is the process of assigning physical locations to input, output, and clock pins on the boundary of an integrated circuit (IC) die. The primary goal is to optimize overall circuit performance, minimize wiring complexity, and ensure robust operation. This is typically handled after the logical circuit design and netlist definition, forming a bridge between the functional (front end) and physical (back end) domains of hardware design.

### Netlist and Connectivity

A **netlist** defines the connectivity of logical gates and components within a digital circuit. It specifies:
- Which cells, such as flip-flops or logic gates, are present.
- How these cells are interlinked via signals and ports (e.g., clocks, data inputs/outputs).

This connectivity is usually described in hardware description languages (HDLs) like Verilog or VHDL. The netlist is essential for guiding both pin placement and subsequent automated placement and routing steps.

<img width="1247" height="893" alt="image" src="https://github.com/user-attachments/assets/fc8d2b36-13c1-4c20-aaad-462394b5f1e3" />


### Clock Domains and Inter-Clock Timing

A **clock domain** is a section of a circuit governed by a specific clock signal. Multiple clock domains may exist, driving different flip-flops or logic blocks independently. The scenario where flip-flops are driven by different clocks (e.g., flip-flop 1 by 'clock 1', flip-flop 2 by 'clock 2') is central to **inter-clock timing analysis**. This analysis ensures correct data transfer and synchronization when crossing between domains.

<img width="912" height="857" alt="image" src="https://github.com/user-attachments/assets/f2d42dab-c6bd-4423-99e5-22495568cdc1" />


### Floorplanning and Pin Arrangement

The **floorplan** is an abstract representation of the chip, detailing block placements and reserved areas for pins:
- Input ports are often placed on the left, output ports on the right, but placements may vary by design needs.
- Physical constraints or block arrangements may dictate pin locations for signal integrity and routing efficiency.
- Port ordering is determined by the proximity to functional blocks, not necessarily by logical sequence.
- Clock pins are physically larger to ensure minimal resistance and high signal integrity because they drive many cells continually.

<img width="1416" height="855" alt="image" src="https://github.com/user-attachments/assets/0a084366-67bd-4a87-8a57-f85df84bc23b" />

### Pin Resistance and Signal Integrity

In digital circuits, especially those with high fanout or critical timing paths, **clock pins** must have:
- The lowest possible resistance paths to minimize signal degradation.
- Larger physical size to reduce electrical resistance, ensuring robust and reliable clock distribution throughout the chip.

This principle supports high speed, low-latency signal propagation, which is vital for synchronous operations.

$R = \frac{\rho L}{A}$

Where:
- $R$ is resistance,
- $\rho$ is resistivity,
- $L$ is wire length,
- $A$ is cross-sectional area.

Increasing $A$ (by making clock pins larger) decreases $R$.


### Logical Blockage and Tool Guidance

After pin positions are finalized, **logical placement blockages** are defined to prevent automated tools from inadvertently placing cells in reserved pin regions. This ensures pins remain unobstructed for connectivity and signal integrity, streamlining the subsequent physical design steps.

<img width="1136" height="840" alt="image" src="https://github.com/user-attachments/assets/e4ee1aa5-d523-41a9-bee2-af28e722831a" />

---

## Key Points

- **Pin placement optimizes physical connectivity**, minimizes routing complexities, and enhances performance.
- The **netlist** forms the basis for mapping logical connectivity to physical locations.
- **Clock domain interactions** (inter-clock timing) are a major source of timing challenges in multi-clock designs.
- **Floorplanning places ports** (inputs/outputs) according to proximity to relevant functional blocks and routing efficiency, not just logical sequence.
- **Clock pins use larger pads** to lower electrical resistance and ensure robust clock signal distribution.
- **Placement blockages** are defined to reserve areas around pins, guiding automated place-and-route tools.
- **Front-end and back-end teams** must coordinate—the front end defines netlist connectivity, the back end executes pin placement and physical constraints.

---
# Steps to run floorplan using OpenLANE

This lab covers the floor plan stage of the physical design flow in open-source hardware design. The floor plan stage follows synthesis and involves setting up the chip layout by defining the die area, core area, aspect ratio, utilization factor, and placement of input/output cells. Additionally, this stage includes Power Distribution Network (PDN) creation and macro placement. The lab will guide you through configuring floor plan parameters, understanding the configuration hierarchy, and executing the floor plan flow using open-source tools.

**Aim:** Learn to configure and execute the floor plan stage of the design flow, including understanding switches, configuration files, and parameter settings.

**Key Steps:**
- Review synthesis flow completion
- Understand floor plan configuration parameters and switches
- Examine design-specific and system-default configuration files
- Set core utilization, aspect ratio, and I/O metal layer parameters
- Execute the floor plan flow and verify successful completion

## Lab Steps

### Step 1: Review Previous Work and Floor Plan Context

The floor plan stage begins after the synthesis flow has been completed. From the previous session, you should have completed:
- Design setup
- Synthesis flow
- Flop count verification
- Clock percentage analysis

### Step 2: Understand Floor Plan Objectives

Floor plan involves the following key activities:

- **Die and Core Area Definition:** Setting the overall chip boundaries and usable core area
- **Aspect Ratio Configuration:** Defining the height-to-width ratio of the core
- **Utilization Factor:** Specifying how densely cells should be packed
- **Input/Output Cell Placement:** Positioning I/O cells around the core periphery
- **Power Distribution Network (PDN):** Creating power and ground distribution structures
- **Macro Placement:** Positioning large pre-designed blocks

Standard cell placement occurs in a later stage and is not performed during floor planning.

### Step 3: Access Configuration Files

Navigate to the OpenLane configuration directory:

```bash
cd openlane/configuration
pwd
ls -la
```

<img width="728" height="576" alt="image" src="https://github.com/user-attachments/assets/a346324c-8c45-4a79-b336-3c3e31678c02" />


Open and review the README file to understand the available variables for each design stage.

```bash
cat README.md
```

### Step 4: Understand Global Configuration Variables

The configuration hierarchy defines parameter precedence. Review the variables section in the README, which includes:

- **Global Variables:** Design name, Verilog files
- **Synthesis Variables:** Library files, driving cell, design rule constraints (transition max, span out, max capacitive load), minimum and maximum libraries for static timing analysis
- **Floor Plan Variables:** Core utilization, aspect ratio, core margin, I/O metal layers, PDN parameters
- **Placement Variables:** Target density and related settings

### Step 5: Review Floor Plan Specific Switches

Key floor plan configuration parameters include:

**FP_CORE_UTIL (Floor Plan Core Utilization)**

This parameter defines the core utilization percentage—the ratio of total cell area to total core area. Default value is 50%.

```
FP_CORE_UTIL = 50
```

<img width="876" height="439" alt="image" src="https://github.com/user-attachments/assets/6a0ee5d5-6bea-4829-9eef-6cae2adc51dc" />


**FP_ASPECT_RATIO (Aspect Ratio)**

This parameter sets the height-to-width ratio of the core. Default value is 1 (square core).

```
FP_ASPECT_RATIO = 1
```

**FP_CORE_MARGIN (Core Margin)**

This parameter specifies the offset between the die boundary and the core boundary. Default value is 3.6 units.

```
FP_CORE_MARGIN = 3.6
```

**FP_IO_HMETAL and FP_IO_VMETAL (I/O Metal Layers)**

These parameters define the horizontal and vertical metal layers used for I/O placement. They are specified as follows:

```
FP_IO_HMETAL = 3
FP_IO_VMETAL = 4
```

Note: In the OpenLane flow, the actual metal layer used will be one layer higher than specified (e.g., if FP_IO_VMETAL is 4, metal layer 5 will be used; if FP_IO_HMETAL is 3, metal layer 4 will be used).

**FP_PDN_* (Power Distribution Network Parameters)**

These variables configure the PDN. The PDN_CORE_NET specifies the decoupling capacitors to be used during floor planning.

```
FP_PDN_CORE_NET = <capacitor_specification>
```

### Step 6: Understand Placement Configuration Parameters

**PL_TARGET_DENSITY (Placement Target Density)**

This parameter controls how closely or how spread out cells should be packed during initial placement:
- Value of 1: Design is very tightly packed with minimal free space
- Value close to 0: Design is spread out with more free space

Default is set close to 0 for initial loose packing. This allows for congestion analysis before gradually tightening the design toward closure.

```
PL_TARGET_DENSITY = 0.x
```

<img width="875" height="852" alt="image" src="https://github.com/user-attachments/assets/b2c25001-0119-4d3b-8ca7-092e21c956c0" />


### Step 7: Examine GPIO Mode Configuration

**FP_PIN_ORDER_CFG (Pin Order Configuration)**

The GPIO_MODE parameter controls pin positioning around the core:
- Mode 1: Pin positions are random but at equal distances
- Mode 0: Pin positions are not equally spaced

This configuration affects how input/output cells are distributed around the core perimeter.

```
GPIO_MODE = 1
```


### Step 8: Navigate to Design Directory

Change to your design-specific directory:

```bash
cd ../designs/picorv32a
ls -ltr
```

<img width="880" height="859" alt="image" src="https://github.com/user-attachments/assets/c6d13bc9-8ece-4eb1-a651-eb2d885ee461" />
<img width="872" height="310" alt="image" src="https://github.com/user-attachments/assets/12ff3728-08d4-4fb3-91e4-384b87111d6c" />


### Step 9: Review Design-Specific Configuration Files

You should see two design-specific configuration files:
- `config.tcl`
- `sky130.tcl`

These files override system default settings. The configuration precedence is:

1. System defaults (lowest priority)
2. `config.tcl` (design-specific overrides)
3. `sky130.tcl` (technology-specific overrides) (highest priority)

### Step 10: Examine config.tcl File

Open the design configuration file:

```bash
cat config.tcl
```

<img width="872" height="565" alt="image" src="https://github.com/user-attachments/assets/ae89b55f-a966-495e-85a9-74381374f979" />

Review the following sections:

**Design Name and Source Files**

```tcl
set ::env(DESIGN_NAME) "picorv32a"
set ::env(VERILOG_FILES) "./designs/picorv32a/src/picorv32a.v"
set ::env(SDC_FILES) "./designs/picorv32a/src/picorv32a.sdc"
```

**Clock Configuration**

```tcl
set ::env(CLOCK_NET) "5.000"
set ::env(CLOCK_PORT) "clk"
```

**Floor Plan Parameters**

```tcl
set ::env(FP_CORE_UTIL) 65
set ::env(FP_IO_VMETAL) 4
set ::env(FP_IO_HMETAL) 3
```

These settings override the system defaults. In this example:
- Core utilization is set to 65% (overriding the default 50%)
- I/O vertical metal is set to 4 (which will use metal layer 5 in the flow)
- I/O horizontal metal is set to 3 (which will use metal layer 4 in the flow)

### Step 11: Compare System Defaults with Design Configuration

From the system default configuration (`openlane/configuration/floorplan.tcl`):
- `FP_CORE_UTIL` default: 50
- `FP_IO_VMETAL` default: 2 (resulting in metal layer 3)
- `FP_IO_HMETAL` default: 3 (resulting in metal layer 4)

From the design-specific configuration (`config.tcl`):
- `FP_CORE_UTIL`: 65
- `FP_IO_VMETAL`: 4 (resulting in metal layer 5)
- `FP_IO_HMETAL`: 3 (resulting in metal layer 4)

### Step 12: Execute the Floor Plan Flow

Run the floor plan stage using the run_flow_plan command:

```bash
run_floorplan
```

<img width="879" height="855" alt="image" src="https://github.com/user-attachments/assets/23f660ee-71bd-4bca-a1a7-aeef9aea477a" />


The tool will process the configuration parameters and generate the floor plan layout based on the specified settings.

### Step 13: Monitor Execution and Review Output

During execution, you may see warnings such as obsolete statement warnings. These warnings can typically be disregarded as they refer to outdated syntax that is no longer required.

```
[WARNING] <message>: The source statement is obsolete and no longer required
```

<img width="889" height="617" alt="image" src="https://github.com/user-attachments/assets/8f2cfc4b-f207-4a30-9d7c-d8f32f39997f" />


### Step 14: Verify Successful Completion

Upon successful completion, the floor plan flow will finish without errors. The absence of error messages indicates that the floor plan stage has been completed successfully.

```bash
[INFO]: PDN generation was successful.
```

<img width="877" height="76" alt="image" src="https://github.com/user-attachments/assets/971c92e6-77a6-4025-a8a3-61bb5fbed9e6" />


### Step 15: Understanding Configuration Flexibility

The floor plan stage, like the overall PNR (Place and Route) flow, is iterative:

- **Initial Stages:** Parameters are set to prioritize congestion analysis with looser packing (lower target density)
- **Later Stages:** Parameters are adjusted to focus on timing closure with tighter packing
- **Designer Control:** You decide which parameters to activate at each iteration based on design requirements

This flexibility allows you to optimize the design progressively as you move toward final closure.

---

**Key Takeaways:**

- Floor plan configuration uses a three-level precedence hierarchy: system defaults, design-specific config, and technology-specific overrides
- Core utilization determines cell packing density
- I/O metal layers are specified one layer below their actual usage in the flow
- Placement target density controls initial cell distribution for congestion management
- The floor plan stage must be successfully completed before proceeding to placement and routing stages

---

# Review floorplan files and steps to view floorplan

This lab demonstrates the verification and visualization of floorplan configuration in an open-source hardware design flow. The objective is to confirm that design-specific configuration parameters override system defaults, and then visualize the resulting floorplan layout. The lab involves navigating the runs folder, examining log files and configuration files to verify parameter precedence, inspecting the DEF (Design Exchange Format) file to understand chip dimensions, and finally opening the floorplan in Magic for visual inspection.

## Lab Steps

### Step 1: Navigate to the Runs Folder

Navigate to the runs folder to access the latest generated design files.

```bash
cd runs
```

Check the contents to locate the folder corresponding to the current date (e.g., 10th September).

```bash
ls -ltr
```

<img width="877" height="320" alt="image" src="https://github.com/user-attachments/assets/c49cd152-2d8f-48e9-845b-3e8df3a69e6e" />


### Step 2: Verify Configuration Parameter Precedence

Move into the logs directory for the current run to examine the floorplan logs.

```bash
cd logs
cd floorplan
```

<img width="876" height="865" alt="image" src="https://github.com/user-attachments/assets/bd02b7d4-2776-40f8-9b02-91488ec23929" />


### Step 3: Examine IO Placer Log File

Open the IO placer log file to verify that design-level configuration parameters have overridden system defaults. Specifically, check the metal layer assignments.

```bash
# Open the io_placer.log file (or similar)
cat ioPlacer.log
```

Search for metal layer information using shift+G to navigate within the file viewer.

<img width="791" height="478" alt="image" src="https://github.com/user-attachments/assets/88a4e114-d225-4595-9788-a3c52bb28062" />



The log should display:
- **Vertical metal layer**: 5 (set by design config)
- **Horizontal metal layer**: 3 (set by design config)

### Step 4: Compare with Design Configuration File

Navigate to the design folder and examine the config.tcl file to verify the source configuration values.

```bash
cd ../../design
cat config.tcl
```

Verify the configuration contains:
- **Vertical metal**: 4 (which becomes 4 + 1 = 5 in the floorplan)
- **Horizontal metal**: 3 (which becomes 3 + 1 = 4 in the floorplan)
- **Core utilization**: 65%

### Step 5: Check Configuration Priority in Run-Specific Config

Navigate to the run configuration file to understand the parameter precedence hierarchy.

```bash
cd ../runs/[DATE]/config.tcl
cat config.tcl
```


Note the priority hierarchy (from highest to lowest priority):
1. Run-specific config.tcl
2. Design config.tcl
3. System defaults

For example, core utilization should show the value of 50 at the run level, which overrides the 65% from the design config.tcl.

### Step 6: Navigate to Floorplan Results

Move to the results directory to access the DEF file containing the floorplan data.

```bash
cd ../results
cd floorplan
ls -la
```

<img width="883" height="369" alt="image" src="https://github.com/user-attachments/assets/59e9a008-3703-4b1c-8542-7c1c2b9a843a" />


### Step 7: Examine the DEF File

Open and inspect the DEF (Design Exchange Format) file to understand the chip dimensions and cell placements.

```bash
cat *.def
```

![PLACEHOLDER: screenshot showing DEF file header and die area specification]

Locate the die area definition in the DEF file. The syntax is:

```
DIEAREA (X1 Y1) (X2 Y2) ;
```
<img width="790" height="198" alt="image" src="https://github.com/user-attachments/assets/5c7744ae-5151-4c74-828d-bbf38f97dfab" />

Where:
- **(X1, Y1)** = lower left corner coordinates (typically 0, 0)
- **(X2, Y2)** = upper right corner coordinates

For example:
```
DIEAREA (0 0) (500000 400000) ;
```

### Step 8: Calculate Die Dimensions

Use the database unit specification to convert DEF coordinates to actual chip dimensions.

The DEF file contains a units statement:

```
UNITS DISTANCE MICRONS 1000 ;
```

This indicates that **1 micron = 1000 database units**.

To calculate actual dimensions in micrometers, divide the coordinates by 1000:

Chip width = (X2 - X1) ÷ 1000 micrometers

Chip height = (Y2 - Y1) ÷ 1000 micrometers

### Step 9: Open Floorplan in Magic Layout Editor

Launch Magic to visualize the floorplan layout. First, identify the tech file location and construct the Magic command.

The tech file is typically located at a path similar to:

```
.../tech/sky130_open_pdk_public_release_tech_file.tech
```

Navigate to the results directory containing the DEF file and run Magic:

```bash
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &

```

<img width="881" height="875" alt="image" src="https://github.com/user-attachments/assets/d79a48fb-15b3-4fe0-bac2-0bcbc92b692a" />


### Step 10: Visualize the Floorplan

Once the files are loaded in Magic, the floorplan visualization will display:

- All placed cells and their orientations
- Metal layers and routing tracks
- Die boundaries
- IO placement around the perimeter

<img width="715" height="715" alt="image" src="https://github.com/user-attachments/assets/206f6ea3-7119-44fb-a666-97ef45d8da00" />

Use Magic's navigation tools (zoom, pan) to inspect different regions of the floorplan and verify that the configuration parameters have been correctly applied during floorplanning.
---

# Review floorplan layout in Magic
This lab focuses on navigating and analyzing the layout window of an integrated circuit design using keyboard shortcuts, mouse actions, and on-screen inspection tools. The aim is to:
- Center and fit the layout on screen.
- Zoom into layout portions and inspect object properties.
- Identify pin placement and layers.
- Examine device boundary structures and special cells (TAP, standard cells).
- Explore the effect of configuration changes on the layout.

## Lab Steps

### 1. Center and Fit the Layout Window

- **Select** the entire layout:
  - Press `S` on your keyboard.
  - The whole layout will be **highlighted**.
  
 <img width="722" height="717" alt="image" src="https://github.com/user-attachments/assets/910b3868-c3be-4bb2-83b7-94a78edd3b4c" />


- **Fit** the selected layout to your display:
  - Press `V` on your keyboard.
  - The layout will now fill the screen area.
  
 <img width="693" height="710" alt="image" src="https://github.com/user-attachments/assets/0ef42793-721a-4a4c-87ed-264741303542" />


### 2. Zooming and Navigating in the Layout

- **Zoom into a Region:**
  - Do a **left mouse click** at the starting corner of your zoom box.
  - Do a **right mouse click** to define the opposite corner.
  - Press `Z` on your keyboard.
  - The selected area will zoom in.
  
  <img width="495" height="590" alt="image" src="https://github.com/user-attachments/assets/91789054-ac4a-44a3-93a4-01913cee24ac" />


- **Repeat zoom** by pressing `Z` again to incrementally zoom further.
  
  <img width="529" height="718" alt="image" src="https://github.com/user-attachments/assets/640b835b-5297-4566-98ab-a78372bc0058" />


### 3. Inspecting Pin Placement and Layer Information

- **Pin Mode Information:**
  - Confirm that pin mode is set (pin mode value ‘1’ places IO pins equidistant).
  - Observe input/output pins arranged equidistantly around the boundary.
  
  <img width="457" height="601" alt="image" src="https://github.com/user-attachments/assets/3d0deffd-bd32-46b5-8209-5f777c90c898" />


- **Check Pin Layer:**
  - Move your mouse cursor over an input or output pin (no click).
  - Press `S` to select the object.
  - A secondary **properties or Tekkon window** will appear, displaying the layer (e.g., "metal 3" for horizontal pins).
  
 <img width="891" height="734" alt="image" src="https://github.com/user-attachments/assets/91187de3-1214-4cff-a482-abf267ade064" />
<img width="987" height="459" alt="image" src="https://github.com/user-attachments/assets/f35c4dc8-ca1f-491f-ad36-d94579917598" />


- **Check Vertical Pin Layer:**
  - Move over a vertical pin and press `S`.
  - The window should show the layer as "metal 4".
  
  <img width="580" height="624" alt="image" src="https://github.com/user-attachments/assets/586d1c96-b763-4eb9-9b61-2c71a459f1a8" />
<img width="940" height="728" alt="image" src="https://github.com/user-attachments/assets/444b6336-bd06-492e-8533-f3157b1f362d" />


### 4. Identify Device Boundaries and Special Cells

- **D Capsules (Boundary Endcaps):**
  - View the layout edge to see D capsules aligned along the side rows, marking the device boundary.
  
 <img width="785" height="733" alt="image" src="https://github.com/user-attachments/assets/53a8b11f-b7b4-4b08-9bd3-68b1f2db4295" />


- **TAP Cells:**
  - Zoom in closer on the layout to spot TAP cells.
  - TAP cells **connect the N-well to VDD and substrate to ground** to prevent latch-up in CMOS devices.
  - These cells appear in a **diagonally equidistant arrangement**. Check the lab README/config file for the relevant switch controlling the TAP cell distance.
  
  <img width="282" height="708" alt="image" src="https://github.com/user-attachments/assets/e24b1ae9-fe9a-4494-a420-96ecdba179f6" />


### 5. Recognize Floorplan Limitations and Identify Standard Cells

- **Floorplan Note:** 
  - The floor plan stage **does not handle standard cell placement**. However, some standard cells may appear in corners.

- **Locate Standard Cells in Lower Corners:**
  - Look at the **lower left** (or right) region marked by dashed lines.
  - Use left click, right click, then zoom, and arrow keys to navigate closer.
  - Identify boxes corresponding to standard cells (e.g., "sky130 buffer", "clock buffer", "OR gate").
  - These standard cells are clustered in one region.
  
  <img width="869" height="502" alt="image" src="https://github.com/user-attachments/assets/9be8ec69-6adb-46ff-9d5c-2277202a4bc4" />

### 6. Experiment with Layout Configuration

- **Change IO Pin Setting:**  
  - Adjust ‘input/output pin’ setting from 1 to 0 to alter placement.
  - Always make changes in the design-related configuration file for flexibility and traceability.
  

### 7. Refocus or Center Layout After Changes

- **Re-center and Fit Again:**
  - Press `S`, then `V` to highlight all and fit the layout again at any stage.
  
  <img width="664" height="665" alt="image" src="https://github.com/user-attachments/assets/7872dbdf-c9cb-49b6-89eb-e5ecc46d509e" />


---

After completing these steps, you may close the layout window as needed and explore further with other display and configuration options for deeper familiarity.
---

# Library Binding and Placement

# Netlist binding and initial place design

The placement and routing stage is a critical phase within the physical design flow for digital integrated circuits. This phase translates the logical representation of a netlist with abstract gates into an organized physical layout, assigning real, manufacturable shapes and positions to cells and interconnections based on defined libraries and constraints. It directly impacts the chip's performance, manufacturability, area, timing, and power consumption.

## Core Concepts

### Netlist to Physical Cells Mapping

- Each gate or logic component in a netlist is mapped to a physical cell representation.
- Though symbolic shapes (like the traditional symbols for AND, OR, etc.) convey logical function, their physical counterparts are *rectangular or square blocks* with specific width and height for manufacturing.
- This mapping provides every netlist element with tangible, physical dimensions for placement.

<img width="592" height="572" alt="image" src="https://github.com/user-attachments/assets/1efaa994-f765-4d86-8421-7b28f1fdfd5e" />


### The Library

- A **library** is a collection that provides:
  - The *physical dimensions* (width and height) of every cell.
  - The *timing characteristics* (e.g., propagation delay).
  - The *behavioral conditions* (such as when a flip-flop produces output).
  - Multiple *flavors* (variants) of the same logic cell, offering trade-offs between area and delay (e.g., larger, faster cells vs. smaller, slower ones).
- Designers can select different flavors based on timing requirements and available silicon area.

<img width="920" height="355" alt="image" src="https://github.com/user-attachments/assets/e1e58f5b-46c8-4499-8f72-3310316a6fa6" />


### Floorplanning and Cell Placement

- The *floorplan* defines overall chip dimensions, as well as the locations of input/output ports and major macros.
- Placement involves arranging physical cells onto the defined floorplan *without overlap*, while considering:
  - Logical connectivity (keeping connected cells close to minimize interconnect delay).
  - Performance objectives (e.g., achieving low delay, minimal area, optimal power usage).
  - Design constraints such as legal regions and reserved areas that must not be used for cell placement.

<img width="948" height="392" alt="image" src="https://github.com/user-attachments/assets/a932765a-1336-4b20-8fe6-80516ec8722e" />


### Placement Objectives and Constraints

- **Key goals during placement**:
  - Maintain proximity between logically connected components to minimize signal delay and wire length.
  - Prevent cells from occupying restricted or reserved regions (obeying block boundaries).
  - Satisfy specific timing and power requirements, possibly trading off area for higher performance cells where necessary.
- The physical arrangement directly affects communication delays between elements, critical for ensuring functional and timing correctness.

<img width="951" height="498" alt="image" src="https://github.com/user-attachments/assets/8d9ac854-b7e5-4b9c-b272-590073df0374" />


### Real-world Optimization

- Multiple iterations and strategies are frequently required to balance design goals:
  - Compactness (minimizing area).
  - Minimizing power and signal crosstalk.
  - Ensuring manufacturability.
- The placement phase serves as the foundation for the subsequent routing step, which establishes metal interconnects following optimal paths defined by the component arrangement.

## Key Points

- **Netlist binding** associates abstract logic elements with physical cells possessing real dimensions.
- A **cell library** provides options in terms of cell size, shape, and timing, supporting trade-offs for performance, area, and power.
- The **placement process** determines precise positions for each physical cell on the chip, ensuring no overlaps and optimal connectivity, while honoring reserved and special regions.
- Logical proximity in placement reduces communication delay; thus, connected modules (e.g., flip-flops to related I/O ports) should be placed near each other.
- **Variants of a cell** (different flavors) exist in the library to enable designers to choose between higher speed, larger area, or lower speed, smaller area as per design requirements.
- The placement phase prepares the groundwork for routing, where physical wiring interconnects all placed cells, respecting the placement, connectivity, and design rules.
- All these steps together directly influence the chip’s speed, area, timing closure, and overall manufacturability.
---
# Optimize placement using estimated wire-length and capacitance

This section addresses theoretical principles of **cell placement** in hardware design, focusing on the geometric arrangement of flip-flops and logic gates relative to I/O ports and the consequences on signal integrity and placement optimization. It covers the impact of wire length on capacitance and resistance, the resulting signal degradation, and the necessity for **repeaters/buffers** to maintain signal integrity over long interconnects.

## Core Concepts

### Cell Placement and Floorplanning

**Cell placement** is the process of assigning physical locations for hardware elements (such as flip-flops and logic gates) within a design layout. The primary objective is to place related cells (like flip-flops and their associated logic) close to relevant I/O ports to minimize wire lengths, which directly impacts electrical characteristics.

<img width="949" height="490" alt="image" src="https://github.com/user-attachments/assets/24d87d5b-2a8b-4c26-823b-a48daa6ea7ae" />


- Placing **input flip-flops** closer to input ports improves signal receive quality.
- Placing **output flip-flops** closer to output ports enhances signal transmission.
- In situations with complex interconnections (e.g., logic separated by a large distance between I/O ports), this process becomes more challenging.

### Impact of Wire Length

Long wires between cells introduce parasitic **capacitance** and **resistance**:

- **Capacitance ($C$):** For metal wires running over a substrate, the capacitance can be approximated by  $C = \epsilon \frac{A}{d}$, where $A$ is area, $d$ is distance between plates (here, wire and substrate), and $\epsilon$ is the permittivity of the material.
- **Resistance ($R$):** Resistance of a wire is given by $R = \rho \frac{L}{A}$, with $\rho$ as resistivity, $L$ as length, and $A$ as cross-sectional area.

Longer wires mean:
- Larger $L$ causes increased $R$
- Increase in overall $C$
- Both degrade signal quality due to larger RC time constants.

<img width="950" height="509" alt="image" src="https://github.com/user-attachments/assets/e9f46c74-d620-4ffe-8223-fede84981181" />


### Signal Integrity and Repeaters

**Signal integrity** refers to the faithful transmission of digital signals without significant distortion or delay. As distance increases:
- High $RC$ delays slow transitions (increase **slew**) and can cause signals to degrade before being captured at the receiver.
- If placement leads to excessive wire length, signal integrity is compromised.

**Repeaters (Buffers):**
- Repeaters are inserted along long wires to "refresh" the signal, re-driving it to restore full logic levels and fast transitions.
- Conceptually, breaking one long wire into several shorter segments, each driven by a buffer, reduces the $RC$ timing penalty per segment.

<img width="944" height="490" alt="image" src="https://github.com/user-attachments/assets/12481f16-7f59-49f6-b7bb-2b7f0cf8e138" />


### Slew Rate and Capacitance

**Slew rate** measures the speed at which a signal transition occurs. It is highly dependent on capacitance:
- High $C$ requires more charge ($Q = CV$) to change voltage, thus for a fixed drive strength, transitions become slower.
- Poor slew rates prevent reliable logic capture.

Signal transition should remain within a **permissible range** to ensure reliable operation throughout the design.

### Placement Optimization

**Optimized placement** aims to minimize total wire length and improve signal transmission by:
- Clustering related cells.
- Placing critical elements to minimize direct distances to their respective ports or connected cells.
- Reducing the need for large numbers of repeaters or buffers, which consume additional area and resources.

This process is iterative and involves estimating wire lengths, evaluating resulting capacitances and resistances, and predicting performance impacts before finalizing layout.

## Key Points

- **Strategic cell placement** is critical for signal integrity and reduces the need for additional buffers/repeaters.
- Wire **capacitance and resistance** are primary factors in determining maximum reliable wire lengths.
- **Repeaters/buffers** are essential for maintaining signal quality over large distances but consume extra silicon area.
- **Slew rate** is determined by load capacitance and affects the ability to transition between logic levels reliably.
- *Optimized placement* reduces unnecessary interconnect length, enhances performance, and manages power/area tradeoffs.
- Evaluating the necessity of repeaters and analyzing transition integrity forms part of broader **timing analysis**, which considers all signal paths and delays in detail. 

---

# Final placement optimization

This documentation covers the theoretical foundation and key concepts related to placement optimization and ideal-clock timing analysis in digital circuit design, particularly as applied in open-source hardware and FPGA workflows. Topics include the motivation for buffer insertion, abutment for high-frequency paths, constraints imposed by physical layout, and the procedure and rationale for timing analysis with ideal clocks.

## Core Concepts

### Placement Optimization

- **Placement** is the process of determining the optimal physical locations for logic elements (such as buffers, flip-flops, and gates) on a hardware chip to satisfy design constraints and performance objectives.
- Goals include minimizing wire lengths, signal delays, power consumption, and avoiding congestion, while satisfying design constraints like proximity to inputs/outputs, resource availability, and logical connectivity.

- **Buffer Insertion:** Buffers (or repeaters) are strategically placed to recondition signals that must travel long distances. They ensure the signal at the destination matches the original in timing and waveform integrity, crucial for reliable operation in distributed circuits operating at high frequency.
- Proper buffer placement is dictated by signal transition requirements and the max allowable wire length for a reliable transmission, often determined through data transition analysis.
<img width="953" height="541" alt="image" src="https://github.com/user-attachments/assets/2909d5ca-0f39-4231-a5b8-739b1511c6b9" />


- **Abutment:** High-speed logic blocks are physically placed very close to each other to minimize interconnect delay—sometimes reducing it nearly to zero. This technique is particularly deployed in sections of the design that must operate at very high frequencies (e.g., 2 GHz), where even small delays can affect overall timing.
<img width="955" height="506" alt="image" src="https://github.com/user-attachments/assets/18f18788-f539-4395-8415-59e8f44b9367" />


- **Physical Constraints:** Placement is affected by other hardware resources, such as decoupling capacitors and existing blocks, which can prevent optimal buffer placement in constrained regions. Routing and buffer strategies must adapt to these constraints.
- **Crisscross and Layer Conflicts:** Signal crossings and congestion are resolved in later routing stages, often by assigning connections to different metal layers (e.g., Metal 3 vs. Metal 4).

### Timing Analysis with Ideal Clocks

- After placement optimization, a **timing analysis** is performed with the assumption of *ideal clocks*—meaning the clock signal reaches all relevant elements with zero delay.
- This idealized analysis isolates the data path timing and helps identify whether the placement and buffer insertion will satisfy the required frequency specifications before real, non-ideal clocks are considered.
- *Setup timing analysis* checks that data can propagate through the placed logic within the clock period. If setup timing fails in ideal conditions, adjustments to placement are needed before advancing to more detailed (and less forgiving) timing analyses.
- Notably, *hold analysis* is not meaningful under ideal clocks, as all flip-flops are triggered simultaneously with zero skew or latency.

## Key Points

- Placement optimization directly impacts the performance and reliability of digital circuits by configuring physical locations of all elements to mitigate delays and maximize throughput.
- Buffer insertion is crucial when signals must traverse long wires; correct placement ensures high-fidelity signal reproduction, essential for synchronous operation at high frequencies.
- Adjacency (abutment) of fast-path logic blocks eliminates unnecessary wire delays and is a common strategy for segments of a circuit operating at the highest speeds.
- Physical layout often imposes constraints, such as regions reserved for capacitors or other blocks, necessitating route adjustments or alternative buffer placements.
- Layered routing is used to resolve crisscrossed or overlapping connections in complex layouts, ensuring logical connectivity without shorts or resource conflicts.
- Timing analysis with ideal clocks is a vital verification step post-placement, providing an early check on the feasibility of meeting timing specifications and informing further optimization if necessary.

- Setup timing is expressed as:  
  $T_{setup} \leq T_{clock} - (T_{comb} + T_{routing} + T_{clock\_q})$  
  Where:  
  $T_{setup}$ = Setup time requirement  
  $T_{clock}$ = Clock period  
  $T_{comb}$ = Combinational logic delay  
  $T_{routing}$ = Routing (wire/buffer) delay  
  $T_{clock\_q}$ = Flip-flop clock-to-q delay

- Meeting setup timing in ideal clocks is a prerequisite; failure here indicates the need for placement changes before more nuanced routing and real-clock analysis.
- Optimizing at this stage reduces complexity in subsequent (more computationally intensive) placement and routing phases as well as the risk of failure due to unmet timing or congestion.

# Need for libraries and characterization

This document outlines the **theoretical foundations** of integrated circuit (IC) design flow, with a focus on how each stage interacts with library characterization. The stages include logic synthesis, floorplanning, placement, clock tree synthesis, routing, and static timing analysis, culminating in the necessity for comprehensive gate characterization. Each stage relies on accurate cell models to achieve targeted functionality, timing, and manufacturability.


<img width="674" height="73" alt="image" src="https://github.com/user-attachments/assets/9ea88100-0213-4102-afd8-9a2be019007b" />

---

## Core Concepts

### IC Design Flow Overview

The **IC design flow** is a stepwise methodology used to convert a high-level functional description into a manufacturable circuit. It integrates hardware description languages, logical arrangement of gates, and physical placement, all orchestrated to meet specific design goals (functionality, area, speed, power). Library characterization underpins each step by providing the necessary cell models and parameters that design tools use.


### Logic Synthesis

**Logic synthesis** transforms a high-level functionality (typically specified in RTL) into an arrangement of gates—such as AND, OR, buffers, and flip-flops—that implements the desired hardware. The synthesized netlist is a gate-level representation optimized for area, speed, and power, guided by the characteristics of the cell library. 

<img width="347" height="304" alt="image" src="https://github.com/user-attachments/assets/10af2e50-e00f-4f19-8fda-4df56dc9c981" />

### Floorplanning

**Floorplanning** imports the gate-level netlist and translates it into a spatial layout. Decisions regarding the width and height of the core and die are based on the quantity and sizes of gates. The shaping and area allocation directly depend on the gate properties defined during characterization, impacting all downstream physical design phases.

<img width="534" height="308" alt="image" src="https://github.com/user-attachments/assets/7563563f-3fa8-46d6-9584-3c2c9083c373" />


### Placement

**Placement** arranges individual logic cells onto the chip to optimize for timing and connectivity. Logical proximity—dictated by netlist connections—drives physical closeness, aiming to minimize path delays (including setup and hold time violations). Placement strategies may adjust cell distances and introduce buffers to maintain design targets, all relying on accurate cell timing models.

<img width="656" height="308" alt="image" src="https://github.com/user-attachments/assets/13a44069-086e-4e4d-8733-0a07a06a3ee6" />


### Clock Tree Synthesis

**Clock tree synthesis** ensures that the clock signal reaches all destinations (clock endpoints) simultaneously, achieving minimal skew. Buffer cells are inserted so every point receives the clock with equal rise/fall times. This process is critically dependent on the timing and electrical characteristics provided by the library for each buffer and flip-flop.

<img width="732" height="329" alt="image" src="https://github.com/user-attachments/assets/7cf9dcc2-edf9-4bd9-8070-ff8946a0a351" />


### Routing

**Routing** connects cell terminals according to logical and physical constraints. Techniques like maze routing consider cell properties, such as output drive strength and input capacitance, to maintain signal integrity and timing. The routing step must honor cell specifications laid out in the characterization phase.

<img width="853" height="322" alt="image" src="https://github.com/user-attachments/assets/b1dd2218-eff4-472b-af2c-60ddcf5d6553" />


### Static Timing Analysis

**Static timing analysis (STA)** evaluates timing paths to identify potential setup and hold violations and calculates the maximum achievable frequency. Core equations include:

- Data Arrival Time:
  - $T_{arrival} = T_{launch} + T_{delay}$
- Setup and Hold Requirements:
  - $T_{setup} \leq T_{clk} - T_{arrival}$
  - $T_{hold} \geq T_{arrival} - T_{clk}$

The accuracy of STA is fundamentally tied to the timing data characterized for each cell.

<img width="567" height="351" alt="image" src="https://github.com/user-attachments/assets/d064f264-5b26-4748-ab55-44f4a09b5fe6" />


### Library Characterization

At every step, the design process references **standard cells** (AND, OR, buffer, inverter, flip-flop, latch) from the cell library. These cells must be characterized for timing, electrical, and physical properties so EDA tools can interpret their behavior accurately. Without detailed models, the design tools cannot guarantee proper functionality or timing.

<img width="682" height="411" alt="image" src="https://github.com/user-attachments/assets/68660076-8aaf-4a18-94ec-5e8c3325a17d" />

---

## Key Points

- Each stage of IC design flow—logic synthesis, floorplanning, placement, clock tree synthesis, routing, and timing analysis—relies on accurate and comprehensive library characterization.
- A **cell library** consists of gate definitions and their associated models, representing the fundamental building blocks used throughout the design flow.
- **Library characterization** is the process of modeling the gates so that EDA tools can understand their functional, timing, and physical behavior.
- Timing equations (e.g., setup time, hold time) are used to ensure reliable operation:
  - Typical setup time analysis: $T_{setup} = T_{clk} - T_{arrival}$
  - STA checks for violations that could affect performance.
- Physical design stages (floorplanning, placement, routing) depend on accurate area and delay numbers from characterization, directly affecting manufacturability and performance.
---
# Congestion aware placement using RePlAce

This lab focuses on the **placement stage** in digital design using open-source hardware tools. The main aim is to perform placement with an emphasis on congestion reduction rather than timing optimization. Placement consists of two distinct phases: **global placement** and **detailed placement**. In this lab, you will use relevant commands and tools to optimize wire length, legalize the placement of standard cells, and verify results visually. The process includes observing parameters such as **half perimeter wire length (HPWL)** and **overflow**, checking standard cell alignment, and viewing the design post-placement using visualization tools.

## Lab Steps

1. **Run Placement**

   - Placement occurs in two stages:
     - **Global Placement**: Coarse positioning aimed at reducing overall wire length.
     - **Detailed Placement**: Legalizes cell positions, ensuring no overlaps and proper alignment within standard cell rows.
  
   - Start the placement run:
     ```
     run_placement
     ```
     <img width="893" height="883" alt="image" src="https://github.com/user-attachments/assets/ac59b027-7501-49ff-a1f6-2be48b44c0e8" />
<img width="894" height="674" alt="image" src="https://github.com/user-attachments/assets/f56fe55f-d8a5-4d00-81d9-5b7ef991c3f7" />


2. **Monitor Algorithm and Output Metrics**

   - During **global placement**, the key metric is **HPWL (half perimeter wire length)**. The goal is to minimize this value for better placement quality.
   - Another critical parameter is **overflow**. Convergence is indicated when the overflow value decreases through iterations.
     ```
     # Example output
     HPWL: 150000
     Overflow: 1200
     HPWL: 140000
     Overflow: 800
     ...
     ```

3. **Verify Legalization After Placement**

   - In **detailed placement**, the process ensures that all standard cells are:
     - Properly aligned inside standard cell rows.
     - Have no overlaps.
     - Legalized from physical and timing standpoints.

4. **Visualize Placement with Magic Tool**

   - Once placement is complete, view the resulting layout:
     ```
     magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &
     ```
     <img width="718" height="710" alt="image" src="https://github.com/user-attachments/assets/b2f21a49-aad2-4143-88b1-43887ee12af8" />


   - Use Magic’s visualization features to inspect:
     - Centralized standard cell clusters
     - Correct placement of single cells (often observed in the lower left corner after floorplan)
     - Alignment of TAP cells and IO pads as set by the floorplan
     <img width="1152" height="863" alt="image" src="https://github.com/user-attachments/assets/cb7dcfd1-6667-4c2a-b7fa-6bb9e00607ac" />
<img width="881" height="711" alt="image" src="https://github.com/user-attachments/assets/f54ec05d-3a98-4215-87f6-be7b5c5e559e" />


   - Zoom in to check detailed placement of standard cells within their rows.
     <img width="906" height="641" alt="image" src="https://github.com/user-attachments/assets/c7046e12-cdae-4e16-9622-a87044bffa79" />


5. **Review Standard Cell Placement and Boundary Conditions**

   - Confirm:
     - **No DRC (Design Rule Check) violations**
     - Standard cells are correctly placed within rows and boundaries
     - Decap cells are at the boundaries, TAP cells and IO pads are properly positioned per floorplan output
     <img width="1083" height="34" alt="image" src="https://github.com/user-attachments/assets/430bb562-ddf3-4d0e-9ad6-8332c3dcf521" />


6. **Save the Design State**

   - Save visualization or DEF file for reference.
     ```
     save <output-name>.def
     ```

7. **Next Steps**
     
   - Note: The **Power Distribution Network (PDN)** is not yet created. In this flow, PDN generation happens after placement and CTS, before routing.

---
# Cell design and characterization flows

# Inputs for cell design flow
This documentation provides a comprehensive theoretical overview of the cell design flow in integrated circuit (IC) development, with an emphasis on the concept and role of standard cells, standard cell libraries, and the foundational elements provided by foundries. It gives a high-level understanding of how standard cells are prepared for use in digital design, the types of information housed in cell libraries, and the key inputs required to design a basic cell such as an inverter.

<img width="581" height="481" alt="image" src="https://github.com/user-attachments/assets/5817a31d-da80-42e6-8a13-8e2ca9dbb5e3" />


## Core Concepts

### Standard Cells

- **Standard cells** are basic logic gates or functional blocks (e.g., AND gates, OR gates, inverters, buffers, flip-flops) used repeatedly in digital IC design.
- These cells are pre-designed, characterized, and stored in a **library** for reuse in placing and routing digital designs.

<img width="854" height="469" alt="image" src="https://github.com/user-attachments/assets/75606831-aa5a-49cc-8231-116bf8ebedc2" />


### Standard Cell Library

- The **library** is a foundational component in the IC design flow, analogous to a bookshelf containing a wide variety of books—here, it contains standard cells and possibly other blocks such as decoupling capacitors, macros, and IPs.
- Libraries contain cells with:
  - **Different functionalities:** e.g., logic gates, storage elements.
  - **Different sizes:** This refers to *drive strengths*, where a physically larger cell can source/sink more current, hence better drive longer or heavily loaded wires.
  - **Different threshold voltages ($V_{t}$):** Cells that differ in $V_{t}$ are optimized for different performance/leakage trade-offs. For example, a cell with lower $V_{t}$ switches faster but may leak more.

<img width="845" height="533" alt="image" src="https://github.com/user-attachments/assets/68e50d62-fc01-479f-9105-f4306448f6ce" />


### Physical and Electrical Characteristics

- **Physical size:** Affects drive strength, generally with larger devices providing greater output current.
- **Threshold voltage ($V_{t}$):** Influences both speed and leakage power. Lower $V_{t}$ means faster switching but more leakage; higher $V_{t}$ means slower switching but less leakage.
- Libraries may contain otherwise identical cells that differ only in $V_{t}$ or drive strength, supporting flexible design optimization.

<img width="844" height="518" alt="image" src="https://github.com/user-attachments/assets/5261d841-965e-4b31-8b96-d1de8f8a4de7" />


### Cell Representation for EDA Tools

- Each cell is represented not just by its schematic symbol but also by:
  - **Physical shape:** For placement/layout.
  - **Timing characteristics:** Delay, setup/hold times.
  - **Drive strength and load capabilities.**
  - **Power consumption data:** Both static and dynamic.

EDA (Electronic Design Automation) tools use these attributes to optimize the placement, timing closure, and power characteristics of the final chip.

### Cell Design Flow Overview

The cell design flow can be divided into three main categories:
1. **Inputs:** Requirements and constraints provided by the foundry and users.
2. **Cell Design:** Application of rules and models to create a functional cell.
3. **Outputs:** Characterized models used by EDA tools.

This explanation focuses on the **inputs** involved in designing a cell such as an inverter.

<img width="804" height="549" alt="image" src="https://github.com/user-attachments/assets/def229b8-5643-4798-98e1-a5c582481e6c" />


#### Inputs to Cell Design

- **Process Design Kits (PDK):**
  - Provided by the semiconductor foundry.
  - Includes:
    - **Design Rule Check (DRC) and Layout Versus Schematic (LVS) rules:** Dictate layout restrictions (e.g., minimum/maximum widths, spacing, overlaps) to ensure manufacturability and correct function.
    - Required for custom layout and standard cell development.

- **SPICE Model Parameters:**
  - Also from the foundry.
  - Provide electrical models for transistor behavior used in simulation and characterization.
  - Include key physical/electrical parameters, such as:
    - **Oxide thickness** ($t_{ox}$), e.g., $t_{ox} = 5.8\,nm$
    - **Nominal threshold voltages** ($V_{t}$), e.g., $V_{t} = 0.5\,V$
    - **Capacitance values:** (e.g., $C_{ox}$, $C_{gs}$, $C_{gd}$, etc.)
    - **Substrate and process-specific constants**

  Example SPICE equations:
  - Threshold voltage:
    - $V_{T} = V_{T0} + \gamma \left( \sqrt{|2\phi_B + V_{SB}|} - \sqrt{|2\phi_B|} \right)$
  - Oxide capacitance:
    - $C_{ox} = \frac{\varepsilon_{ox}}{t_{ox}}$

- **Library and User-Defined Specifications:**
  - Custom requirements, functional specifications, and any additional constraints or targets for the cell.

<img width="868" height="551" alt="image" src="https://github.com/user-attachments/assets/10012b2e-2bf9-4908-a3ee-82393d683928" />


#### Design Rule Examples

- **Poly width minimum:** $2\lambda$
- **Poly extension over active area:** $3\lambda$
- **Poly to active spacing:** $1\lambda$

*These rules help ensure manufacturability and reliable device operation. There are typically thousands of such rules in a full process design kit.*

<img width="861" height="548" alt="image" src="https://github.com/user-attachments/assets/8b82516d-f1d5-4f01-b843-d12abbc3d906" />

## Key Points

- **Standard cells and libraries** are fundamental building blocks for digital IC design, enabling modular design reuse.
- Libraries contain functionally diverse, size-varied, and electrically distinct versions of each standard cell for design flexibility.
- **Drive strength** and **threshold voltage ($V_{t}$)** are critical characteristics used to tailor standard cell performance to the needs of the chip.
- The **cell design flow** relies heavily on detailed inputs from the foundry:
  - **Physical design/spacing rules** (DRC, LVS)
  - **SPICE models and parameters** for accurate electrical characterization.
- Each cell must be thoroughly described for placement, timing, drive/power, and verified against foundry-provided rules and models.
- The process ensures that custom or standard cells are manufacturable, meet performance targets, and are compatible with automated design tools.

---
# Circuit design step

This document explains the theoretical foundations behind user-defined specifications in standard cell library development for open-source hardware design. Key concepts discussed include the relationship between physical design parameters and circuit performance requirements, as well as the primary design stages of standard cell creation: circuit design, layout design, and characterization.

## Core Concepts

### User-Defined Specifications in Standard Cell Libraries

User-defined specifications drive the development of standard cell libraries by dictating the physical and electrical properties cells must meet to integrate successfully into semiconductor design environments.

- **Cell Height** is determined by the separation between the power rail (VDD) and the ground rail (VSS). The library developer is responsible for maintaining this height based on the provided specifications.

  <img width="841" height="535" alt="image" src="https://github.com/user-attachments/assets/bcc9806b-3e22-48be-a8c1-681047a49c83" />


- **Cell Width** is related to the drive strength required by the user. Drive strength indicates a cell’s ability to drive electrical load.
  - Low drive strength ($\text{Drive Strength} = 1$) makes a cell less capable of controlling large wire loads.
  - High drive strength (e.g., $\text{Drive Strength} = 10$) allows the cell to drive longer or heavily loaded wires.


- **Supply Voltage** is specified at the top level. The standard cell must be designed to operate at this specified voltage, influencing transistor sizing and ensuring correct noise margins. Typical values might include $V_{dd} = 1.2\,V$, and the threshold voltage needs to be considered for noise immunity.

  <img width="835" height="534" alt="image" src="https://github.com/user-attachments/assets/468d54ae-d66c-4786-ab4e-c9aa39f284b2" />


- **Metal Layer Requirements** define which layers (e.g., Poly, Metal1, Metal3) are used for different cell components (e.g., power, ground, signal contacts). Specific design constraints can require certain connections or contacts to occupy particular metal layers for routing or process compatibility.

  <img width="851" height="542" alt="image" src="https://github.com/user-attachments/assets/ebeab1b1-3e24-43a9-b90b-107e00b01bc2" />


- **Pin Locations**: The physical placement of cell pins (inputs/outputs) often needs to respect guidelines, such as proximity to power and ground rails, to ease integration with downstream physical design tools.

  <img width="837" height="535" alt="image" src="https://github.com/user-attachments/assets/90f60e3e-0ea5-4f02-981f-16ca71b58af8" />


- **Drawn Gate Length**: The gate length of MOS transistors may have slight process-provided variations (e.g., for 130nm technology, typical variation might be drawn lengths of $125\,nm$ or $135\,nm$), often explicitly set by user or process constraints.

  <img width="825" height="534" alt="image" src="https://github.com/user-attachments/assets/75cc1efd-dbe4-4b54-bd48-7eba5f29d0c1" />


## Key Points

- **Spec Driven Design**: The library developer must carefully interpret and implement all user-defined specifications so that cells promote successful integration through every stage of the chip design flow.

  <img width="319" height="487" alt="image" src="https://github.com/user-attachments/assets/b88214cc-34c5-478e-9272-1fc9fb46cdd4" />


- **Design Process Stages**:
  - **Circuit Design** involves two core steps:
    1. **Functional Implementation**: Defining the logic function the cell will implement.
    2. **Transistor Sizing**: Modelling PMOS and NMOS transistors, primarily optimizing width-to-length ($W/L$) ratios to meet electrical specifications like drive strength and switching threshold.
       - Example relationship: $W_p = 2.5 \times W_n$, where $W_p$ and $W_n$ are widths for PMOS and NMOS, respectively.
       - The drain current requirement must be met:
         
         $I_{D, p} + I_{D, n} = 0$
         
         The switching threshold voltage is set to a target value (e.g., $V_{th} = 1.2\,V$) based on the application.

    <img width="801" height="522" alt="image" src="https://github.com/user-attachments/assets/2ee0805b-da99-478c-9cb1-71fd7d943e40" />
<img width="843" height="534" alt="image" src="https://github.com/user-attachments/assets/b06f4127-42b8-4904-a90b-529c4ae59736" />


  - **Layout Design** translates the circuit-level transistor dimensions and connectivity into physical mask layouts. Steps include:
    - Arranging series and parallel PMOS/NMOS networks derived from the logic structure.
    - Mapping the exact dimensions and locations as dictated by the $W/L$ ratios and physical constraints from user-defined specs.

    <img width="804" height="504" alt="image" src="https://github.com/user-attachments/assets/48244725-05fd-43b1-b0a5-3085707afc74" />

  - **Characterization** (mentioned as a future elaboration) ensures the cell's timing, noise margin, and other properties meet requirements under realistic conditions.

- **Integration into Top-Level Design Flow**: Adhering to specifications ensures the cells are compatible with higher-level steps such as clock tree synthesis, placement, and routing, thus avoiding physical design conflicts.

---
# Layout design step

This section elaborates on the theoretical process of digital layout design for standard cells, emphasizing the translation from circuit function to physical layout, and the requirements imposed by both foundry rules and user specifications. Key steps include network graph derivation, Euler path extraction, stick diagram generation, and the final production of manufacturable layouts adhering to design rule checks (DRC). Parasitic extraction and cell characterization follow to ensure timing, noise, and power compliance.

## Core Concepts

### Function-to-Transistor Network Mapping

The initial theoretical step involves implementing a specified logic function using connections of PMOS and NMOS transistors. Each given function corresponds to a specific transistor configuration, forming the foundation for all later physical abstractions.

<img width="810" height="333" alt="image" src="https://github.com/user-attachments/assets/c5783ffe-de4c-4ccd-9990-b813dd88cd03" />


### Network Graphs and Euler Path

After mapping the function, separate PMOS and NMOS network graphs are constructed. These graphs represent the connectivity of transistor sources and drains as dictated by the logic function.

An Euler path is then sought in each network graph. An Euler path is a sequence through a graph that visits each edge exactly once. In layout design, finding such a path enables a more compact arrangement of transistors, optimal for area and performance.

- *Euler path*: A path through a graph where each edge is visited only once.

<img width="477" height="317" alt="image" src="https://github.com/user-attachments/assets/a897100d-38cc-4765-9058-c659011127bd" />


### Stick Diagram Generation

With the Euler path established, a stick diagram is produced. This abstract representation shows the sequence of gate and diffusion connections as simplified lines and contacts, encoding the order derived from the Euler path.

- Input sequence (e.g., ACEF, DNB) guides polygate placement and interconnections.
- Sources, drains, and gates are abstracted, emphasizing topological relations over geometric detail.

<img width="526" height="290" alt="image" src="https://github.com/user-attachments/assets/920e88ab-e950-4879-afdb-db34a22eadd5" />


### Physical Layout Adhering to DRC and Specifications

The stick diagram is translated into a detailed physical layout, observing strict Design Rule Check (DRC) guidelines provided by the semiconductor foundry. User-defined specifications such as gate length, contact locations, and pin placements are incorporated at this stage.

- Multiple metal layers (metal 1, metal 2, etc.) and polysilicon are organized to match functional and manufacturing requirements.
- The cell's physical dimensions (width, height) emerge from these constraints and optimization strategies.

<img width="451" height="381" alt="image" src="https://github.com/user-attachments/assets/5494a06b-565c-4801-9a7a-12449d86924b" />


### Layout Verification and Output Formats

The resulting hand-drawn layout transitions to a digital form using layout editing tools (e.g., open-source tool "magic"). Outputs typically include the GDSII (GDS2) layout file, which contains all geometrical information necessary for fabrication.

- GDSII format specifies component boundaries and facilitates manufacturing.
- The layout must comply with all DRC rules to ensure manufacturability.

### Parasitic Extraction and Cell Characterization

Parasitic extraction derives the resistance and capacitance ($R$, $C$) of interconnects and devices directly from the drawn layout. This data is compiled into a SPICE netlist, integrating the extracted parasitic values.

- Extracted parameters are key for precise simulation of timing and electrical behavior.

Characterization uses the SPICE netlist to compute:

- Timing metrics
- Noise margins
- Power consumption

The results are captured in standard output files (e.g., LIB, .lib files), defining the functionality and performance of the cell for broader use in digital circuit synthesis.

<img width="792" height="530" alt="image" src="https://github.com/user-attachments/assets/c2e31e8b-f07d-4f1e-bdfa-04f3d0c6212c" />


## Key Points

- **Logical function** is implemented using PMOS/NMOS transistor networks specifically arranged to realize that function.
- **PMOS and NMOS network graphs** clarify transistor connections and provide a basis for area-optimized transistor placement.
- **Euler path** in the network graph yields a single continuous transistor ordering, minimizing diffusion breaks and yielding compact layouts.
- **Stick diagrams** abstract transistor and interconnect arrangements before formal layout, bridging logical topology and physical design.
- **Design Rule Constraints (DRC)** and user specifications rigorously define feature sizes, contact/metal placements, and cell boundaries.
- **Layout editing tools** (e.g., magic) convert abstract representations into fabrication-ready GDSII files.
- **Parasitic extraction** ensures realistic modeling of circuit performance by accounting for layout-induced electrical effects ($R$, $C$).
- **Cell characterization** translates physical properties into timing, noise, and power metrics required for higher-level digital design automation.

---
# Typical characterization flow

This section covers the theoretical foundations and key ideas behind the cell-level characterization flow in custom digital design using open-source hardware tools. The focus is on understanding the role and nature of different inputs, the stepwise methodology followed in industry-standard flows, and the classification of resulting models used in timing, power, and noise analysis.

## Core Concepts

### Inputs to Characterization

- **Layout**: A fully designed geometric representation of a cell, such as a buffer or inverter, typically created with open-source physical layout tools. Each component in the layout (like metal layers and contacts) corresponds to electrical properties including resistance and capacitance.

  <img width="292" height="330" alt="image" src="https://github.com/user-attachments/assets/de6ccc22-5aeb-4a39-a26d-a2648c62eff3" />


- **Cell Description**: Specifies how basic elements (e.g., inverters in a buffer) are interconnected, detailing their relationships and connection to power sources.

  <img width="829" height="452" alt="image" src="https://github.com/user-attachments/assets/62c46762-dd57-4ed5-907a-58d1cd17fa99" />


- **Extracted SPICE Netlist**: Produced from the layout, this netlist includes all electrical components (transistors, resistors, capacitors) derived from the physical geometry. Parasitive resistances and capacitances are included, enabling accurate simulation of real-world circuit behavior.

  <img width="273" height="252" alt="image" src="https://github.com/user-attachments/assets/9d90b1a2-2dd1-491a-be88-6409a4c200f1" />


- **Subcircuits and SPICE Models**: Subcircuits define reusable blocks such as inverters, instantiated multiple times in a design. SPICE device models (for NMOS and PMOS) represent the electrical characteristics of each transistor, capturing properties like threshold voltage ($V_{TH}$), doping, and process-specific behaviors.

<img width="713" height="340" alt="image" src="https://github.com/user-attachments/assets/5ce74d32-2fb8-4df8-9123-ed55e62a4d5c" />


### Characterization Flow: Stepwise Approach

A typical industry-standard cell characterization flow consists of these ordered steps:

1. **Read Model Files**: Import SPICE models for all device types (e.g., NMOS, PMOS), specifying their electrical characteristics.
2. **Read Extracted SPICE Netlist**: Load the complete netlist, including parasitic resistances and capacitances, detailing physical layout effects.
3. **Recognize Buffer Behavior**: Analyze the netlist to understand the logical and functional behavior of the cell (e.g., identify it as a buffer).
4. **Read Subcircuits**: Import subcircuit descriptions for any instantiated building blocks (such as inverters).
5. **Attach Power Sources**: Ensure all supply connections (e.g., $V_{DD}$, ground) are present and correctly tied to the cell.
6. **Apply Stimulus**: Define the types of input stimuli to drive the simulation, essential for exercising and characterizing circuit response.
7. **Specify Output Capacitance**: Configure external loading (e.g., a 10 fF output load) which may be varied for nonlinear delay or load-dependent measurements.
8. **Add Simulation Commands**: Choose the analysis type (e.g., transient, DC), specifying simulation directives (e.g., `.tran`) required for the characterization.

These steps are typically compiled into a configuration file supplied to characterization software.

<img width="867" height="554" alt="image" src="https://github.com/user-attachments/assets/d0893a14-ccac-48ac-986f-4dddc1388b84" />
<img width="862" height="524" alt="image" src="https://github.com/user-attachments/assets/416ff095-40f1-4ccc-8985-3cbaf5914a4e" />


### Role of Characterization Software

- The configuration file encapsulating all inputs is provided to a characterization tool (e.g., GUNA).
- The software uses these inputs to perform simulations and extract *timing*, *noise*, and *power* data.
- Generated models form the basis for further digital design flows and are essential for accurate design closure.

<img width="873" height="540" alt="image" src="https://github.com/user-attachments/assets/d14d835c-ca8d-4043-bb3f-d931127844c3" />


### Model Classification

The resulting data from characterization flow is normally categorized as:

- **Timing Characterization**: Determines delays, setup/hold times, and signal propagation characteristics of the cell under varying conditions.
- **Power Characterization**: Quantifies the dynamic and static power consumption based on expected usage and switching patterns.
- **Noise Characterization**: Assesses the cell's behavior in the presence of noise, indicating susceptibility or robustness.

These comprehensive models are critical for assembling higher-level digital blocks and ensuring reliable design at system scale.

<img width="852" height="253" alt="image" src="https://github.com/user-attachments/assets/20897fac-c2e9-412b-8987-0c67b75a33d4" />


## Key Points

- Accurate cell characterization relies on integrating layout, netlist extraction (including physical parasitics), libraries of subcircuits, and accurate SPICE models for devices.
- The characterization flow is a formalized, stepwise process culminating in detailed functional, power, and noise models applicable in subsequent simulation and analysis.
- Output models (often in the form of library files such as `.lib`) are divided by the type of analysis: timing, power, and noise.
- All simulations depend fundamentally on the integrity of the device models and quality of the physically extracted netlist.
- Stimulus and load selection are critical: choices made at this stage impact the representativeness and utility of generated timing and power models.

---
# General timing characterization parameters

# Timing threshold definitions
