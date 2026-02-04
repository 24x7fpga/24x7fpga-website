+++
title = "Architecture vs. Microarchitecture"
author = ["kiran"]
date = 2025-12-16T18:56:00-05:00
tags = ["riscv", "ca"]
draft = false
css = "../../zcustom.css"
+++

Modern computer systems are built using levels of abstraction that manage complexity by clearly separating responsibilities. Among these layers, architecture and microarchitecture form one of the most important boundaries—especially for understanding the concepts in computer architecture. Figure 1, shows a simplified abstraction stack where each layer builds upon the one below it, hiding complexity while exposing only what is necessary.

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

CPU performance is determined by three fundamental parameters: the number of instructions a program executes, the average number of clock cycles required per instruction (CPI), and the duration of each clock cycle. Together, these parameters define the CPU performance equation:

\begin{equation}
\label{eq.1}
Execution \\; Time = \underbrace{\frac{Instructions}{Program}}\_\text{Instruction Count} \times \underbrace{\frac{Cycles}{Instruction}}\_\text{CPI} \times \underbrace{\frac{Time}{Cycle}}\_\text{Clock Period}
\end{equation}

-   Instruction Count: The total number of instructions executed by a program. Determined by the program’s algorithm, the quality of compiler optimizations, and the underlying instruction set architecture (ISA).
-   Cycles Per Instruction (CPI): The average number of clock cycles required to execute an instruction. CPI depends heavily on the CPU’s microarchitecture, including pipeline depth, instruction-level parallelism, cache behavior (hits and misses), and instruction complexity.
-   Clock Period: The duration of one clock cycle. Clock period is the inverse of the processor’s clock frequency, which is measured in hertz (Hz).

The above equation can be deduced to:

\begin{equation}
\label{eq.2}
Execution \\; Time = \frac{Instructions Count \times CPI}{f}
\end{equation}

Therefore, CPU performance equation is:

\begin{equation}
\label{eq.3}
Performace = \frac{1}{Execution \\; Time}
\end{equation}
