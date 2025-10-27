## Introduction to QFN-48 Package, chip, pads, core, die and IPs

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
- These functional blocks are typically represented as separate areas or macros within the chip layout.  
  <img width="997" height="718" alt="image" src="https://github.com/user-attachments/assets/eebcdd8f-d6f5-46cb-aba1-2a5b987e9266" />


### Foundry and IP (Intellectual Property) Concepts

- A **foundry** refers to the specialized manufacturing facility where chips are produced. Foundries contain deposition, lithography, and other fabrication equipment.

- **Foundry IPs** (Intellectual Properties) are pre-designed and verified blocks (such as ADCs, DACs, memory modules) provided by the foundry for integration into custom chips. These blocks require significant engineering expertise to create and are shared as reusable intellectual property elements.

- The distinction between "macros" and "IPs":
  - **Macros**: Typically refer to standard digital logic blocks.
  - **IPs**: More complex, often mixed-signal or specialized circuits, requiring advanced design knowledge.

- Effective chip design requires continuous communication with the foundry, typically through standardized interface files provided by the foundry, enabling the translation of design intent into manufacturable hardware.  
  <img width="1018" height="729" alt="image" src="https://github.com/user-attachments/assets/f866f890-e17e-4052-884f-86ae805a0cdc" />


## Key Points

- Understanding **board layouts** and **block diagrams** is foundational before delving into chip-level design.
- The term **package** refers to the entire external housing of the chip, not just the silicon die.
- **Wire bonds** and **pads** are physical mechanisms for interfacing between the external board and internal chip logic.
- The **core** of the chip contains the main processing and logical functions; the periphery involves communication and I/O interfaces.
- **System on Chip (SoC)** architecture groups essential components (memory, analog blocks, communication modules) for integrated functionality.
- **Foundry and IP** are central to modern chip design, with foundries supplying both the physical manufacturing capability and foundational building blocks for design integration.
- Successful hardware design and fabrication rely on standardized communication between designers (engineers) and foundry operations, often mediated by shared interface files and design standards.  
- Mastery of these fundamentals opens up advanced work in hardware design, SoC integration, and semiconductor manufacturing.

---

##  Introduction to RISC-V

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
- The translation process ensures that the logic authored by the programmer becomes a stream of hardware-level commands.


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
## From Software Applications to Hardware

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

## Introduction To All Components Of Open-Source Digital Asic Design

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
## Simplified RTL2GDS flow

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

- Standard cells are modular building blocks with predefined electrical and physical characteristics, simplifying synthesis and placement.

- Power and clock distribution networks are crucial for circuit reliability, necessitating careful planning to reduce IR drop, electromigration, and timing mismatches.

- Verification occurs at multiple stages to guarantee functionality (functional and layout-vs-schematic checks), manufacturability (design rule checks), and timing compliance (static timing analysis).

- The entire design is captured in GDSII format for mask generation and manufacturing. Successful ASICs require close attention to detail at each stage to meet the design’s functional, physical, and performance goals.

---

## Introduction to OpenLANE and Strive chipsets
