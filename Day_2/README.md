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

![PLACEHOLDER: screenshot showing runs folder contents with date-based directories]

### Step 2: Verify Configuration Parameter Precedence

Move into the logs directory for the current run to examine the floorplan logs.

```bash
cd logs
cd floorplan
```

![PLACEHOLDER: screenshot showing logs directory structure]

### Step 3: Examine IO Placer Log File

Open the IO placer log file to verify that design-level configuration parameters have overridden system defaults. Specifically, check the metal layer assignments.

```bash
# Open the io_placer.log file (or similar)
cat io_placer.log
```

Search for metal layer information using shift+G to navigate within the file viewer.

![PLACEHOLDER: screenshot showing io_placer.log with vertical and horizontal metal layer specifications]

The log should display:
- **Vertical metal layer**: 5 (set by design config)
- **Horizontal metal layer**: 3 (set by design config)

### Step 4: Compare with Design Configuration File

Navigate to the design folder and examine the config.tcl file to verify the source configuration values.

```bash
cd ../../design
cat config.tcl
```

![PLACEHOLDER: screenshot showing config.tcl with metal layer and core utilization parameters]

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

![PLACEHOLDER: screenshot showing run-specific config.tcl with parameter values and priority notes]

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

![PLACEHOLDER: screenshot showing results/floorplan directory with DEF file]

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

![PLACEHOLDER: screenshot showing DEF units specification and coordinate calculations]

### Step 9: Open Floorplan in Magic Layout Editor

Launch Magic to visualize the floorplan layout. First, identify the tech file location and construct the Magic command.

The tech file is typically located at a path similar to:

```
.../tech/sky130_open_pdk_public_release_tech_file.tech
```

Navigate to the results directory containing the DEF file and run Magic:

```bash
magic -T [TECH_FILE_PATH] &
```

For example:

```bash
magic -T ../../tech/sky130_open_pdk_public_release_tech_file.tech &
```

![PLACEHOLDER: screenshot showing Magic command in terminal]

### Step 10: Load the Floorplan Layout

In Magic, load the merged LEF and DEF files. The merged LEF file (merge.lef) is typically located in the temp folder, two levels up from the current directory.

```bash
# Within Magic console or script:
lef read ../../temp/merge.lef
def read merge.def
```

![PLACEHOLDER: screenshot showing Magic loading LEF and DEF files]

### Step 11: Visualize the Floorplan

Once the files are loaded in Magic, the floorplan visualization will display:

- All placed cells and their orientations
- Metal layers and routing tracks
- Die boundaries
- IO placement around the perimeter

![PLACEHOLDER: screenshot showing Magic window with floorplan visualization displaying cells, metal layers, and die area]

Use Magic's navigation tools (zoom, pan) to inspect different regions of the floorplan and verify that the configuration parameters have been correctly applied during floorplanning.
