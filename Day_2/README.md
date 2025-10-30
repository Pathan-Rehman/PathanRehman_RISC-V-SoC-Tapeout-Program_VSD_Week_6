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
