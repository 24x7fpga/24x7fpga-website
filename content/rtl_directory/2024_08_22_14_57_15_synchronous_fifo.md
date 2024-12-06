+++
title = "Synchronous FIFO"
author = ["Kiran"]
date = 2024-08-22T14:57:00-04:00
tags = ["rtl"]
draft = false
css = "../../zcustom.css"
+++

[Source](https://github.com/24x7fpga/RTL/tree/master/rtl_designs/fifo_buffer_syn) -- [RTL Design Directory]({{< relref "2024_06_05_00_21_53_rtl_design_directory.md" >}})


## Synchronous FIFO {#synchronous-fifo}

A FIFO (First-In-First-Out) is a storage buffer that is widely used in digital systems where the data that is stored in the memory is retrieved first, hence the name FIFO.

A synchronous FIFO is a type of FIFO where the write and read operations are synchronized with the same clock signal.

{{< figure src="/ox-hugo/fifo.svg" caption="<span class=\"figure-number\">Figure 1: </span>FIFO Buffer" >}}

The data retrieval is based on the order of the data written to the FIFO buffer. A FIFO buffer can be categorized as the first word fall through (FWFT) FIFO or a standard FIFO based on the data read-out configuration. From Figure 1, if the data is read from the FIFO head without any assertion of the control signal it is called as a FWFT FIFO. This type of FIFO is particularly useful in situations where minimal latency is desired, and the first data word needs to be accessed as soon as it's written. The read operation still requires the read enable signal to increment the read pointer. The difference is that the first data word is already present on the output without requiring an initial read. Whereas in a standard FIFO, separate pointers for write and read are initiated to write and read data from the FIFO. The read point remains unchanged until a read enable signal is applied and the data from the head of the FIFO is retrieved on the next clock cycle.


### Control Signals {#control-signals}

The two control signals to write and read the data from the FIFO is:

1.  Write Enable(wr_en): Write enable is a signal when asserted, will write data to the end of the FIFO buffer.
2.  Read Enable(rd_en) Read enable is a signal when asserted, will read data from the head of the FIFO buffer.


### Write and Read Pointers {#write-and-read-pointers}

Two pointers are used as the address for writing and reading data from the FIFO location and they are:

1.  Write Pointer(wr_ptr): A write pointer is used to keep track of where the next data should be written. It increments on every clock cycle when the write enable signal is high.
2.  Read Pointer(rd_ptr): Read pointer is used to keep track of where the next data should be read from. It increments on every clock cycle when the read enable signal is high.


### Full and Empty Flags {#full-and-empty-flags}

1.  Full Flag: Indicates that the FIFO has reached its maximum capacity, preventing any further write operations until space is freed up.
2.  Empty Flag: Indicates that the FIFO is empty, meaning no data is available to be read.

The write pointer specifies the location within the FIFO where data is stored. Upon each write operation, the write pointer advances to the subsequent position. If the FIFO reaches its maximum capacity, further write operations are halted until sufficient space is made available. Similarly, data retrieval occurs at the location indicated by the read pointer, which progresses to the next position following each read operation. Should the FIFO become empty, read operations are suspended until new data is written.


### Reference {#reference}

1.  [Chu, Pong P. FPGA Prototyping by SystemVerilog Examples: Xilinx MicroBlaze MCS SoC Edition. John Wiley &amp; Sons, 2018.](https://www.wiley.com/en-us/FPGA+Prototyping+by+SystemVerilog+Examples%3A+Xilinx+MicroBlaze+MCS+SoC+Edition-p-9781119282662)
