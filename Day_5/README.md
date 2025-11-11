# Table of contents

- [Introduction to Maze Routing – Lee’s algorithm](#introduction-to-maze-routing--lees-algorithm)
- [Lee’s Algorithm conclusion](#lees-algorithm-conclusion)
- [Design Rule Check](#design-rule-check)
- [Lab steps to build power distribution network](#lab-steps-to-build-power-distribution-network)
- [Lab steps from power straps to std cell power](#lab-steps-from-power-straps-to-std-cell-power)
- [Basics of global and detail routing and configure TritonRoute](#basics-of-global-and-detail-routing-and-configure-tritonroute)
- [TritonRoute feature 1 - Honors pre-processed route guides](#tritonroute-feature-1---honors-pre-processed-route-guides)
- [TritonRoute Feature2 & 3 - Inter-guide connectivity and intra- & inter-layer routing](#tritonroute-feature2--3---inter-guide-connectivity-and-intra---inter-layer-routing)
- [TritonRoute method to handle connectivity](#tritonroute-method-to-handle-connectivity)
- [Routing topology algorithm and final files list post-route](#routing-topology-algorithm-and-final-files-list-post-route)

# Introduction to Maze Routing – Lee’s algorithm

This section focuses on the routing and design rule checking (DRC) phases within the physical design flow of digital VLSI systems. It introduces the theoretical basis of routing as the algorithmic process of connecting source and target points on silicon using optimal paths, details the conceptual operation of maze routing (Lee's algorithm), and highlights key constraints and considerations such as blockages and grid-based search spaces.

## Core Concepts

### Routing in Physical Design

- **Routing** is the stage where physical interconnections are created between specified points, typically nets between logic elements such as the output of one flip-flop and the input of another.
- From an algorithmic standpoint, routing is abstracted as a problem of identifying the optimal path from a **source** to a **target** within a constrained layout, considering blockages and design rules.
- The goal in routing is to minimize path cost, which may correspond to wirelength, number of vias, or signal integrity factors like crosstalk and delay.
- The connection should ideally have minimal bends, with a preference for L-shaped paths, minimal Z-shaped paths, and avoidance of excessive twists.

<img width="1630" height="822" alt="image" src="https://github.com/user-attachments/assets/aeb9500f-8b69-4fd3-a6e8-121213f1b927" />


### Grid-Based Routing

- Routing algorithms commonly use a **grid-based model** in which the silicon area is overlaid with a routing grid. Points to be connected are mapped onto specific grid coordinates.
- Obstacles and blockages (such as macros or reserved zones) are marked as impassable grid locations. The routing algorithm must circumvent these areas.
- The grid defines the set of permissible paths and ensures routing constraints are respected.

<img width="1113" height="783" alt="image" src="https://github.com/user-attachments/assets/0462d505-3034-4440-a7c9-b686a38d3d7d" />


### Maze Routing (Lee's Algorithm)

- **Lee's Algorithm (Maze Routing)** solves the shortest path problem using a breadth-first search on the routing grid.
- The algorithm operates as follows:
  - The **source** grid location is labeled with the value $0$.
  - All adjacent (orthogonal, not diagonal) grid cells are labeled with $1$.
  - Each subsequent wavefront increments the label, so any unlabeled, immediately adjacent cell is labeled with $n+1$, where $n$ is the label of the parent cell.
  - This process continues outward until the **target** is reached.
  - The shortest route is extracted by tracing labels backward from target to source, selecting at each step the adjacent cell with a decrementing value.

- This algorithm guarantees the shortest possible path in the absence of diagonal moves or cost variations between moves.

- Limitation: **Maze routing is computationally intensive and not scalable for very large grids due to its exhaustive expansion of possibilities. Still, it provides the fundamental basis for optimal path-finding in automated routing.**

<img width="952" height="772" alt="image" src="https://github.com/user-attachments/assets/a25c0e55-1d74-4ac8-afbe-53a86e8aff42" />


### Obstacles and Constraints

- Physical blockages (e.g., macro blocks) are treated as impassable by the routing algorithm and are explicitly excluded from the wavefront propagation.
- Only vertical and horizontal moves are considered in the propagation and label assignment process.
- Each grid cell is labeled only once—the first time it is reached—ensuring that the shortest path metric from the source is maintained.

<img width="932" height="761" alt="image" src="https://github.com/user-attachments/assets/c8604793-53d4-46bb-b1b2-fea9e9160dff" />


## Key Points

- **Routing** transforms logical netlist connections into explicit geometric wire paths within the chip, strictly adhering to physical design rules and avoiding non-routable blocks.
- **Grid-based routing** enables algorithmic ease but can be limited by grid resolution and obstacle complexity.
- **Lee's Algorithm** is an archetypal breadth-first search routing strategy that reliably identifies the shortest source-to-target path by iterative labeling, but may struggle with efficiency at large scale.
- Efficient routing is critical to minimize wirelength, area, and timing, while ensuring manufacturability and testability.
- Design Rule Checking (DRC) is a subsequent validation step, ensuring all routed paths conform to technological and manufacturability constraints.

---

# Lee’s Algorithm conclusion

Lee's algorithm is a fundamental pathfinding method, widely used in maze routing for connecting two points on a grid while navigating blockages and minimizing bends. It operates through a wavefront expansion from the source towards the target, systematically labeling adjacent grid cells in increasing order until the target is reached. The algorithm inherently produces shortest-path solutions, with routes minimizing the number of turns always preferred for efficient circuit layout design. However, Lee's approach can be resource-intensive, prompting the use of alternative algorithms in large-scale applications.

<img width="928" height="758" alt="image" src="https://github.com/user-attachments/assets/2f29b912-155a-4cce-ab8c-9a5a19d73498" />


## Core Concepts

### Wavefront Expansion and Grid Labeling

- The algorithm begins by marking the source cell (e.g., '$S$') with zero or an initial integer.
- Each iteration labels all unmarked adjacent cells with incrementally higher integers, effectively expanding a wavefront outward from the source.
- Blockages within the grid are respected, meaning no labels are applied to obstructed or reserved cells.
- The process continues layer by layer, with each integer representing the minimal step distance from the source, until the wavefront reaches the target cell ('$T$').

<img width="914" height="770" alt="image" src="https://github.com/user-attachments/assets/69b470ff-60fb-436a-a0dc-1911f0a66a02" />


### Routing Path Selection

- Once the target is reached, pathfinding proceeds by tracing backward from the target to the source along descending integer values.
- Multiple valid shortest paths may emerge, differing primarily in their shape (e.g., L-shaped vs. more zigzag routes).
- Fewer bends in a route are preferred, as each bend typically increases signal delay and complexity in fabrication.
- The routing engine thus chooses the shortest path with the minimal number of bends.

<img width="918" height="767" alt="image" src="https://github.com/user-attachments/assets/25a2ad12-b6eb-4df7-9a34-72e6c54cb43d" />


### Handling Blockages

- Cells obstructed by design constraints (physical or logical blockages) are not labeled during wavefront expansion, forcing the algorithm to route around these regions.
- This adherence to blockages ensures that reserved spaces (such as areas allocated for pins or other components) are not violated during routing.

<img width="1109" height="793" alt="image" src="https://github.com/user-attachments/assets/e0c9c4d7-0970-46f6-a4a1-c7d58bbf22cb" />
<img width="998" height="790" alt="image" src="https://github.com/user-attachments/assets/03adade3-d8c4-41bf-a25c-e18db08bd517" />
<img width="933" height="792" alt="image" src="https://github.com/user-attachments/assets/e9151de7-1181-4bbf-b20f-de1eeb0dc065" />
<img width="1637" height="799" alt="image" src="https://github.com/user-attachments/assets/65195615-31b6-493c-abc1-da8e3874da53" />


### Algorithmic Properties

- **Breadth-First Search (BFS):** Lee's algorithm is a classical BFS variant, which expands uniformly across available paths and guarantees that the first route to the target is also the shortest.
- **Complexity:** The time and memory requirements for Lee's algorithm are significant for large grids, generally $O(m \times n)$ for a grid of size $m \times n$.
- **Optimality:** If a valid route exists, Lee's algorithm is guaranteed to find and mark the optimal (shortest) path.


### Limitations and Alternatives

- For routing millions of connections, Lee's algorithm can be too slow and memory-hungry due to exhaustive grid marking and storage.
- More advanced algorithms—such as Line Search or Steiner Tree methods—also seek the shortest path but typically offer better performance for large-scale routing tasks.
- These alternatives operate on similar shortest-path principles but employ heuristics or optimized data structures to reduce computational load.


## Key Points

- **Labeling Principle:** Adjacent cells to any labeled cell $i$ are marked with $i+1$, progressing outward until the destination is reached.
- **Shortest Path Guarantee:** Because BFS examines all possible routes in expanding circles, Lee's algorithm always delivers the shortest feasible route between source and target.
- **Blockage Handling:** Obstructed grid regions are respected throughout; routes circumvent these regions, ensuring adherence to physical design constraints.
- **Preference for Fewer Bends:** Among shortest paths, those with less zigzagging (i.e., fewer turns or bends) are chosen, optimizing for manufacturability and signal delay.
- **Resource Consumption:** The algorithm's step-by-step labeling and backtracing consume substantial memory and runtime, especially for large or dense grids.
- **Algorithmic Complexity:** For an $m$ by $n$ grid, typical time and space complexity is $O(m \times n)$.
- **Alternatives:** In extensive routing scenarios, more efficient algorithms such as Line Search or Steiner Tree methods are preferable due to their improved performance.
- **Physical Design Implications:** Routing algorithms like Lee's are essential tools for physical design engineers, directly supporting the overall synthesis and layout flow of integrated circuits.

---

# Design Rule Check

This documentation provides **theoretical foundations of Design Rule Check (DRC)** as applied to electronic circuit routing, with a focus on lithography-derived design constraints and common error scenarios. It covers the underlying principles guiding wire geometry in integrated circuits, the relationship between DRC and fabrication techniques, and essential signal integrity concepts.

## Core Concepts

### Design Rule Check (DRC)

Design Rule Check, or DRC, is a systematic process used to ensure that circuit layouts comply with a set of physical and electrical constraints before manufacturing. DRC evaluates parameters such as **wire width**, **wire spacing**, and **wire pitch** to guarantee reliable fabrication and operation.

- DRC rules are largely **driven by manufacturing capabilities**, especially by limitations imposed by optical photolithography.
- These checks are performed automatically by EDA tools, but must be understood and reasoned about at the theoretical and process level.
- Violations may result in errors like open circuits, short circuits, or features that cannot be reliably printed onto silicon.

<img width="1572" height="816" alt="image" src="https://github.com/user-attachments/assets/73fb936d-1c85-4408-ba00-4fa9802d149a" />
<img width="1570" height="774" alt="image" src="https://github.com/user-attachments/assets/5530efe1-0e57-40ab-a3d3-78b4c8222844" />
<img width="1590" height="796" alt="image" src="https://github.com/user-attachments/assets/013f8450-ce11-4ae1-af18-2281356ac880" />


### Lithography Constraints in IC Routing

The physical constraints for constructing viable wires on silicon are governed by the capability of the optical lithography process. Here, the **minimum printable feature size** is dictated by *the wavelength of light* used for patterning:

- **Minimum Wire Width ($w_{min}$):** Determined by the smallest feature that the lithography process can resolve. It's not possible to fabricate wires narrower than this threshold.
  
  $w \geq w_{min}$

- **Minimum Wire Pitch ($p_{min}$):** Defined as the minimum center-to-center distance between adjacent wires that can be reliably reproduced.
  
  $p \geq p_{min}$

- **Minimum Wire Spacing ($s_{min}$):** The least separation between the edges of neighboring wires that avoids pattern interference or merging.

  $s \geq s_{min}$

These minima are set experimentally, often by printing test patterns with varying dimensions and identifying the smallest reliably manufacturable geometries.


Furthermore, the actual wire cross-section after fabrication may deviate from ideal rectangular geometry due to process artifacts, underlining the need for conservative rule-setting.

### Routing on Multiple Metal Layers & Signal Shorts

In complex layouts, signals routed on the same metal layer risk **short-circuiting** if wires overlap or intersect inappropriately. This scenario is termed a **signal short** and represents a critical DRC violation.

- To resolve signal shorts, routes are separated onto **different metal layers** (e.g., from Metal 2 to Metal 3).
- Inter-layer connections are made using **vias**, which have their own width and spacing constraints influenced by the lithographic process.

Typically, higher-numbered (upper) metal layers are wider to accommodate increased current or for routing convenience, but all layers must adhere to their respective DRC rules.

#### DRC for Vias

- **Minimum Via Width ($v_{min}$):** The smallest printably reliable via dimension.
  
  $v \geq v_{min}$

- **Minimum Via Spacing ($vs_{min}$):** The least separation between adjacent vias to avoid electrical leakage or process-induced defects.

  $vs \geq vs_{min}$

Again, these are determined by process experiments where lithography is used to attempt fabrication of structures at various scales.

<img width="1542" height="787" alt="image" src="https://github.com/user-attachments/assets/3a387cf7-e7ce-4b06-a345-0b039849a72f" />
<img width="1533" height="784" alt="image" src="https://github.com/user-attachments/assets/fc5e797e-be63-4af0-a2b0-1c1b4dc2eb1d" />

## Key Points

- **DRC is essential** for ensuring that a physical design can be reliably fabricated and will function as intended. 
- **Key geometric constraints**—minimum wire width, pitch, and spacing—are set according to the *limits of the lithography process*.
- Violations such as **signal shorts** occur when proper separation and layer assignment are neglected, risking functionality failure.
- Use of **multiple metal layers** and **vias** mitigates some layout limitations but introduces new DRC criteria.
- **All constraints are empirically derived** from the practical capabilities of the lithographic method used to transfer patterns to silicon.
- **Each wire and via can be modeled electrically** by their resistance and capacitance, which are extracted after layout for use in signal integrity analysis and timing.

<img width="1658" height="833" alt="image" src="https://github.com/user-attachments/assets/9a2d9229-1331-442c-acec-a0a96a38c5b7" />

In summary, DRC serves as the **interface between design and manufacturability**, encoding the practical limitations of fabrication technologies into enforceable rules that safeguard yield, reliability, and performance.

---

# Lab steps to build power distribution network

This lab section guides you through **completing the physical design flow after CTS (Clock Tree Synthesis)**, focusing on resuming from the last completed stage, generating power/ground grids and straps, and understanding the necessary geometric constraints for standard cell placement. You will:
- Confirm the last executed flow stage using environment variables.
- Generate power and ground (VDD/GND) rails and straps using OpenROAD commands.
- Visualize and verify power distribution layouts.
- Understand the importance of matching standard cell heights to the grid pitch for effective power connectivity.

## Lab Steps

### 1. Start Docker and Initialize the OpenROAD Environment

Open your terminal and start the docker container set up for OpenROAD.

```bash
docker
./flow.tcl -interactive
```
<img width="815" height="311" alt="image" src="https://github.com/user-attachments/assets/ff5cb9b2-e742-4bb5-8703-41c1852de5b2" />


### 2. Prepare the Design Using Previous Configurations

Invoke the interactive flow environment and prepare the design for the next stage, using the same configuration as your last run; do not overwrite unless you intend to modify settings.

```bash
prep -design <your_design_folder> -tag <your_experiment_tag>
```

- If you want to specify new configuration values in `config.tcl`, use the `-overwrite` flag:

```bash
prep -design <your_design_folder> -tag <your_experiment_tag> -overwrite
```

### 3. Check the Current Flow Stage

Verify which stage was completed during the last run. This ensures you resume from the correct step.

```bash
echo $RUN_ENV
```
Example output:

```
CTS
```
<img width="655" height="45" alt="image" src="https://github.com/user-attachments/assets/f0491e2d-c4f3-4a38-b71e-23ec5a5c5066" />


In this example, the last completed flow was after Clock Tree Synthesis (CTS).

### 4. Generate Power and Ground Grids After CTS

To lay out the **power (VDD) and ground (GND) distribution networks**, use the following command after CTS:

```bash
gen_pdn
```

<img width="250" height="47" alt="image" src="https://github.com/user-attachments/assets/b303143b-2368-4f33-8ad8-a79ca4ee4137" />

This will:
- Read the current design state (post-CTS).
- Generate the grid and apply **straps** (wider metal lines that supply power extensively across the chip) and **rails** (lines running alongside standard cell rows).

### 5. Standard Cell Rows and Power Rails Geometry

The **standard cell rows** are spaced at a fixed pitch; in this flow, the relevant value is:

- **Pitch:** $2.72$ microns
- **Power/ground rails** are placed at this same pitch.

This means:
- Any custom standard cell (e.g., an inverter) must have a height that is a multiple of $2.72$ microns to make proper contact with the power and ground rails for consistent power delivery.

**Schematic:**

```
|---------------- 2.72 um ----------------|
+------------------------------------------+
| Standard Cell Row (for placement)        |
|                                          |
+------------------------------------------+
```
![PLACEHOLDER: screenshot/terminal-output/schematic]

If your cell height is not a multiple of $2.72$, its VDD/GND pins may not align with the grid, breaking the power connection.

### 6. Visualize Power Delivery from Pads to Standard Cell Rails

- Power (VDD/GND) comes from chip pads at the boundary.
- It is distributed through **straps** running across the chip.
- Straps are connected to **rails** at each standard cell row, supplying power to every cell.

**Schematic:**

```
Pad (VDD) -----> STRAPS ----------+
                                 |
                             +---+----------------+ 
                             |  Rails (2.72 um)   |
                             |  [Standard Cell]   |
                             +--------------------+
```
![PLACEHOLDER: screenshot/terminal-output/schematic]

### 7. Proceed to Next Physical Design Steps

After confirming and generating power/ground networks:
- Continue with placement, routing, and other sign-off steps per your flow script.

---

# Lab steps from power straps to std cell power

This documentation outlines the theoretical framework and core concepts behind the **Power Distribution Network (PDN) design in integrated circuits**, focusing on diagrammatic representations of the flow of power from external sources to internal circuit elements. The design features are reviewed in terms of their function within the IC: input/output pads, power and ground pads, rings, vertical straps, rails, and their interconnections. The explanation emphasizes how power is methodically routed through hierarchical structures to reach standard cells, providing insight into the organizational principles guiding PDN design in modern chip layouts.

<img width="936" height="629" alt="image" src="https://github.com/user-attachments/assets/e01986c8-c495-47a1-96ea-673ee7354267" />


## Core Concepts

### Pad Types and Functions

- **Input/Output Pads (IO pads):** 
  These pads are situated around the perimeter and serve as the interface points for signal inputs and outputs to the chip.

- **Power and Ground Pads:**
  Distinct pads (commonly colored red for power, blue for ground) introduce external electrical power and ground to the chip, creating the entry point for energy distribution.

- **Corner Pads:**
  Located at chip corners, corner pads contribute to structural connectivity and may be involved with special routing patterns for power or ground.


### Power Rings

- **Power Ring:**
  After entering through the power pads, electrical power is collected in a ring structure (outer red ring) circling the inner boundary of the chip. This ring acts as a primary distribution reservoir for power.

- **Ground Ring:**
  Similarly, ground connections aggregate in an inner (often blue) ring. This segregates power from ground, aiding noise reduction and robust current handling.


- **Connection Mechanism:**
  Cross-like structures visually depicted in the diagram denote the conductive paths connecting the pads to their corresponding rings.

### Straps

- **Vertical Straps:**  
  Significant metal traces (colored to match their associated rings) extend vertically from the power and ground rings, functioning as conduits that guide current from the rings deeper into the chip's interior.

- **Attachment:**
  Each strap is directly attached at its endpoints to the respective ring, ensuring robust current flow and consistent potential within the chip area.


### Rails and Power Delivery to Standard Cells

- **Power Rails:**
  Parallel lines running adjacent to standard cell rows, typically fabricated in lower metal layers (e.g., Metal 1 or Metal 2), serve as the final distribution element, feeding individual logic elements (standard cells).

- **Strap-to-Rail Connection:**
  Cross-connections (depicted as additional crosses) link vertical straps to horizontal rails, completing the pathway for current distribution from chip periphery to active circuitry.

- **Standard Cell Rows:**
  Block-like rectangles in the top view of the diagram represent standard cell rows, which require consistent and reliable power.

- **Macro Cells:**
  If macro cells are present (not in this scenario), specialized routing connects straps to these larger functional blocks.


### Hierarchical Power Flow

The power delivery sequence can be defined as:
1. Power enters through pads (red for power, blue for ground).
2. Pads connect to rings, aggregating the current.
3. Rings connect to vertical straps, channeling distribution into the chip.
4. Straps are linked to horizontal rails via cross-connections.
5. Rails deliver power to standard cell rows and, optionally, macros.

Mathematically, current distribution ($I$) through this network prioritizes minimized resistance and voltage drop:
$V = I \cdot R$
where the total resistance ($R$) of each segment—from pad to rail—determines the voltage available at the standard cells.


## Key Points

- **Power Distribution Network (PDN) Design:**  
  An optimal PDN minimizes IR drop and voltage variability across standard cells. The stepwise, hierarchical approach—from pad to ring, ring to strap, strap to rail, and finally rail to cell—ensures uniform supply and supports high current density.

- **Metals and Layers:**
  - Straps typically occupy higher metal layers (e.g., Metal 4, Metal 5) to facilitate large current flows and reduce resistance.
  - Rails reside in lower metal layers to interface directly with standard cells.

- **Connectivity State:**
  The connect state is the configuration where each element—pad, ring, strap, rail—becomes electrically joined, forming a complete PDN.

- **Floorplanning Sequence:**  
  Floorplan is set up, standard cell placement is completed, block resynthesis (if necessary) is performed, then PDN is generated. The final step is routing, which forms the physical interconnections for signal and power networks.

- **Design Artifacts:**
  The design file (e.g., '.def') updates at each stage, reflecting changes as PDN is implemented.

- **No Macro Cells:**
  In designs consisting solely of standard cells, macro connectivity is unnecessary, further simplifying the PDN.

- **Routing as Final Step:**
  Once power distribution is in place, signal routing establishes data pathways—this proceeds after confirming PDN integrity.

---

# Basics of global and detail routing and configure TritonRoute

This transcript provides a theoretical overview of the routing process in hardware design, focusing on the division of routing into two main phases: global routing and detailed routing. It explains the role of routing engines, the concept of routing guides, and the optimization strategies involved in connecting circuit elements efficiently. The discussion highlights the complexity of the routing solution space and the importance of structured approaches for effective hardware design.

<img width="1432" height="604" alt="image" src="https://github.com/user-attachments/assets/bb6f1c62-3264-430c-b61c-1d0a537586b7" />


## Core Concepts

### Routing Process Overview

Routing in hardware design is the process of establishing electrical connections between components on a circuit board or chip. Due to the vast solution space, routing is typically divided into two distinct phases: global routing and detailed routing.

- **Global Routing**: This phase divides the routing region into a grid of rectangular cells and represents the routing space as a coarse 3D graph. The goal is to create a high-level plan, or "routing guide," for each net (a group of pins that need to be connected). The global router outputs a set of routing guides that specify the general path each net should follow.

- **Detailed Routing**: This phase takes the routing guides from the global routing phase and determines the exact connectivity between pins. It ensures that the physical connections are realized according to the global routing solution, optimizing for factors such as wire length, congestion, and signal integrity.


### Routing Engines and Strategies

- **Routing Engines**: Different engines are used for global and detailed routing. For example, the "fast route" engine is typically used for global routing, while the "Triton route" engine is used for detailed routing.

- **Routing Strategies**: The choice of routing strategy affects the optimization mode and the performance of the routing process. Strategies are often numbered (e.g., 0 to 4), with each number corresponding to a different level of optimization. Lower numbers may prioritize runtime and memory efficiency over optimal routing quality, while higher numbers may produce better routing solutions at the cost of increased computational resources.


### Routing Guides

- **Routing Guides**: These are the outputs of the global routing phase. They specify the general path that each net should follow, represented as a series of grid cells or boxes. The detailed router uses these guides to find the best possible connectivity between pins.

- **Connectivity**: The detailed routing phase ensures that the physical connections are made according to the routing guides, optimizing for factors such as wire length, congestion, and signal integrity.


## Key Points

- The routing process is divided into global routing and detailed routing to manage the complexity of the solution space.
- Global routing creates a high-level plan (routing guides) for each net, while detailed routing determines the exact connectivity between pins.
- Different routing engines are used for global and detailed routing, with the choice of engine affecting the quality and efficiency of the routing process.
- Routing strategies are numbered and correspond to different levels of optimization, with lower numbers prioritizing runtime and memory efficiency and higher numbers prioritizing routing quality.
- Routing guides are the outputs of the global routing phase and are used by the detailed router to find the best possible connectivity between pins.
- The detailed routing phase ensures that the physical connections are realized according to the global routing solution, optimizing for factors such as wire length, congestion, and signal integrity.

---

# TritonRoute feature 1 - Honors pre-processed route guides

The detailed routing process in FPGA design builds upon prior global routing by refining connections to satisfy all layout constraints at a physical, segment-by-segment level. Tools like TritonRoute perform detailed routing, adhering closely to preprocessed route guides output by global routing engines. Advanced algorithms, including those based on Mixed Integer Linear Programming (MILP), are used to optimize this process with parallel strategies within layers and sequential strategies between layers.

<img width="1280" height="452" alt="image" src="https://github.com/user-attachments/assets/78773466-630b-46dd-a27c-0aff396491f1" />


## Core Concepts

### Global Routing vs. Detailed Routing

- **Global routing** outlines a coarse path for each net, segmenting the FPGA into larger routing channels without specifying exact wiring tracks. The result is a set of route guides meant to direct the subsequent detailed routing phase.
- **Detailed routing** translates route guides into specific wire assignments, accounting for fabrication constraints and resolving connection competitions by selecting precise tracks and implementing all necessary physical connections.

### Preprocessed Route Guides

- Preprocessed route guides are generated by the global router and serve as boundaries within which the detailed router must work.
- These guides often take the form of directionally preferred channels, such as vertical and horizontal paths associated with specific metal layers.
- The detailed router attempts to maximize routing within these guides to ensure optimal use of resources and minimal congestion.

<img width="1218" height="757" alt="image" src="https://github.com/user-attachments/assets/8d6628eb-3bcb-48c1-8025-d82cbacb8cce" />


### Preferred and Non-Preferred Directions

- Each metal layer typically has a preferred routing direction (e.g., vertical for one layer, horizontal for the next).
- If a route guide is not aligned with the preferred direction, it is subdivided into unit-width segments (splitting) and possibly merged with orthogonally adjacent guides (merging).
- Bridging occurs when adjacent guides in parallel directions across layers are connected with vias, resulting in improved layout efficiency and reduced capacitive coupling.


### Mixed Integer Linear Programming (MILP) Based Routing

- MILP is an algorithmic approach to optimize detailed routing with constraints formulated as linear equations, where some variables are restricted to integer values.
- MILP-based panel routing solves for the best connection pattern given the physical and logical constraints of the FPGA fabric.


### Intra-layer Parallelism and Inter-layer Sequencing

- Detailed routing within a single layer (intra-layer) can be performed in parallel, significantly improving computational efficiency.
- Routing between layers (inter-layer) proceeds sequentially: all routes on one layer are completed before placing vias and routing in the adjacent layer.
- Vias, which enable connections between layers, are only placed once the source and destination layer routings are determined.


### Guide Connectivity

- Two route guides are considered connected if:
  - They are on the same metal layer and share a common edge (touching segments).
  - They are on adjacent metal layers and have a non-zero area of vertical overlap, allowing for via insertion.

<img width="1172" height="572" alt="image" src="https://github.com/user-attachments/assets/990f217d-3718-4281-bc24-69f37c4d949d" />


## Key Points

- The detailed router is constrained by preprocessed route guides created during global routing. It must try to complete connections within these guides to ensure feasible and efficient layout.
- The preferred routing direction for each metal layer minimizes capacitance and crosstalk, alternating orientation between successive layers.
- When route guides deviate from preferred directions, a sequence of splitting, merging, and bridging ensures proper alignment and efficient utilization of routing resources.
- MILP-based algorithms enable global optimization of routing choices by representing the problem with mathematical constraints, facilitating solutions even in complex, resource-limited scenarios.
- The process leverages intra-layer parallel routing (within each layer simultaneously) and inter-layer sequential routing (one layer at a time), ensuring correct via placement and minimized signal interference.
- Connectivity between guides follows strict physical requirements: edge adjacency for same-layer connections and overlapping projections for cross-layer via access.

---

# TritonRoute Feature2 & 3 - Inter-guide connectivity and intra- & inter-layer routing

This section presents key theoretical concepts in routing as applied to VLSI physical design, specifically focusing on **layer overlaps**, **pin accessibility**, **panel routing schemes**, and the principles underpinning **guide-based connectivity**. It differentiates between intra-layer and inter-layer routing strategies and explains their sequential and parallel execution.

<img width="1192" height="688" alt="image" src="https://github.com/user-attachments/assets/43864945-a93c-425e-a0a8-11cbca98d004" />


## Core Concepts

### Layer Overlap and Via Placement

When two metal layers, such as Metal 1 and Metal 2, share a **nonzero vertically overlapped area**, it signifies a required electrical connection. The Electronic Design Automation (EDA) tool detects this overlap and determines the appropriate location for inserting a via. A via is a vertical interconnection that joins two metal layers and is only placed if this overlap exists, ensuring precise and efficient electrical continuity between layers.


### Pin Accessibility and Routing Guides

Each **pin**—the connection point of a standard cell—must be overlapped by a routing guide to ensure that it is accessible for routing. In physical layouts, routing guides are typically represented by colored boxes (e.g., purple for Metal 1 guides). The intersection of these guides and pins is deliberate: pins are strategically placed at the intersections of horizontal and vertical routing tracks, maximizing accessibility and simplifying router implementation. This design choice underpins robust connectivity and reduces complexity in later routing stages.

Key Principle: Pin placement at track intersections guarantees overlap with route guides, which is a fundamental requirement for reliable routing.


### Guide Connectivity Principle

**Internal guide connectivity** ensures that the routing guides on different layers (or within the same layer) are correctly overlapped at points where connections are to be made. This principle is critical for preprocess routing steps, where the layout must satisfy all guide connectivity before detailed routing occurs.


### Panel Routing: Intra-Layer Parallelism and Inter-Layer Sequentiality

Routing layers (e.g., Metal 1, Metal 2, Metal 3, etc.) are divided into **panels**, which are sections delineated by dashed lines across the layer. Routing in these panels is organized by two core strategies:

- **Intra-layer parallel panel routing**: Routing occurs simultaneously (in parallel) across multiple panels within the same layer. For example, even-indexed panels might be routed in parallel, followed by odd-indexed panels.
- **Inter-layer sequential panel routing**: Routing is performed in sequence from one layer to the next. Only after routing is completed in layer $M_n$ does routing begin in layer $M_{n+1}$. This sequential execution ensures hierarchical progression and avoids routing conflicts between layers.

Key Distinctions:
- Intra-layer routing uses parallel algorithms within a single metal layer, increasing efficiency.
- Inter-layer routing enforces strict ordering, starting routings at layer $M_{n+1}$ only when all panels in $M_n$ are routed.


## Key Points

- **Overlap between metal layers** (nonzero vertical overlap) is required for via insertion, which enables vertical electrical connectivity.
- **Pins must be overlapped by routing guides** to ensure guaranteed access for router tracks; this is generally achieved by pin placement at the intersection of routing tracks.
- **Routing panels** segment each layer, with intra-layer routing performed in parallel panels and inter-layer routing executed sequentially across metal layers.
- The **panel routing scheme** distinguishes between parallelism (within a single metal layer) and sequentiality (between metal layers), optimizing both performance and layout quality.
- **Preprocessing steps** in routing must satisfy guide connectivity principles to streamline subsequent, more detailed routing algorithms. 

---

# TritonRoute method to handle connectivity

TritonRoute is an open-source detailed routing framework for VLSI physical design that focuses on producing optimized, design rule clean (DRC-clean) routing solutions. Its goals are achieved through key constraints, advanced routing concepts like access points, and the use of mixed integer linear programming for optimal connectivity.

<img width="1429" height="770" alt="image" src="https://github.com/user-attachments/assets/bf5e8322-0410-499e-9708-d3dd8aa8e552" />

## Core Concepts

### Detailed Routing and Constraints

- **Detailed routing** is the stage where precise geometric paths for interconnections are defined within the boundaries established by previous routing steps.
- The **inputs** required for TritonRoute include:
  - The LEF file (process design data),
  - The DEF file (design instance data),
  - Preprocessed routing guides derived from global routing.

- **Primary constraints** in detailed routing:
  - All routing must remain within the designated routing guides.
  - Connectivity constraints require all nets and pins to be properly connected according to circuit intent.
  - **Design rule constraints** ensure that no physical layout violations occur, meaning all minimum width, space, and enclosure rules are respected.


### Access Points and Access Point Clusters

- An **access point** is a specific grid location on a routing layer where connections such as vias or pin accesses are possible.
- These are determined during preprocessing and are essential for enabling layer-to-layer or pin connectivity.
- **Access point clusters (APCs)** refer to collections of access points derived from a particular net, segment, or region, representing all feasible locations where connections can be established.


### Routing Tracks and Layers

- Each metal layer in the design has preferred routing directions and associated tracks:
  - For example, Metal 1 (M1) may have horizontal tracks as its primary routing direction, while Metal 2 (M2) may be vertical.
- **Routing tracks** are the regularly spaced grid lines upon which wires are typically routed.
- To establish connections between different layers (e.g., from M1 to M2 or M2 to M3), **vias** are inserted at valid access points—the intersections of tracks on adjacent layers.


### Connectivity Scenarios

There are several core scenarios that detailed routing must handle:
- Connecting two routing guides on different layers via one or more vias.
- Connecting pins and internal cells within the same or different layers.
- Choosing optimal access points, especially when multiple valid connect locations exist.

For example, the intersection of five tracks between two layers yields five possible access points for a via. When more tracks are present, such as an intersection between horizontal and vertical routing tracks of different layers, the number of access points increases (e.g., 15 intersection points).


### Optimization via Mixed Integer Linear Programming (MILP)

- The routing problem is mathematically formulated as a **Mixed Integer Linear Programming (MILP)** problem.
- The objective of MILP is to select which access points and which specific connections between access point clusters should be made in order to:
  - Fulfill all connectivity requirements,
  - Remain within design rule constraints,
  - Optimize metrics such as wirelength and via count.

- In formulaic terms, the MILP objective could be minimizing a cost function such as $C = \alpha \cdot \text{wirelength} + \beta \cdot \text{via\_count}$, subject to constraints representing connectivity and design rules.


## Key Points

- **Inputs and outputs**: The router accepts LEF/DEF files and preprocessed routing guides; it produces a DRC-clean, optimized routing solution.
- **Constraints**: Routing solutions must satisfy boundary, connectivity, and strict design rule constraints.
- **Access points**: These grid locations are the only permissible sites for connections like vias or pin accesses, and are grouped into access point clusters.
- **Multiple connection choices**: Situations may present several candidate access points for connection; the choice among them directly affects quality metrics like wirelength and via usage.
- **Optimization**: Routing seeks the optimal mapping between access points and clusters using MILP, ensuring all constraints are met while optimizing for design objectives.

---

# Routing topology algorithm and final files list post-route

This documentation outlines the theoretical foundation and algorithmic concepts behind detailed routing in open-source physical design automation tools, with a particular focus on the minimal spanning tree algorithm, routing guides, design rule checking (DRC), parasitic extraction, and the process for timing analysis post-routing. Each concept is described to emphasize the underlying principles without referencing implementation steps or explicit code usage.

<img width="1075" height="598" alt="image" src="https://github.com/user-attachments/assets/5d431f84-10d5-4383-b50b-713530e99a78" />

---

## Core Concepts

### Minimal Spanning Tree in Routing Algorithms

The routing algorithm connects individual components (APCs/IPC) by evaluating the *cost* associated with each possible connection. The task is to establish connections in such a way that the **total cost is minimized**, which is accomplished using a **minimum spanning tree (MST)** strategy. The cost function quantifies the preference for each possible routing edge; by selecting routes that minimize total cost, the algorithm ensures optimal interconnection with minimal resource consumption.

$C_{total} = \min \sum_{i} c_i$

Where $c_i$ is the cost for each connection in the tree.


### Routing Guides and Rectangle Representation

Routing guides act as *spatial constraints* for each net, indicating preferred regions for signal paths. Conceptually, they are described as rectangles associated with specific metal layers. The essential parameters for each guide are:

- **Lower left coordinates $(x_{ll}, y_{ll})$**
- **Upper right coordinates $(x_{ur}, y_{ur})$**

For each net, multiple routing guides can exist across different layers:

Guide rectangle: $Rect(x_{ll}, y_{ll}, x_{ur}, y_{ur})$

During detailed placement, routing decisions are restricted to the confines of these rectangles, ensuring *localized routing* and aiding in design rule compliance.


### Design Rule Checking (DRC) and Violations

DRC is an automated process that checks the routed design for compliance with technology-specific rules (such as minimum metal spacing). DRC violations are documented post-routing, categorized by type (e.g., spacing) and affected layer. The trade-off exists between the strictness of routing strategies and the number of violations:

- **Tighter strategy (e.g., routing strategy = 14): fewer/no violations, higher computation time**
- **Looser strategy (e.g., routing strategy = 0): more violations, lower computation time**

The final goal is to eliminate all DRC violations for a manufacturable design.


### Parasitic Extraction

After routing, the physical layout introduces parasitic effects (capacitances, resistances) that must be quantified for accurate timing and functional validation. *Parasitic extraction* computes these values based on the routed geometry. This data serves as input for post-route timing analysis.

Typical process:

1. Provide the layout and netlist (design connectivity).
2. Extract parasitics into a standardized specification.

The extracted parameters are annotated onto the routed design files for use in timing simulation.


### Netlist Evolution Across the PNR Flow

As the physical design flow progresses, various interventions modify the design's netlist:

- **Post-synthesis netlist:** Original connectivity post logic synthesis.
- **Post-clock tree synthesis (CTS):** Modified netlist reflecting inserted clock buffers.
- **Post-routing netlist:** Additional modifications (such as antenna diode insertion).
- Netlist evolution tracks logical-to-physical mapping and incremental design change.

Final timing and functional checks use the *last* netlist, which encompasses all modifications up to routing completion.


### Timing and Post-Route Analysis

The timing analysis phase checks whether the routed design meets all timing requirements (setup, hold, etc.), factoring in extracted parasitics. Steps include:

- **Creating updated design databases (DB) with new routing information**
- **Referencing the modified netlist**
- **Running timing checks using standard constraint files (e.g., SDC) and parasitic data**

Convergence in timing and design rule checks (i.e., zero violations, timing met) signifies completion of the physical design cycle.


---

## Key Points

- The **minimum spanning tree algorithm** is fundamental to finding optimal routing with minimal total cost in layout generation.
- **Routing guides** are defined by rectangular coordinates and layer assignment, controlling the legal routing space for each net.
- **Design rule checking (DRC)** identifies manufacturing constraint violations; optimal routing strategies balance violation avoidance and computational requirements.
- **Parasitic extraction** translates routed geometry into electrical parameters necessary for timing analysis.
- The **netlist** undergoes systematic updates through synthesis, clock tree insertion, and routing interventions.
- The physical design process iteratively focuses on *reducing congestion*, *meeting timing*, and *eliminating DRC violations*, culminating in a manufacturable, timing-compliant layout.
- Open-source hardware design practices mirror those of commercial tools, emphasizing continuous documentation and validation at each stage.
