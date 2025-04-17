+++
title = "Microarchitecture Overview"
author = ["Kiran"]
date = 2025-02-21T14:59:00-05:00
tags = ["riscv"]
draft = false
css = "../../zcustom.css"
+++

##  {#d41d8c}

Figure 1 illustrates a simplified RISC-V microarchitecture, highlighting only the major functional units. These units play a crucial role in executing instructions efficiently. The key components can be categorized as follows:

1.  Program Counter (PC) – Keeps track of the instruction sequence by incrementing to the next program instruction on every clock cycle.
2.  Instruction Memory – Stores the program instructions that are fetched and executed sequentially.
3.  Register File – Receives instructions from the instruction memory, decodes them, and provides the appropriate inputs to the ALU.
4.  Arithmetic Logic Unit (ALU) – Performs mathematical and logical operations on the provided inputs.
5.  Data Memory – Stores ALU results or acts as a temporary storage unit for data required in future computations.

{{< figure src="/ox-hugo/risc_v_functional_units.svg" caption="<span class=\"figure-number\">Figure 1: </span>Abstract Microarchitecture Overview" >}}

Regardless of the specific instruction being executed, every instruction cycle in a RISC-V processor follows these two fundamental steps:

1.  Instruction Fetch – The _program counter_ (PC) fetches the instruction from the instruction memory.
2.  Register Read – Based on the instruction fields, one or two registers are selected and read from the register file.


### Program Flow {#program-flow}

The _program counter_ (PC) increments to fetch the next instruction from the instruction memory. In the RISC-V architecture, instructions are stored as 32-bit values, each representing a specific operation.

Once fetched, this 32-bit instruction is sent to the register file, where the appropriate registers are accessed. For example, from the Figure 1, the registers `rs1`, `rs2`, and `rd` are represented by 5-bit fields A1, A2, and A3, allowing for a total of 32 addressable registers, as specified in the RISC-V architecture manual.

Based on the instruction type:

-   The register file outputs two 32-bit values, RD1 and RD2, which serve as inputs to the Arithmetic Logic Unit (ALU) for computation.
-   The ALU processes these values, performing arithmetic or logical operations.
-   The result is either stored back in the register file, in register `rd` via the WD3 pin or written to memory for later use.

This describes a basic instruction execution flow. A more detailed breakdown of each step will be explored in upcoming sections.
