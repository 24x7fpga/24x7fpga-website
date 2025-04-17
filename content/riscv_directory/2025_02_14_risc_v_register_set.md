+++
title = "RISC-V Register Set"
author = ["Kiran"]
date = 2025-02-14T15:38:00-05:00
tags = ["riscv"]
draft = false
css = "../../zcustom.css"
+++

##  {#d41d8c}

Registers are fundamental components of a processor, serving as high-speed storage units that enable the CPU to access data, addresses, and instructions efficiently. Since registers are located within the CPU, they provide significantly faster access compared to main memory, enhancing overall execution speed. The table below outlines the registers used in a RISC-V CPU and their functions.

| Name   | Register Name | Description                     |
|--------|---------------|---------------------------------|
| zero   | x0            | Constant Value 0                |
| ra     | x1            | Return Address                  |
| sp     | x2            | Stack Pointer                   |
| gp     | x3            | Global Pointer                  |
| tp     | x4            | Thread Pointer                  |
| t0-t2  | x5-x7         | Temporary Registers             |
| s0/fp  | x8            | Saved Register/Frame Pointer    |
| s1     | x9            | Saved Register                  |
| a0-a1  | x10-x11       | Function Arguments/Return Value |
| a2-a7  | x12-x17       | Functions Arguments             |
| s2-s11 | x18-x27       | Saved Registers                 |
| t3-t6  | x28-x31       | Temporary Registers             |

RISC-V features a total of 32 registers, numbered from 0 to 31, each serving a specific purpose. These registers play a crucial role in storing data, addresses, and intermediate results, optimizing the efficiency of program execution.


#### Zero Register {#zero-register}

The `zero` register always holds the constant value 0. Any value written to this register is discarded, making it useful for operations where a fixed zero value is required.


#### Storeage Registers {#storeage-registers}

Registers `s0–s11` and `t0–t6` are primarily used for storing variables during program execution. These registers help preserve data that needs to be accessed frequently without relying on slower memory.


#### Function Calls {#function-calls}

Certain registers in RISC-V are designated for handling function calls. The `ra` (return address) register stores the address to which the program should return after a function completes. Additionally, registers `a0–a7` are used for passing function arguments and storing return values.


#### Stack Pointer {#stack-pointer}

The stack pointer `sp` is a register that holds the address of the stack (memory). It typically points to the top of the stack and updates as data is pushed or popped, decreasing when new data is stored and increasing when data is removed. This register primarily utilized in function calls, local variable storage, and managing program execution flow.


#### Global Pointer {#global-pointer}

The global pointer `gp` is a register used to access globally stored variables before a program starts executing. It provides a reference to static and global variables, allowing efficient memory access without repeated address calculations during runtime


#### Thread Pointer {#thread-pointer}

This a register that is dedicated to store the thread's local memory. A `thread` is smallest unit of instructions within a process that take turns being executed on the processor. A program running on the computer is called a `process`. Computer can multiple processes at the same time, each process consists of one or more threads running simultanelously.
