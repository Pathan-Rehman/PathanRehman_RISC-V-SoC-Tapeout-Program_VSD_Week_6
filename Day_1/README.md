## Summary

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
