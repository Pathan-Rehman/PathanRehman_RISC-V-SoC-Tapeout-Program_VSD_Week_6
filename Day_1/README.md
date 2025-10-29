# Introduction to QFN-48 Package, chip, pads, core, die and IPs

This document introduces foundational concepts and terminology in open-source hardware and chip design, focusing on understanding electronic boards, the internal structure of chips, and the critical role of foundries. It is intended as an entry-level guide for students and newcomers to the hardware design field.

## Core Concepts

### Electronic Boards and Block Diagrams

- **Electronic boards** are commonly encountered in industry and learning environments, including familiar boards like those used with Arduino or FPGAs.  
  <img width="682" height="560" alt="image" src="https://github.com/user-attachments/assets/051016ea-af0a-465c-a281-f4083aec710b" />


- A *block diagram* is the primary method for describing the functional organization of a board. At its center is the **processor**, surrounded by several key interfaces and components such as programming ports (e.g., JTAG), storage (e.g., QSPI flash, I2C EEPROM), power supply (VCC, GND), and analog/digital converters (ADC, DAC).  
  <img width="1288" height="727" alt="image" src="https://github.com/user-attachments/assets/1de9ce4e-811e-49a2-9e1e-b93e76b8083e" />


### Chip, Package, and Internal Structure

- What is often referred to as a "chip" is more precisely called a **package**. A package is the physical container (e.g., QFN, quad flat no-leads) that holds the silicon die. Packages vary by the number of pins (e.g., 48 or 256) and overall size.  
  <img width="1159" height="733" alt="image" src="https://github.com/user-attachments/assets/54a0d866-55b4-4b16-a154-2c1dbf2baa76" />

- The **chip (die)** is placed at the center of the package. Electrical connections between the package pins and the chip are usually made using **wire bonds**, which transmit signals between the external world and the internal circuitry.  
  
  <img width="764" height="720" alt="image" src="https://github.com/user-attachments/assets/53555311-0fe3-497d-bae1-3fa4af498d6d" />


- Upon examining the chip itself:
  - **Pads**: Serve as gateways or "doors" through which signals enter and exit the chip.
  - **Core**: The central region housing the fundamental digital logic (e.g., AND/OR gates, multiplexers).
  - The chip is fabricated on a **silicon wafer**, and the area manufactured is called the *die*.  
  <img width="1040" height="724" alt="image" src="https://github.com/user-attachments/assets/bdc40d16-3142-4f34-8e8d-1fc7cc798d6e" />


### System on Chip (SoC) Organization

- In modern chips (e.g., those based on RISC-V), the SoC design integrates multiple components:
  - Central processor(s)
  - **SRAM** (Static Random-Access Memory)
  - Communication modules (e.g., SPI controllers)
  - Clocking elements (e.g., PLL: Phase-Locked Loop)
  - Analog interfaces (ADC, DAC)
- These functional blogs are typically represented as separate areas or macros within the chip layout.  
  <img width="997" height="718" alt="image" src="https://github.com/user-attachments/assets/eebcdd8f-d6f5-46cb-aba1-2a5b987e9266" />


### Foundry and IP (Intellectual Property) Concepts

- A **foundry** refers to the specialized manufacturing facility where chips are produced. Foundries contain deposition, lithography, and other fabrication equipment.

- **Foundry IPs** (Intellectual Properties) are pre-designed and verified blogs (such as ADCs, DACs, memory modules) provided by the foundry for integration into custom chips. These blogs require significant engineering expertise to create and are shared as reusable intellectual property elements.

- The distinction between "macros" and "IPs":
  - **Macros**: Typically refer to standard digital logic blogs.
  - **IPs**: More complex, often mixed-signal or specialized circuits, requiring advanced design knowledge.

- Effective chip design requires continuous communication with the foundry, typically through standardized interface files provided by the foundry, enabling the translation of design intent into manufacturable hardware.  
  <img width="1018" height="729" alt="image" src="https://github.com/user-attachments/assets/f866f890-e17e-4052-884f-86ae805a0cdc" />


## Key Points

- Understanding **board layouts** and **block diagrams** is foundational before delving into chip-level design.
- The term **package** refers to the entire external housing of the chip, not just the silicon die.
- **Wire bonds** and **pads** are physical mechanisms for interfacing between the external board and internal chip logic.
- The **core** of the chip contains the main processing and logical functions; the periphery involves communication and I/O interfaces.
- **System on Chip (SoC)** architecture groups essential components (memory, analog blogs, communication modules) for integrated functionality.
- **Foundry and IP** are central to modern chip design, with foundries supplying both the physical manufacturing capability and foundational building blogs for design integration.
- Successful hardware design and fabrication rely on standardized communication between designers (engineers) and foundry operations, often mediated by shared interface files and design standards.  
- Mastery of these fundamentals opens up advanced work in hardware design, SoC integration, and semiconductor manufacturing.

---

#  Introduction to RISC-V

This introduction explains the foundational role of the **Instruction Set Architecture (ISA)**—focusing on RISC-V—in computer hardware and software design. ISA serves as the central language between software applications and hardware, outlining how instructions are formatted, interpreted, and executed within a processor. The flow from high-level application code to executable machine binaries involves several stages and interfaces, ultimately allowing programs to interact with physical silicon implementations.

<img width="1448" height="910" alt="image" src="https://github.com/user-attachments/assets/aaf06205-f60d-4c5c-8e8f-90a827a0040b" />


## Core Concepts

### Instruction Set Architecture (ISA)

- The **ISA** is the formal definition of how a computer's processor executes instructions and manages operations. It acts as an interface dictating how software communicates with hardware.
- In RISC-V and similar architectures, the ISA specifies available operations, data types, register sets, and memory addressing mechanisms.
- The ISA does not exist physically within computer memory; rather, it is a **conceptual specification** that hardware designs implement.


### Compilation Flow

- Applications (e.g., in C) must be translated so that processors can execute them. The flow is:
    - **Source Code** (C, etc.) $\rightarrow$ **Assembly Language** (RISC-V assembly) $\rightarrow$ **Machine Code** (binary instructions)
    - Machine code is composed of binary representations: strings of $0$ and $1$; sometimes expressed as hexadecimal for readability.
- The translation process ensures that the logic authored by the programmer becomes a stream of hardware-level cmds.log.


### RTL and Hardware Implementation

- The **ISA** forms the basis for hardware design.
    - Design engineers use **Hardware Description Languages (HDLs)** to implement the ISA specifications as **Register Transfer Level (RTL)** logic.
    - RTL abstracts the internal data flows and logic required to realize the operations defined by the ISA on silicon.
- The **RTL implementation** is then synthesized to produce the physical layout of the chip (using standard chip design flows like Place-and-Route).


## Key Points

- **ISA as a Language:** The ISA can be likened to the language that software uses to communicate intent to the hardware—defining the permissible set of instructions for computation and control.
- **Separation of Concerns:** The ISA allows hardware designers and software developers to operate independently, provided both conform to the ISA specification. This separation ensures software binary compatibility across different hardware implementations adhering to the same ISA.
- **Binary Execution:** The final code executed by hardware consists of sequences of binary digits (bits) that encode the instructions as specified by the ISA. This is the format directly understood by the chip's logic gates and transistors.
    - Example: If a program swaps two numbers, that logic is ultimately converted to a specific set of binary instructions implemented and executed by the chip.


- **ISA vs. Microarchitecture:**
    - **ISA**: High-level, abstract specification (“what” the hardware should do).
    - **Microarchitecture/RTL**: Actual realization (“how” those operations are implemented) in silicon.

- **Hardware Description Language (HDL):** Used to express RTL implementations that embody the ISA specifications in physical logic.


- **Design Flow Overview:**
    1. **Start with ISA specifications**
    2. **Implement with HDL/RTL**
    3. **Synthesize RTL into chip layout (physical design)**
    4. **Applications compile to binary executable for that ISA**
    5. **Chip executes machine instructions, producing output for the user**


- **User Perspective:** For most users, executing a program appears seamless. The underlying complexity—translating software logic through layers of abstraction down to transistor-level operations—remains hidden.

---
# From Software Applications to Hardware

This documentation focuses on the theoretical aspects of how application software interacts with hardware through the system software layer. It explains the flow from application software to being converted into binary instructions that the hardware can understand. Key components include the operating system, compiler, and assembler, each playing a crucial role in this process.

## Core Concepts
### System Software Layers
- **Operating System (OS):** Serves as the interface between application software and hardware. It handles memory management, I/O operations, and ensures the software runs smoothly by allocating necessary resources. The OS converts application programs into assembly language and then binary code for the hardware.
- **Compiler:** Translates source code (e.g., C or Java) into instructions based on the hardware architecture (e.g., Intel X86, ARM, MIPS).
- **Assembler:** Converts assembly language into machine code (binary digits) that the hardware can execute directly.

<img width="1468" height="911" alt="image" src="https://github.com/user-attachments/assets/02e68f0d-1f07-410f-9fe6-53d4d4bb3e09" />


### Instruction Set Architecture (ISA)
- **Definition:** Acts as an abstract interface between the programming language and hardware, defining how instructions are executed.
- **Function:** Provides a set of instructions that the hardware understands, allowing application software to communicate with the hardware. Examples include X86, ARM, and MIPS ISA.


### Hardware Description Language (HDL)
- **Definition:** Used to describe digital circuits at a high level, before they are synthesized into gates and flip-flops.
- **Function:** Essential for designing hardware that can interpret and execute the binary instructions derived from the ISA.

<img width="1452" height="900" alt="image" src="https://github.com/user-attachments/assets/cff7d367-d3c9-4f6f-bec1-9ddee3559456" />

## Key Points
- **System Flow:** Application software enters the system software, which converts it into assembly and then binary code. This binary code is executed by the hardware.
- **Hardware vs. Software Interaction:** The compiler ensures that the output instructions are compatible with the specific hardware architecture.
- **RISC vs. CISC Architectures:** RISC (Reduced Instruction Set Computing) focuses on simpler instructions executed quickly, while CISC (Complex Instruction Set Computing) involves more complex instructions.
- **Synthesis and Implementation:** HDL specifications are synthesized into gate-level netlists, which are then physically implemented on the chip.

<img width="1518" height="903" alt="image" src="https://github.com/user-attachments/assets/dbdf32fd-c1c6-43a9-80d7-f0998d8dd7d7" />

---

# Introduction To All Components Of Open-Source Digital Asic Design

Open Lane is an open-source, Python-based platform designed to automate the flow of digital Application-Specific Integrated Circuit (ASIC) implementation, supporting customizable workflows and integration of various open-source Electronic Design Automation (EDA) tools. The platform facilitates the entire ASIC design process, from high-level hardware descriptions down to manufacturing-ready physical layouts, and is a major enabler for open-source silicon development. The recent release of open-source Process Design Kits (PDKs), such as the SkyWater 130nm PDK, completes the open-source toolchain by providing essential fabrication technology data to the community.


## Core Concepts

### ASIC Design Flow

- **ASIC (Application-Specific Integrated Circuit) design** is a methodical process transforming functional hardware descriptions into manufacturable chip layouts.
- The flow starts from RTL (Register-Transfer Level) models, written in hardware description languages (HDLs) like Verilog or SystemVerilog, and progresses through stages like synthesis, floorplanning, placement, routing, and verification.
- Multiple EDA tools are orchestrated to automate these steps, resulting in a final GDSII file suitable for fabrication.
- Open Lane uses integrated open-source tools such as Yosys (for synthesis), OpenROAD (for physical design), Magic (for layout), and Klayout (for verification).

<img width="700" height="805" alt="image" src="https://github.com/user-attachments/assets/d4b16af1-03cb-42d2-a021-2e3232e28519" />


### Process Design Kit (PDK)

- A **PDK (Process Design Kit)** is a comprehensive bundle of device models, design rules, library files, and technology-specific data that enables designers to create circuits compatible with a specific fabrication process.
- Historically, PDKs were proprietary, limiting access and participation in silicon fabrication to select organizations.
- The recent release of open-source PDKs—for example, the SkyWater 130nm PDK—now allows broader access, so all three foundational elements of ASIC design (RTL, EDA tools, PDK) can be open-source.
- The PDK encapsulates models such as transistor characteristics and standard cell libraries, essential for accurate simulation and layout.

<img width="951" height="622" alt="image" src="https://github.com/user-attachments/assets/202550b3-9642-48ae-b10b-cb4aa5db115f" />


### Technology Nodes and Market Relevance

- **Technology node** refers to the minimum feature size of transistors in an integrated circuit, commonly expressed in nanometers (e.g., 130nm, 5nm).
- While 130nm node is no longer cutting-edge, it remains commercially viable, representing a significant market share and supporting applications that do not require the extreme performance offered by the latest nodes.
- Lower node sizes ($5\,\text{nm}$, $7\,\text{nm}$) allow for higher density and performance but are costly; 130nm offers a balance between performance and affordability.

<img width="802" height="534" alt="image" src="https://github.com/user-attachments/assets/859fdc31-4601-4409-b075-af60966510e0" />


## Key Points

- **Open Lane platform** provides an automated, customizable infrastructure for converting RTL designs into manufacturable layouts, lowering the barrier to ASIC development.

- The **SkyWater 130nm PDK** enables true open-source silicon fabrication, giving designers essential process data for successful chip design and tape-out.

- ASIC design involves these key elements:
  - Hardware description: typically at RTL, via HDLs like Verilog/SystemVerilog.
  - EDA tools: automate synthesis, placement, routing, verification, and more.
  - PDK: supplies foundry-specific fabrication data, such as device models and design rules.

- ASIC design methodology is encapsulated in a "flow," a sequence of steps that implements the design from RTL to the final physical layout (GDSII format).
  - The flow manages complex inter-tool orchestration and applies hierarchical design protocols.

- Equations such as gate delay, power, and area are essential throughout design stages. For example:
  - Gate delay: $t_{pd} = k \cdot L^2 / (V_{dd} - V_{th})$
  - Dynamic power: $P = \alpha C V_{dd}^2 f$
  - Where $L$ is channel length, $V_{dd}$ is supply voltage, $V_{th}$ is threshold voltage, $C$ is capacitance, $f$ is frequency.


- Open Lane and similar open-source flows accelerate innovation, reduce cost, and democratize access to advanced silicon design.

- 130nm technology is proven in high-performance chips (e.g., Pentium 4 running at 3.5GHz) and remains relevant for many practical applications.

- With all three elements—open-source RTL, EDA, and PDK—now available, the creation of 100% open-source ASICs is feasible for a broad range of designers and organizations.

---
# Simplified RTL2GDS flow

This document outlines the theoretical foundation of the **ASIC (Application-Specific Integrated Circuit) design flow**, detailing each stage from the initial RTL model to final mask layouts in GDSII format. It covers conceptual steps such as synthesis, floorplanning, placement, clock tree synthesis, routing, and verification, emphasizing how each phase contributes to the manufacturable silicon chip.

<img width="1028" height="603" alt="image" src="https://github.com/user-attachments/assets/f6e36907-8186-45a3-b7ae-dde44dfac189" />


## Core Concepts

### ASIC Design Flow Overview

**ASICs** are custom-designed integrated circuits tailored for specific applications, offering benefits like high performance, power efficiency, and reduced size. The design process is a sequence of well-defined, interdependent stages ensuring the circuit meets its intended function and manufacturability.

### Register Transfer Level (RTL) and Gate-Level Netlist

- **RTL Model**: The chip's functionality is described in a hardware description language (HDL) such as Verilog or VHDL, representing the data flows and operations at the register level.

- **Synthesis**: The RTL model is transformed into a gate-level netlist—a structural description of the design using components (standard cells) from a technology library. This netlist is functionally equivalent to the RTL specification.

- **Standard Cells**: Each standard cell has a regular (fixed-height, variable-width) physical layout and is described by multiple views used by electronic design automation (EDA) tools, such as:
  - **Liberty view**: Contains timing and power information.
  - **HDL behavioral view**: For simulation.
  - **SPICE/CDL view**: For circuit-level simulation.
  - **Physical layout views**: Include abstract and detailed layouts.

<img width="1013" height="821" alt="image" src="https://github.com/user-attachments/assets/b67b1047-e416-4528-a6a0-cbd78adc2ee4" />
<img width="1006" height="410" alt="image" src="https://github.com/user-attachments/assets/7344e541-8def-4c05-92da-f90313f1e5cd" />


### Floorplanning and Power Planning

- **Floorplanning**: Defines the spatial organization of the chip or macros, partitioning the die into areas for different components and establishing the foundation for layout. Macro dimensions and locations are set, and placement rows/routing tracks are defined.

- **Power Planning**: Includes designing the power distribution network (PDN) to deliver power to all chip components. This network uses multiple VDD and ground pins, rings, and metal straps—primarily on upper (thicker, lower resistance) metal layers—to minimize resistance and voltage drop (IR drop) and mitigate electromigration.

<img width="1031" height="776" alt="image" src="https://github.com/user-attachments/assets/2d80e4d4-16c7-4e94-9ee8-27037831c19c" />
<img width="984" height="367" alt="image" src="https://github.com/user-attachments/assets/01aaafc6-d66b-4a97-bf9d-3adf1bdcc761" />
<img width="990" height="352" alt="image" src="https://github.com/user-attachments/assets/18e9e78c-6eb0-4e3c-8f8a-e24a9b6d0afe" />


### Placement

- **Placement**: Involves assigning physical locations to each cell in the design. Connected cells are placed close together to minimize signal delay and facilitate routing.
  - **Global Placement**: Finds approximate locations, potentially allowing overlaps.
  - **Detailed Placement**: Adjusts cell locations to ensure legal, non-overlapping placement.

<img width="1015" height="799" alt="image" src="https://github.com/user-attachments/assets/41e294c0-419e-426b-8feb-faf65d11fa18" />
<img width="997" height="370" alt="image" src="https://github.com/user-attachments/assets/4a62f64f-303f-479d-8c20-711a4d8af1d6" />


### Clock Tree Synthesis

- **Clock Tree Synthesis (CTS)**: Builds the clock distribution network to deliver the clock signal with minimal skew (timing difference between clock arrival at various points) and low latency. The network is tree-shaped, with various structures (e.g., H-tree, X-tree, or hybrids) used to meet timing requirements.

- **Skew Minimization**: Minimizing clock skew is critical for correct sequential logic operation, ensuring synchronous components receive the clock with minimal timing differences.

$skew = t_{clk, max} - t_{clk, min}$

<img width="997" height="788" alt="image" src="https://github.com/user-attachments/assets/c6c58bcb-56fa-4280-b2c6-b3b6b9c35be1" />


### Signal Routing

- **Routing**: Establishes the actual metallic connections between placed cells using the available set of metal layers (as specified by the process design kit, or PDK).
  - **Global Routing**: Generates high-level routing guides using coarse grids.
  - **Detail Routing**: Implements specific wire paths and via connections, following design rules.

- **Metal Layers**: Each has defined thickness, pitch (track spacing), and minimum width.

- **Routing Challenges**: Finding valid wiring patterns within layer/track constraints and minimizing wirelength, congestion, and parasitics.

<img width="1019" height="828" alt="image" src="https://github.com/user-attachments/assets/a99bd90b-9c94-4d66-904f-59423bd93d26" />


### Layout and Verification

- **Final Layout**: The complete geometrical description of all circuit features, generally captured in GDSII format, ready for mask generation.

- **Design Rule Checking (DRC)**: Ensures layout complies with manufacturing constraints (such as minimum width and spacing).

- **Layout Versus Schematic (LVS)**: Verifies that the physical layout matches the intended schematic or netlist.

- **Static Timing Analysis (STA)**: Performs timing verification to ensure all paths meet required timing, confirming the circuit will operate correctly at the target frequency.

$T_{clk} \geq \max(\text{setup time}, \text{combinational delay}, \text{hold time})$

<img width="1033" height="763" alt="image" src="https://github.com/user-attachments/assets/971abcc2-dc47-4421-b285-44469d64d2d5" />


### Tape-Out

- **Tape-Out**: The conclusion of the implementation flow, producing the final mask set layouts for fabrication. "Tape-out" historically referred to physically sending magnetic tapes with the design data to the foundry.

## Key Points

- The ASIC design flow is a sequential, interdependent process including specification, RTL modeling, functional verification, synthesis, floorplanning, placement, clock tree synthesis, routing, layout verification, and tape-out.

- Standard cells are modular building blogs with predefined electrical and physical characteristics, simplifying synthesis and placement.

- Power and clock distribution networks are crucial for circuit reliability, necessitating careful planning to reduce IR drop, electromigration, and timing mismatches.

- Verification occurs at multiple stages to guarantee functionality (functional and layout-vs-schematic checks), manufacturability (design rule checks), and timing compliance (static timing analysis).

- The entire design is captured in GDSII format for mask generation and manufacturing. Successful ASICs require close attention to detail at each stage to meet the design’s functional, physical, and performance goals.

---

# Introduction to OpenLANE and Strive chipsets

Open-source ASIC development leverages publicly available Process Design Kits (PDKs) and design automation flows to create custom chips. The reference methodology described is called **Open Lane**, which is an open-source toolchain for ASIC implementation designed for high automation, minimal human intervention, and broad compatibility with open silicon platforms. It targets the SkyWater 130nm process and supports multiple chip architectures, memory configurations, and modes of operation. The overall objective is to enable fully automated and verifiable chip design from RTL to final layout, with emphasis on design space exploration and transparent licensing.

## Core Concepts

### ASIC Design Flow

The ASIC (Application-Specific Integrated Circuit) design flow is a systematic process that transforms a functional concept into a physical chip. It generally proceeds through these stages:

- **Specification and Architecture**: Define the chip’s purpose, performance targets, and architectural blogs, including memory, control, and datapaths.
- **RTL Design**: Functional description is written in a hardware description language (HDL) at the Register Transfer Level (RTL), outlining how signal flows between registers and functional blogs.
- **RTL Verification**: Simulation tools confirm that the RTL code performs as intended. Verification includes functional correctness and testbench-driven coverage analysis.
- **Synthesis**: Convert RTL to a gate-level representation, mapping functional code to standard cell libraries corresponding to the chosen fabrication process.
- **Gate-Level Verification**: Ensure synthesized netlist matches the original specifications and functions under intended timing constraints.
- **Place and Route (P&R)**: Physically arrange gates and route interconnections, perform clock tree and power grid synthesis.
- **Physical Verification**: Validate that the layout follows design rules (DRC), matches intended circuit topology (LVS), and is free from manufacturing violations.
- **Timing Analysis**: Confirm the design is free of timing violations, i.e., meets setup and hold time constraints for all paths: 
- **Tapeout**: Produce the final layout file (GDSII) and send to manufacturing.

### Open Source ASIC Methodology

**Open Lane** provides an open-source reference implementation for the ASIC flow. Key aspects include:

- Integration with open-source PDKs such as SkyWater 130nm.
- Containerized deployment for out-of-the-box operation.
- Support for both autonomous (push-button) and interactive design flows.
- Enables design and hardening of macros as well as whole chips.
- Provides extensive design space exploration tools for discovering optimal configuration settings automatically.

<img width="1027" height="609" alt="image" src="https://github.com/user-attachments/assets/547ed847-1f95-4829-93fa-d79980b1021c" />
<img width="1414" height="736" alt="image" src="https://github.com/user-attachments/assets/768c53b4-3330-4369-a312-cf4d2255a635" />


### Design Space Exploration

Design space exploration is a methodology for optimizing flow configuration parameters to achieve best results for timing, area, power, or other design goals. It involves:

- Sweeping a range of configuration values.
- Evaluating each candidate using metrics like timing slack and violation absence.
- Searching for the set of parameters $(P_1, P_2, ..., P_n)$ maximizing design quality under constraints:
  - $\max \{Q(P_1, P_2, ..., P_n) | V_{DRC} = 0, V_{LVS} = 0\}$
    - Where $Q$ is a quality metric (timing, area, etc.)
    - $V_{DRC}$ and $V_{LVS}$ denote number of DRC and LVS violations.

<img width="1398" height="724" alt="image" src="https://github.com/user-attachments/assets/349ed2cd-7c87-45be-88b8-252ed5ac980e" />


## Key Points

- **Automation**: Open Lane seeks full automation from RTL to GDSII with minimal manual intervention, aiming for ‘clean’ tapeout-ready designs.
- **Verification**: The flow continuously checks for violations:
  - No Layout Versus Schematic (LVS) violations.
  - No Design Rule Check (DRC) violations.
  - No timing violations, although timing closure remains a work in progress.
- **Open Ecosystem**: All components are open, including PDKs, EDA tools, RTL libraries, and design examples.
- **Reference Implementations**: The Thrive family showcases different memory configurations, hierarchy strategies, and library choices:
  - Multiple SRAM block sizes and memory banks.
  - Varied hierarchical organization for experimentation.
  - Design-for-test structures for manufacturability.
- **Modes of Operation**:
  - *Autonomous*: Fully automated, push-button flow yielding final layout and reports.
  - *Interactive*: Step-wise execution for research, debugging, or custom flow tuning.
- **Community Contributions**: The flow includes over 40 publicly available designs with best-known configuration sets, regularly updated and expanded by contributors.

---
## Introduction to OpenLANE detailed ASIC design flow

The OpenLane ASIC flow enables the automatic transformation of digital designs from RTL descriptions to final physical layout formats suitable for fabrication, such as GDSII. This process utilizes open-source tooling and project collaboration to ensure accessibility and customization for a range of hardware projects. The flow incorporates specialized utilities for design and synthesis exploration, allowing for optimization across multiple metrics and design goals.

<img width="1009" height="632" alt="image" src="https://github.com/user-attachments/assets/43a688a4-1912-4d93-8d89-fc606ee449d8" />


## Core Concepts

### End-to-End OpenLane Flow
- **OpenLane** is constructed from several open-source projects and tools, orchestrating a complete ASIC implementation flow starting from RTL (Register Transfer Level) design and ending with a finalized GDSII layout file suitable for manufacturing.
- The process relies on a Process Design Kit (PDK) that provides fabrication-specific technology data and libraries.

<img width="908" height="558" alt="image" src="https://github.com/user-attachments/assets/0a73bcbd-4a82-4e06-b263-96f96ecbddd8" />

### RTL Synthesis and Optimization
- The flow begins with **RTL synthesis**, where high-level hardware descriptions are converted into gate-level logic circuits composed of generic components.
- This synthesized logic is mapped onto a set of technology-specific cells from a standard cell library.
- The optimization stage utilizes different strategies (guided by ABC scripts), targeting objectives such as minimizing area or delay.
- Synthesis exploration utilities allow analysis of how various strategies influence design metrics like delay ($t_{delay}$) and area ($A_{design}$).

$A_{design},\ t_{delay},\ \text{metrics} \rightarrow \text{synthesis strategies}$

<img width="953" height="495" alt="image" src="https://github.com/user-attachments/assets/24253c16-0023-481f-88aa-2a2a1a3ce5bc" />


### Design and Synthesis Exploration
- **Design exploration utilities** enable sweeping through more than 16 possible configuration parameters, generating comprehensive metric reports (over 35 distinct metrics) and identifying violation counts for each configuration.
- These reports are essential for selecting optimal configurations and are also used in regression testing to support continuous integration for large design sets.

<img width="925" height="483" alt="image" src="https://github.com/user-attachments/assets/642a5774-e84b-49bf-a404-dbee2e15d4eb" />


### Testing Structure Insertion
- To make designs testable after fabrication, optional steps can be enabled that insert scan chains, test pattern generation, fault coverage analysis, and, optionally, JTAG controllers for standardized testing access.
- The scan chain forms a path through which each flip-flop can be accessed and controlled for post-silicon verification.

<img width="1427" height="725" alt="image" src="https://github.com/user-attachments/assets/3d7d734d-6584-410d-92fe-0718dfb75060" />


### Physical Implementation
- This phase involves **floorplanning**, macro and cell placement, power network creation, clock tree synthesis, placement optimization, and both global and detailed routing.
- Tools automate layout tasks: arranging cells, connecting signals, synthesizing clogs, and resolving placement for optimal timing and area.

<img width="1411" height="671" alt="image" src="https://github.com/user-attachments/assets/41ccabc3-512c-4b71-a850-ce2327550aff" />


### Logic Equivalence Checking
- After optimizations and transformations during physical realization, **logic equivalence checking** ensures the netlist produced by physical design matches the original synthesized logic in functionality.
- This verification is crucial to avoid unintended changes introduced during physical optimization steps.

<img width="1393" height="692" alt="image" src="https://github.com/user-attachments/assets/09476d2d-ed6d-4672-8edc-9cf25d542ee8" />


### Antenna Effect Handling
- Long metal wire segments may act as antennas during fabrication, collecting charge that risks gate damage. There are two mitigation strategies:
  - **Bridging technique**: tmporarily connects the wire to higher metal layers.
  - **Insertion of antenna diodes**: allows charge to dissipate, protecting transistor gates.
- OpenLane includes a preventive approach by inserting a “fake” antenna diode cell during initial placement, replaced by a real diode if violations are discovered in later checks.

$Q_{antenna} > Q_{safe} \rightarrow \text{insert diode}$

<img width="936" height="655" alt="image" src="https://github.com/user-attachments/assets/1ed09579-abde-4522-82a6-46059efdf7c5" />
<img width="1420" height="629" alt="image" src="https://github.com/user-attachments/assets/bc485e68-133f-4f26-a23c-15fa1fd4b854" />


### Signoff Analysis
- **Static timing analysis (STA)** verifies the design meets timing constraints, highlighting violations if paths exceed allowable delays.
- **Design Rule Checking (DRC)** and **Layout vs. Schematic (LVS)** validation ensure the physical layout adheres to fabrication and logical requirements.
- Extraction of interconnect resistance and capacitance ($R_{int}$, $C_{int}$) is performed for accurate timing verification.

$T_{path} \leq T_{max};\ \text{DRC pass};\ \text{LVS pass}$

<img width="785" height="655" alt="image" src="https://github.com/user-attachments/assets/41b5610b-30bd-4fbf-9c7d-0eb03527ae8b" />
<img width="858" height="626" alt="image" src="https://github.com/user-attachments/assets/97530d0e-cfa1-4ddc-a5d9-b70ee9696267" />

## Key Points

- **OpenLane automates the full ASIC flow** from RTL input to final GDSII output, leveraging open-source EDA tools.
- **Synthesis and design exploration utilities** facilitate selection of optimal configuration parameters, improving area, timing, and minimizing violations.
- **Testing structure insertion** enhances post-fabrication testability, implementing scan chains and optional JTAG.
- **Physical implementation** sequences encompass floorplan, placement, CTS, routing, and specialized optimization steps.
- **Logic equivalence checking** is critical to guarantee functional preservation after physical transformations.
- **Antenna effects** are handled through bridging or diode insertion; OpenLane includes automated approaches to insert and replace diodes as needed.
- **Signoff analysis** is performed with open-source tools, including static timing analysis, DRC, and LVS, ensuring readiness for manufacturing.

---

# OpenLANE Directory structure in detail

## Lab Introduction

This workshop guides you through the setup and exploration of the **OpenLane flow**, an open-source toolchain for digital hardware design. The aim is to automate the process from RTL description all the way to GDSII layout using open-source tools and the SkyWater 130nm PDK. Key steps include navigating directories, exploring PDK contents, and invoking the OpenLane workflow.

## Lab Steps

### 1. Start in Home Directory

You will begin in your **Linux terminal** within your home directory.

<img width="733" height="490" alt="image" src="https://github.com/user-attachments/assets/b6303f77-8551-4ce5-aaac-cfc497d1335c" />


### 2. Change to Work Tools Directory

Use the `cd` command to navigate to the `work/tools` directory.

```bash
cd work/tools
```
<img width="734" height="491" alt="image" src="https://github.com/user-attachments/assets/af4f1a21-628f-465f-aa10-275599df5fda" />


### 3. List Contents Chronologically

List all files and directories inside your current folder using `ls` with the `-ltr` switch (lists files in chronological order).

```bash
ls -ltr
```
<img width="732" height="485" alt="image" src="https://github.com/user-attachments/assets/131ceedf-d9b2-4472-90f7-02aab1921afc" />


### 4. Get Command Help

To see a command’s available options and their descriptions, append `--help` to any command. For example, to view help for `ls`:

```bash
ls --help
```
<img width="738" height="814" alt="image" src="https://github.com/user-attachments/assets/16cc8917-d262-4a32-b0d1-8e1cde752c3a" />


### 5. Clear Terminal Output

If your terminal becomes cluttered, use:

```bash
clear
```

### 6. Explore Directory Structure

Navigate into the **openlane_working_dir** directory to access the OpenLane flow and PDK folders:

```bash
cd openlane_working_dir
```
<img width="575" height="24" alt="image" src="https://github.com/user-attachments/assets/188a655a-6e0c-4f5b-a200-307b16c24f2b" />


Now list the folders:

```bash
ls
```
<img width="637" height="152" alt="image" src="https://github.com/user-attachments/assets/4ae1c5f1-067e-4ca7-a148-ebb6f869482f" />

You will see `openlane` and `pdks` directories.

### 7. Examine the PDKs Folder

Change into the `pdks` directory:

```bash
cd pdks
```
<img width="664" height="46" alt="image" src="https://github.com/user-attachments/assets/abf68d91-5a1c-49a6-bb4a-89c91345435e" />


List its contents:

```bash
ls
```
<img width="744" height="179" alt="image" src="https://github.com/user-attachments/assets/7373efe7-5632-4a08-9dc2-e77f8f106388" />


You should see the folders: `skywater-pdk`, `open_pdks`, and `sky130A`.

### 8. Explore SkyWater 130nm PDK

Change to the `sky130A` directory (the SkyWater 130nm variant made compatible with OpenLane):

```bash
cd sky130A
```
<img width="782" height="196" alt="image" src="https://github.com/user-attachments/assets/554bb059-2509-4c83-9bdd-a9c562cc365a" />


Now list its contents:

```bash
ls
```
<img width="779" height="227" alt="image" src="https://github.com/user-attachments/assets/f1030bc3-e318-4572-b367-f963632a9302" />


You should see `libs.ref` and `libs.tech` directories.

- **libs.ref** contains process-specific files (timing, liberty, cell info).
- **libs.tech** contains tool-specific files (layout engines, netgen, Magic overlays).

### 9. Inspect libs.ref

Go into `libs.ref` directory:

```bash
cd libs.ref
```
<img width="821" height="246" alt="image" src="https://github.com/user-attachments/assets/967af367-1dc3-4574-9818-9ac9bd4f8d0a" />


List the folders with `-ltr` for more information:

```bash
ls -ltr
```
<img width="853" height="508" alt="image" src="https://github.com/user-attachments/assets/d36e861e-c8d9-466a-8e1c-b07905303ce7" />


Explore files such as timing libraries, `.lef` (cell/floorplan info), `.techlef` (technology definitions).

#### Example: Timing Libraries

You may see files named as process-voltage-tmperature (PVT) corners, such as:

- `slow.lib`
- `fast.lib`

These represent timing information for cells at slow and fast silicon process corners, voltages, and tmperatures.

#### Example: Technology LEF

- `.techlef` files contain layer information.

#### Example: Cell LEF

- `.lef` files describe cell layouts.

### 10. Inspect libs.tech

Go back and enter `libs.tech`:

```bash
cd ..
cd libs.tech
```

List the contents:

```bash
ls
```
<img width="867" height="773" alt="image" src="https://github.com/user-attachments/assets/4774867a-269a-4ae3-afcc-05015d8dfc5b" />

Here you find overlays and tool compatibility files for engines like Magic, KLayout, and Netgen.

### 11. Return to OpenLane Working Directory

Go up four levels to reach OpenLane’s working directory:

```bash
cd ../../../..
cd openlane
```

List contents to confirm location:

```bash
ls
```

Always ensure to run OpenLane from this directory.

### 12. Invoking OpenLane

To start the OpenLane tool, you will proceed from the `openlane` working directory.
---
# Design Preparation Step

This lab guides you through performing an **interactive OpenLane flow** on a sample RISC-V core design. Key steps include launching the interactive session, inspecting and setting up the design folder, configuring design parameters, preparing the file system, and merging library files required for synthesis.

## Lab Steps

### 1. Start OpenLane Interactive Session

Begin by running OpenLane in interactive mode. This allows you to execute each step of the flow manually, observe changes, and compare results.

```bash
docker
./flow.tcl -interactive
```
<img width="767" height="322" alt="image" src="https://github.com/user-attachments/assets/fcec656b-0b73-441e-a2d8-a31d170f2c46" />


You will see the prompt change to a percentage symbol (`%`), indicating the OpenLane interactive prompt.

### 2. Load Required OpenLane Packages

Next, load the necessary OpenLane packages for the flow to run.

```tcl
package require openlane 0.9
```
<img width="736" height="361" alt="image" src="https://github.com/user-attachments/assets/20236e53-533f-4b43-abed-59a359795b01" />


Upon successful loading, you are ready to proceed with the flow.

### 3. Explore Available Designs

OpenLane comes with a **designs** folder containing many example projects. Locate and explore the available designs.

```bash
cd designs
ls
```
<img width="864" height="619" alt="image" src="https://github.com/user-attachments/assets/fd2db74e-52f2-4e4a-b51f-72d3fe8f81a7" />


The list shows all included design test cases—your own design should be placed here.

### 4. Inspect a Design Directory

Navigate into the target design directory (for example, `picorv32a`) and list its contents.

```bash
cd picorv32a
ls
```
<img width="870" height="715" alt="image" src="https://github.com/user-attachments/assets/dd998267-9b50-495a-967e-076ce9287222" />


You will see three primary files:

- `src/` — Contains the Verilog source file and SDC information.
- `config.tcl` — The PDK-specific configuration for this design.
- `config.json` — Optional; can bypass some configuration defaults.

The `src` directory typically contains the Verilog RTL file for the design along with its SDC (Synopsys Design Constraints).  
<img width="868" height="769" alt="image" src="https://github.com/user-attachments/assets/13af3b09-d889-42b4-80de-917be0bc6cc5" />


### 5. Examine Design Configuration

Open `config.tcl` to check or modify design-specific settings, such as clock period or overridden parameters.

Key configuration lines might look like:

```tcl
set ::env(CLOCK_PERIOD) 5.000
```
<img width="874" height="549" alt="image" src="https://github.com/user-attachments/assets/d0acb04e-33da-403b-b421-5d660d613e7a" />

This sets the clock period (for example, $5$ ns) for the OpenLane flow, which takes precedence over default values.

If you set a value such as:

```tcl
set ::env(CLOCK_PERIOD) 12
```

in another file or at a later stage, this last assignment gets the highest priority and will override previous values.

### 6. Precedence in Configuration

OpenLane uses the following precedence for design parameters:

- Default value set by OpenLane.
- Value in `config.tcl` (design-specific).
- Direct cmds.log within the session (highest priority).

For example, if `config.tcl` specifies:

```tcl
set ::env(CLOCK_PERIOD) 20
```

but the interactive session or another script later sets

```tcl
set ::env(CLOCK_PERIOD) 12
```

then $12$ ns will be used.

### 7. Prepare File System (Design Setup)

Return to the OpenLane prompt and prepare the file system for the flow. This step creates necessary directories and data structures for your design:

Note: Run the below command in openlane directory

```tcl
prep -design picorv32a
```
<img width="884" height="764" alt="image" src="https://github.com/user-attachments/assets/ee161951-902c-4fad-a856-01baa09ac5cb" />


**What happens in this step:**

- The preparation stage merges library (LEF) files.
- It combines the cell-level and technology-level LEF files into one, so subsequent flow steps can easily access all necessary cell and layer definitions from a single place.
- The directories and structure required by OpenLane for every flow stage are created.

You may see references to scripts such as `mergeLef.py` running in the output.

### 8. Verify Setup

Check if the preparation produced new directories and expected files inside your design directory.

```bash
ls
```
<img width="885" height="337" alt="image" src="https://github.com/user-attachments/assets/7d85feae-ad67-4419-8e12-e2d043b39d19" />


You should see newly created folders and possibly some generated configuration or log files.

---

# Review files after design prep and run synthesis

This lab walks you through the OpenLane automated RTL-to-GDSII flow for open-source hardware, focusing on initial setup, directory structure, and launching the synthesis stage. The aim is to familiarize you with the folder layout, tmporary and output files, key configuration, and how to monitor changes to parameters during execution. The main steps include navigating the `runs` directory, examining generated files, understanding configuration, and initiating synthesis.

## Lab Steps

### 1. Navigate to the Runs Directory

After setup, your project will have a `runs` directory, with a subfolder named by date (e.g., `12th_August`). Change into this directory to view the structure required by OpenLane.

```bash
cd runs/27-10_13-46
```

You can list the contents to see the standard folder layout:

```bash
tree
```
```
vsduser@pathanrehman:~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/27-10_13-46$ tree
.
├── cmds.log
├── config.tcl
├── logs
│   ├── 0-prep_runtime.txt
│   ├── cts
│   ├── cvc
│   ├── floorplan
│   ├── flow_summary.log
│   ├── klayout
│   ├── lvs
│   ├── magic
│   ├── placement
│   ├── routing
│   └── synthesis
├── OPENLANE_VERSION
├── PDK_SOURCES
├── reports
│   ├── cts
│   ├── cvc
│   ├── floorplan
│   ├── klayout
│   ├── lvs
│   ├── magic
│   ├── placement
│   ├── routing
│   └── synthesis
├── results
│   ├── cts
│   │   └── merged_unpadded.lef -> ../../tmp/merged_unpadded.lef
│   ├── cvc
│   │   └── merged_unpadded.lef -> ../../tmp/merged_unpadded.lef
│   ├── floorplan
│   │   └── merged_unpadded.lef -> ../../tmp/merged_unpadded.lef
│   ├── klayout
│   │   └── merged_unpadded.lef -> ../../tmp/merged_unpadded.lef
│   ├── lvs
│   │   └── merged_unpadded.lef -> ../../tmp/merged_unpadded.lef
│   ├── magic
│   │   └── merged_unpadded.lef -> ../../tmp/merged_unpadded.lef
│   ├── placement
│   │   └── merged_unpadded.lef -> ../../tmp/merged_unpadded.lef
│   ├── routing
│   │   └── merged_unpadded.lef -> ../../tmp/merged_unpadded.lef
│   └── synthesis
│       └── merged_unpadded.lef -> ../../tmp/merged_unpadded.lef
└── tmp
    ├── cts
    │   └── merged_unpadded.lef -> ../../tmp/merged_unpadded.lef
    ├── cvc
    │   └── merged_unpadded.lef -> ../../tmp/merged_unpadded.lef
    ├── floorplan
    │   └── merged_unpadded.lef -> ../../tmp/merged_unpadded.lef
    ├── klayout
    │   └── merged_unpadded.lef -> ../../tmp/merged_unpadded.lef
    ├── lvs
    │   └── merged_unpadded.lef -> ../../tmp/merged_unpadded.lef
    ├── magic
    │   └── merged_unpadded.lef -> ../../tmp/merged_unpadded.lef
    ├── merged.lef
    ├── merged_unpadded.lef
    ├── met_layers_list.txt
    ├── placement
    │   └── merged_unpadded.lef -> ../../tmp/merged_unpadded.lef
    ├── routing
    │   └── merged_unpadded.lef -> ../../tmp/merged_unpadded.lef
    ├── synthesis
    │   └── merged_unpadded.lef -> ../../tmp/merged_unpadded.lef
    ├── tracks_copy.info
    ├── trimmed.lib
    └── trimmed.lib.exclude.list

40 directories, 30 files
```
The output will show directories such as `tmp`, `results`, `reports`, and `logs`. Initially, only the `tmp` folder contains files; all others are empty until processing begins.

### 2. Explore the tmp Folder

The `tmp` folder holds intermediate files generated during the flow. For example, after merging, you might find a file like `merged.lef` (created by `merged.lef.py`). Inspect its contents to see layer and cell-level information.

```bash
cat tmp/merged.lef
```

This file contains detailed layout information, such as wire-level data (at the top) and appended cell-level information (e.g., macro definitions).

<img width="741" height="868" alt="image" src="https://github.com/user-attachments/assets/b79bdd3d-1032-496d-b443-b8c089ca99e1" />


### 3. Understand the Results, Reports, and logs Folders

- **results**: Contains output files for each stage (e.g., `synthesis`, `placement`, `flow`). Initially empty; populated as each stage completes.
- **reports**: Holds timing and other analysis reports if STC files are defined in your configuration. Also empty at the start.
- **logs**: Contains lock files for each step to prevent concurrent execution. The `config.tcl` file here (and sometimes in the `design` directory) is particularly important.

To view the current state of these folders:

```bash
ls -l results
ls -l reports
ls -l logs
```

<img width="734" height="874" alt="image" src="https://github.com/user-attachments/assets/bb2cec12-3bf3-44e2-b47d-9207c1a73534" />


### 4. Examine Conflict Article File

The `config.tcl` file shows which default parameters are being used in the current run, including PDK settings, track information, synthesis library, and timing libraries (min/max, transition). It is updated when configuration changes are made, providing a quick way to verify that your edits are reflected in the flow.

```bash
cat config.tcl
```

For example, if you change a parameter like `CORE_UTILIZATION` to 50 in your config and re-run the flow planning stage, this file will reflect the new value.

<img width="733" height="870" alt="image" src="https://github.com/user-attachments/assets/97f200f8-57b0-4a43-9b18-8e4434208430" />


### 5. Review cmds.log File

The `cmds.log` file logs all cmds.log executed during the flow. It is updated as steps are completed. Initially, it may be empty or minimal.

```bash
cat cmds.log
```

<img width="726" height="660" alt="image" src="https://github.com/user-attachments/assets/2a41d1a7-e9e8-4219-9017-b76fe90e9497" />


### 6. Launch Synthesis

With the environment prepared, initiate the synthesis stage, which includes RTL synthesis and ABC optimization. This process typically takes 3–5 minutes.

```bash
run_synthesis 
```

While synthesis runs, you can monitor progress in the terminal or by checking the `results/synthesis` directory as files are generated.

<img width="733" height="882" alt="image" src="https://github.com/user-attachments/assets/6537c9cc-925e-4375-b97f-0c982709ae84" />


### 7. Reference OpenLane Documentation

While the flow runs, review the official OpenLane documentation for further guidance. Search for “OpenLane GitHub” to find the repository, which contains usage instructions, examples, and issue tracking.

---

**Note:**  
Always check the `config.tcl` and `cmds.log` files after each stage to ensure your configuration is correctly applied and to review the executed workflow. tmporary and output files in `tmp` and `results` provide detailed insight into the process and should be inspected for debugging or validation.

--- 

## OpenLANE Project Git Link Description

This documentation provides an overview of key theoretical concepts behind GitHub, remote repositories, and the associated workflows that underpin collaborative open-source hardware projects. It also explores fundamental ideas regarding version control and automated design flows within the open-source electronic design automation (EDA) toolchain.

## Core Concepts

### What is GitHub and a Repository

**GitHub** is an online platform for hosting and managing remote repositories. A **repository** (repo) in this context acts as a centralized location for your project, enabling storage, structured organization, and version control. In simple terms, a repository is just a project folder that is made available for others to access, contribute to, or review. When a project is uploaded (pushed) to GitHub, it becomes accessible as a repository that anyone with permission can interact with.

<img width="1919" height="879" alt="image" src="https://github.com/user-attachments/assets/6e715378-1f6a-4ff3-8d5e-1927f1ea973c" />


### Organizations, Users, and Repository Structure

On GitHub, repositories are owned either by individual users or organizations. Each repository represents a distinct project. An organization can contain multiple repositories, each stemming from different project initiatives, and repository names are always prefixed with the organization or user name (e.g., fabulous/openlane).


### Remote vs Local Repositories

A **remote repository** is hosted externally—such as on GitHub, GitLab, or a private server—while a **local repository** resides on your computer. Synchronization between these occurs through explicit commands. This arrangement enables multiple contributors to work on the same project and keep their work up-to-date with others.

- *Remote repository*: Centralized, shared, accessible via internet.
- *Local repository*: Your own working copy, where you make changes.

Synchronization must be performed using Git operations like "push" (send changes to remote) and "pull" (fetch updates from remote).


### Remote URLs and Repository Management

Each remote repository is associated with a unique URL, which identifies its storage location (commonly HTTPS or SSH URLs). Remotes are linked to local repositories by names (commonly 'origin'), allowing you to manage connections to various remote repositories and coordinate updates.


### Cloning

**Cloning** means copying the entire contents and history of a remote repository to your local system. This process maintains an exact snapshot of the project, including its version history. The basic operation is initiated using a “clone” command that references the remote repository’s URL.


### Open-Source EDA Workflows and Toolchains

An open-source hardware EDA flow is typically structured as a sequence of toolchain steps, each responsible for a specific design transformation:

- **Inputs:** Generally include design source files (e.g., from a 'src' folder) and technology definitions (such as a Process Design Kit, PDK).
- **Process Steps:** Sequential stages such as synthesis, floorplanning, placement, clock tree synthesis (CTS), global routing, and more. Each stage uses the output of the previous as its input.
- **Outputs:** Final manufactured-ready representations, often in standardized formats for further processing or fabrication.

This approach emphasizes automation, reproducibility, and modularity in hardware design.


### Importance of Workflow Order

Design automation flows require strict adherence to step order. Skipping or reordering steps can result in failures, as each operation relies on the successful completion and outputs of earlier stages. For example, running "routing" without executing "CTS" (Clock Tree Synthesis) first will fail, since routing requires the clock network built during CTS.


### Automated vs Interactive Flows

Toolchains often support both:

- **Interactive mode**: Users execute stages step-by-step, allowing inspection and intervention at each point.
- **Automated runs**: A single command executes all required steps in sequence, suitable for standardized flows or batch processing.

Clear understanding of these modes enhances efficiency and error prevention in design environments.


## Key Points

- **GitHub** enables collaborative project development by hosting remote repositories accessible to others.
- Repositories are project folders, managed by users or organizations, each with distinct access and contribution controls.
- Local repositories interact with remotes via explicit push and pull commands—synchronization is never automatic.
- The *clone* operation initializes a local repository as an exact copy of a remote, including full project history.
- EDA toolchains in open-source hardware design use strictly ordered, modular steps—inputs, processing, and outputs are clearly defined.
- Skipping steps or misordering commands in the design process leads to flow errors, emphasizing the importance of process discipline.
- Workflow automation reduces manual intervention and repeatability, but interactive modes remain crucial for debugging and learning.
- Understanding the structure, connectivity, and workflow of open-source repositories is foundational for hardware collaboration and rapid design iteration.

---

# Steps To Characterize Synthesis Results

This lab focuses on post-synthesis analysis in a digital hardware workflow, specifically aiming to compute the **flop ratio**, verify the synthesis output, and inspect related synthesis statistics and reports. The key steps are:

- Locate and interpret synthesis statistics to find D flip-flop counts.
- Calculate the flop ratio using cell counts from reports.
- Explore synthesis output directories for netlists and reports.
- Review how results are presented and interpreted in the context of the workflow.

## Lab Steps

### 1. Find D Flip-Flop Count in Synthesis Statistics

- Open the synthesis statistics report provided by your synthesis tool.
- Look for the final D flip-flop (e.g., the cell type `dfxtp_2`) count and the total number of cells.

  Example values:
  - D flip-flop count: **1613**
  - Total cell count: **14876**

  <img width="734" height="881" alt="image" src="https://github.com/user-attachments/assets/0aa55582-04d0-40af-86b4-7b73f8d0c6e9" />


### 2. Calculate Flop Ratio

- Use a calculator or your preferred method to compute the flop ratio as:

  ```
  flop_ratio = D_flip_flop_count / Total_cell_count
  ```

  For the given example values:
  ```
  flop_ratio = 1613 / 14876
  ```

<img width="753" height="884" alt="image" src="https://github.com/user-attachments/assets/ae11ef05-0bed-4059-be92-14b866fc43f1" />


- The output is:
  ```
  0.108429
  ```

  - To express as a percentage:
    ```
    flop_ratio_percent = flop_ratio * 100
    ```
    So:
    ```
    flop_ratio_percent = 10.8429%
    ```

  <img width="757" height="878" alt="image" src="https://github.com/user-attachments/assets/8a254aed-e668-4739-bf16-52c6016e1099" />


### 3. Locate Synthesis Results and Netlist

- Navigate to the results directory (typically named `results` or similar).
- Confirm that synthesis has completed and output files (e.g., netlist files) are present.

  ```
  cd results
  ls
  ```

  <img width="736" height="313" alt="image" src="https://github.com/user-attachments/assets/9d289797-c16a-43fe-a5c4-938b2d51bd42" />


- Inspect the synthesized netlist, ensuring that all standard cell mappings have been performed by the synthesis tool (such as ABC).

(Add Synthesis verilog file here)

### 4. Check Reports Directory for Synthesis and Timing Reports

- To obtain detailed synthesis and timing reports, access the `reports` folder (e.g., `cd reports`).

  ```
  cd reports
  ls -ltr
  ```

  <img width="731" height="717" alt="image" src="https://github.com/user-attachments/assets/8f7c283d-613e-4b71-a4b1-38f30f1ef3cc" />


- The last file listed (by modification time) typically contains the most recent synthesis statistics.

  ```
  ls -ltr
  cat 1-yosys_4.stat.rpt
  ```

 ```
=== picorv32a ===

   Number of wires:              14596
   Number of wire bits:          14978
   Number of public wires:        1565
   Number of public wire bits:    1947
   Number of memories:               0
   Number of memory bits:            0
   Number of processes:              0
   Number of cells:              14876
     sky130_fd_sc_hd__a2111o_2       1
     sky130_fd_sc_hd__a211o_2       35
     sky130_fd_sc_hd__a211oi_2      60
     sky130_fd_sc_hd__a21bo_2      149
     sky130_fd_sc_hd__a21boi_2       8
     sky130_fd_sc_hd__a21o_2        57
     sky130_fd_sc_hd__a21oi_2      244
     sky130_fd_sc_hd__a221o_2       86
     sky130_fd_sc_hd__a22o_2      1013
     sky130_fd_sc_hd__a2bb2o_2    1748
     sky130_fd_sc_hd__a2bb2oi_2     81
     sky130_fd_sc_hd__a311o_2        2
     sky130_fd_sc_hd__a31o_2        49
     sky130_fd_sc_hd__a31oi_2        7
     sky130_fd_sc_hd__a32o_2        46
     sky130_fd_sc_hd__a41o_2         1
     sky130_fd_sc_hd__and2_2       157
     sky130_fd_sc_hd__and3_2        58
     sky130_fd_sc_hd__and4_2       345
     sky130_fd_sc_hd__and4b_2        1
     sky130_fd_sc_hd__buf_1       1656
     sky130_fd_sc_hd__buf_2          8
     sky130_fd_sc_hd__conb_1        42
     sky130_fd_sc_hd__dfxtp_2     1613
     sky130_fd_sc_hd__inv_2       1615
     sky130_fd_sc_hd__mux2_1      1224
     sky130_fd_sc_hd__mux2_2         2
     sky130_fd_sc_hd__mux4_1       221
     sky130_fd_sc_hd__nand2_2       78
     sky130_fd_sc_hd__nor2_2       524
     sky130_fd_sc_hd__nor2b_2        1
     sky130_fd_sc_hd__nor3_2        42
     sky130_fd_sc_hd__nor4_2         1
     sky130_fd_sc_hd__o2111a_2       2
     sky130_fd_sc_hd__o211a_2       69
     sky130_fd_sc_hd__o211ai_2       6
     sky130_fd_sc_hd__o21a_2        54
     sky130_fd_sc_hd__o21ai_2      141
     sky130_fd_sc_hd__o21ba_2      209
     sky130_fd_sc_hd__o21bai_2       1
     sky130_fd_sc_hd__o221a_2      204
     sky130_fd_sc_hd__o221ai_2       7
     sky130_fd_sc_hd__o22a_2      1312
     sky130_fd_sc_hd__o22ai_2       59
     sky130_fd_sc_hd__o2bb2a_2     119
     sky130_fd_sc_hd__o2bb2ai_2     92
     sky130_fd_sc_hd__o311a_2        8
     sky130_fd_sc_hd__o31a_2        19
     sky130_fd_sc_hd__o31ai_2        1
     sky130_fd_sc_hd__o32a_2       109
     sky130_fd_sc_hd__o41a_2         2
     sky130_fd_sc_hd__or2_2       1088
     sky130_fd_sc_hd__or2b_2        25
     sky130_fd_sc_hd__or3_2         68
     sky130_fd_sc_hd__or3b_2         5
     sky130_fd_sc_hd__or4_2         93
     sky130_fd_sc_hd__or4b_2         6
     sky130_fd_sc_hd__or4bb_2        2

   Chip area for module '\picorv32a': 147712.918400
  ```

- Verify your previous calculations and review other statistics as needed.

### 5. Review Pre-Layout (OpenSTR) and Other Reports

- Optionally, check the open STR (Static Timing Report) or other pre-layout analysis reports in the relevant directory.

  <img width="734" height="876" alt="image" src="https://github.com/user-attachments/assets/b4b48bc3-cc17-433a-a9c8-9a5ef89fedf5" />


### 6. Documentation and Further Study

- It is recommended to review provided documentation and relevant tutorial videos for enhanced understanding and context on synthesis and tool interpretation.

---
