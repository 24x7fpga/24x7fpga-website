+++
title = "RTL Design Directory"
author = ["Kiran"]
date = 2024-06-05T00:21:00-04:00
tags = ["toc"]
draft = false
css = "../../zcustom.css"
+++

Welcome to my comprehensive collection of [RTL(Register Transfer Level)](<https://en.wikipedia.org/wiki/Register-transfer_level>) designs. It is an evolving collection highlighting my projects, skills, and achievements in digital circuit design and verification. I specialize in developing efficient, high-performance hardware architectures using industry-standard languages like [SystemVerilog](<https://ieeexplore.ieee.org/document/10458102>) and [Verilog](<https://accellera.org/images/downloads/standards/v-ams/VAMS-LRM-2023.pdf>). Explore my blog for insightful articles on the latest trends and best practices in RTL design, and visit the tutorials section for step-by-step guides to mastering this critical aspect of hardware engineering. Connect with me to collaborate on innovative solutions and advance your knowledge in RTL design.


## My Work Flow {#my-work-flow}

-   Operating System and Text Editor
-   Folder Structure
-   Design Scripts


## Design Tools {#design-tools}

-   [Icarus verilog](<https://steveicarus.github.io/iverilog/index.html>)
-   [GTKWave](<https://gtkwave.sourceforge.net/>)
-   [Vivado Installation]({{< relref "2024_07_28_10_50_56_vivado_installation.md" >}})


## Hardware {#hardware}

-   [Digilent Zybo-Z7-20](<https://digilent.com/reference/programmable-logic/zybo-z7/start>)
-   [Digilent Zedboard](<https://digilent.com/reference/programmable-logic/zedboard/start>)
-   [Zynq UltraScale+ RFSoC ZCU1275](<https://www.xilinx.com/products/boards-and-kits/zcu1275.html>)
-   Miscellaneous


## Important Concepts {#important-concepts}

-   [Throughput and Latency]({{< relref "2024_07_23_21_33_58_throughput_and_latency.md" >}})
-   [Synchronous and Asynchronous Reset]({{< relref "2024_07_25_11_34_45_synchronous_and_asynchronous_reset.md" >}})
-   [Casex and Casez]({{< relref "2024_07_25_11_36_52_casex_and_casez.md" >}})
-   [Full Case and Parallel Case]({{< relref "2024_07_26_17_05_01_full_case_and_parallel_case.md" >}})
-   [Assign and If-Else Statement]({{< relref "2024_07_26_17_07_08_assign_and_if_else_statement.md" >}})
-   [Blocking and Non-Blocking Assignment]({{< relref "2024_07_26_17_10_23_blocking_and_non_blocking_assignment.md" >}})
-   [Always@(\*) and Always_Comb]({{< relref "2024_08_05_16_05_55_always_and_always_comb.md" >}})
-   [Mealy Machine and Moore Machine]({{< relref "2024_08_05_16_07_25_mealy_machine_and_moore_machine.md" >}})
-   [Clock Gating]({{< relref "2024_09_13_12_36_11_clock_gating.md" >}})
-   [Difference between "==" and "==="]({{< relref "2024_10_07_00_08_52_difference_between_and.md" >}})
-   [Valid-Ready Protocol]({{< relref "2024_11_29_18_17_10_valid_ready_protocol.md" >}})


## Generic RTL Blocks {#generic-rtl-blocks}

-   [Half Adder]({{< relref "2024_06_05_22_56_41_half_adder.md" >}})
-   [Full Adder]({{< relref "2024_06_08_11_32_42_full_adder.md" >}})
-   [Multiplexer]({{< relref "2024_06_13_12_22_45_multiplexer.md" >}})
-   [De-Multiplexer]({{< relref "2024_06_14_22_07_44_de_multiplexer.md" >}})
-   [Encoder]({{< relref "2024_06_23_20_40_25_encoder.md" >}})
-   [Decoder]({{< relref "2024_06_23_21_30_22_decoder.md" >}})
-   [Comparator]({{< relref "2024_07_06_15_54_47_comparator.md" >}})
-   [Adder-Subtractor]({{< relref "2024_07_06_16_50_01_adder_subtractor.md" >}})
-   [Register]({{< relref "2024_07_06_19_23_36_register.md" >}})
-   [Shift Register]({{< relref "2024_07_06_19_54_50_shift_register.md" >}})
-   [Universal Shift Register]({{< relref "2024_07_10_20_30_35_universal_shift_register.md" >}})
-   [Up/Down Counter]({{< relref "2024_07_16_22_30_28_up_down_counter.md" >}})
-   [Code Converter]({{< relref "2024_07_18_21_58_46_code_converter.md" >}})
-   [Mod-N Counter]({{< relref "2024_07_18_23_30_22_mod_n_counter.md" >}})
-   [Ring Counter]({{< relref "2024_07_22_11_17_17_ring_counter.md" >}})
-   [Edge Detector]({{< relref "2024_07_22_14_10_16_edge_detector.md" >}})
-   [Frequency Divider]({{< relref "2024_07_22_15_24_11_frequency_divider.md" >}})
-   [Read Only Memory]({{< relref "2024_07_23_13_11_22_read_only_memory.md" >}})


## FIFO Design {#fifo-design}

-   [Synchronous FIFO]({{< relref "2024_08_22_14_57_15_synchronous_fifo.md" >}})
-   [Asynchronous FIFO]({{< relref "2024_09_07_10_52_50_asynchronous_fifo.md" >}})


## Static Timing Analysis {#static-timing-analysis}

-   [Latch and Flip-Flop]({{< relref "2024_12_21_latch_and_flip_flop.md" >}})


## Clock Domain Crossing {#clock-domain-crossing}

-   Theory
-   Pulse Stretcher
-   MUX Synchronization
-   Two-Flop Synchronization


## Communication Protocols {#communication-protocols}

-   [UART]({{< relref "2024_09_17_20_46_04_uart.md" >}})
-   [SPI]({{< relref "2024_09_18_22_25_40_spi.md" >}})
-   I2C
-   VGA
-   I2S
-   HDMI
-   AXI-Stream
-   AXI4-Lite
-   AXI-Interconnect
-   8b/10b Encoding


## Pipeline {#pipeline}

-   Global stall
-   Half-buffer
-   Skid buffer
-   2-depth Fifo
-   Pipeline Fifo
-   Elastic Buffer


## FPGA Projects {#fpga-projects}

-   Floating point unit
-   AXI BUS interface
-   DMA controller
-   Error Correction Code
-   DSP core for audio processing
-   Phase-Locked Loop
-   H.264 Video Encoder
-   Convolution Neural Network
-   Hardware Random Number generator
-   Network-on-chip
-   DDR Memory Controller


## Computer Architecture {#computer-architecture}

-   [Single Cycle MIPS Design](https://github.com/24x7fpga/MIPsSingleCycle)
-   Multi Cycle MIPS design
-   RISC-V Core
