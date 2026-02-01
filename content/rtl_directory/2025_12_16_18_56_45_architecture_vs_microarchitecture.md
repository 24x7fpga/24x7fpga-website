+++
title = "Architecture vs. Microarchitecture"
author = ["kiran"]
date = 2025-12-16T18:56:00-05:00
tags = ["riscv", "ca"]
draft = false
css = "../../zcustom.css"
+++

Modern computer systems are built using levels of abstraction that manage complexity by clearly separating responsibilities. Among these layers are architecture and microarchitecture form one of the most important boundaries—especially for understanding the concepts in computer architecture. Figure 1, shows a simplified abstraction stack where each layer builds upon the one below it, hiding complexity while exposing only what is necessary.

{{< figure src="/ox-hugo/abstraction.svg" caption="<span class=\"figure-number\">Figure 1: </span>Levels of Abstraction" class="center !important" width="200px" >}}


## Architecture: The Programmer’s View {#architecture-the-programmer-s-view}

Architecture, more precisely the Instruction Set Architecture (ISA), defines the contract between software and hardware. It specifies what the machine does from the perspective of a programmer or compiler, without revealing how those behaviors are implemented internally.

At the architectural level:

-   The instruction set (arithmetic, control, memory operations)
-   Programmer-visible registers
-   Addressing modes and data types
-   Memory consistency model
-   Exceptions and interrupts

This level is critical because it guarantees correctness and compatibility. Any processor that correctly implements an ISA must execute the same program and produce the same results, regardless of its internal design. Many different architectures exist, such as x86, MIPS, RISC-V.

Example:

-   RISC-V specifies a load–store architecture with a fixed set of integer registers. Whether the processor is simple or highly optimized, it must obey these rules for software to work correctly.


## Microarchitecture: The Hardware Implementation {#microarchitecture-the-hardware-implementation}

Microarchitecture describes how the ISA is implemented in hardware. It focuses on internal design choices that are invisible to software but crucial for performance, power efficiency, and silicon area.

Microarchitectural decisions include:

-   Pipeline depth and structure
-   In-order vs. out-of-order execution
-   Issue width and execution units
-   Cache hierarchy and policies
-   Branch prediction techniques
-   Clock frequency and power optimizations

Different microarchitectures can implement the same ISA while exhibiting vastly different performance characteristics.

Example: Two processors may both implement RISC-V:

-   One uses a simple 5-stage in-order pipeline.
-   Another uses a deep, out-of-order pipeline with aggressive speculation
-   Both execute the same programs correctly, but one may be significantly faster or more power-efficient.


## What is Computer Architecture? {#what-is-computer-architecture}

It is the science and art of designing, selecting and interconnecting hardware components and designing the hardware/software interface to create a computing system that meets functional, performance, energy consumption, cost and other specific goals. (Source: Onur Mutlu)


## CPU Performance Equation {#cpu-performance-equation}

\begin{equation}
\label{eq.1}
Execution \quad Time = \frac{Instructions}{Programs} \times \frac{Cycles}{Instructions} \times \frac{Time}{Cycle}
\end{equation}
