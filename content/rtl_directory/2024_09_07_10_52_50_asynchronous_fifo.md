+++
title = "Asynchronous FIFO"
author = ["Kiran"]
date = 2024-09-07T10:52:00-04:00
tags = ["rtl"]
draft = false
css = "../../zcustom.css"
+++

[Source](https://github.com/24x7fpga/RTL/tree/master/rtl_designs/fifo_asyn) -- [RTL Design Directory]({{< relref "2024_06_05_00_21_53_rtl_design_directory.md" >}})


## Asynchronous FIFO {#asynchronous-fifo}

An Asynchronous FIFO is a type of buffer where the write and read operations are controlled by different or unsynchronized clock signals. This makes it ideal for digital systems where data is transferred between components running at different speeds, such as when data is transmitted from a peripheral device to a CPU operating at a different clock frequency than that of the peripheral.

The primary role of the asynchronous FIFO is to ensure smooth data flow between these distinct clock domains, preventing data loss during transmission. By temporarily storing data in the buffer, the asynchronous FIFO allows the system to handle varying clock speeds without missing any critical data.


### Handling Pointer Synchronization {#handling-pointer-synchronization}

In an asynchronous FIFO, the `write pointer` is updated in the write clock domain, while the `read pointer` is updated in the read clock domain. The `full flag` is generated based on the write clock, and the `empty flag` is generated based on the read clock. Challenge arises when comparing the write and read pointers to generate the full and empty flags, as the pointers are updated in different clock domains from each other. This is different from a synchronous FIFO, where both pointers are updated in the same clock domain, making comparisons straightforward.

The key issue with asynchronous FIFOs is metastability—a situation where the design works fine in simulations but fails in real hardware due to timing violations when crossing clock domains. This can lead to unreliable data handling or system failures. One of many solutions for clock-domain crossing is to use a `two-flop synchronizer`.

When implementing a `two-flop synchronizer` in asynchronous FIFOs, one critical constraint is ensuring that only one bit of the data signal changes at a time while crossing from one clock domain to another. This minimizes the risk of metastability. To meet this constraint, gray code is used instead of a traditional binary counter for the read and write pointers. Unlike binary counters, where multiple bits can change simultaneously, gray code ensures that only a single bit changes with each transition. This makes Gray code ideal for safely synchronizing data between different clock domains, providing a robust solution for clock domain crossings in digital design.


#### Gray Code Counter {#gray-code-counter}

Detailed design for binary to Gray code and Gray to binary conversion [here](https://24x7fpga.com/rtl_directory/2024_07_18_21_58_46_code_converter/). From the table below, you’ll notice that Gray code exhibits a mirrored symmetry from decimal values 8 to 15. This makes Gray code a symmetric code, except for the most significant bit (MSB).

| Decimal system | Binary System | Gray Code |
|----------------|---------------|-----------|
| 0              | 0000          | 0000      |
| 1              | 0001          | 0001      |
| 2              | 0010          | 0011      |
| 3              | 0011          | 0010      |
| 4              | 0100          | 0110      |
| 5              | 0101          | 0111      |
| 6              | 0110          | 0101      |
| 7              | 0111          | 0100      |
| 8              | 1000          | 1100      |
| 9              | 1001          | 1101      |
| 10             | 1010          | 1111      |
| 11             | 1011          | 1110      |
| 12             | 1100          | 1010      |
| 13             | 1101          | 1011      |
| 14             | 1110          | 1001      |
| 15             | 1111          | 1000      |

For instance, if the FIFO depth is eight, the three least significant bits (LSBs) of the Gray code are used as the address to access FIFO locations. The MSB, on the other hand, is crucial for checking the FIFO full condition. Hence, for a N-depth FIFO (from N-1 to 0), the number of bits required to represent the pointer is log₂(N) + 1. For example, for a 8-depth FIFO (0 to 7 memory location), log₂(8) + 1 = 3 + 1 = 4 bits are required for pointer representation.


#### Two-Flop Synchronizer {#two-flop-synchronizer}

A two-flop synchronizer consists of two flip-flops arranged in series without any combinational logic in between. By placing these flip-flops close together, the design minimizes propagation delay, improving the reliability of data transfer between asynchronous clock domains.

The synchronizer circuit ensures reliable data transfer between clock domains by sampling the incoming signal with the receiving clock, aligning it to the timing of the destination clock domain. This helps prevent metastability and ensures the stable operation of the system. A two-flop synchronizer is shown in Figure 1.

{{< figure src="/ox-hugo/2ff_sync.svg" caption="<span class=\"figure-number\">Figure 1: </span>Two-Flop Synchronizer" >}}

In a two-flip-flop synchronizer, the first flip-flop captures the asynchronous input signal as it enters the new clock domain. This signal then remains in the first stage for a full clock cycle, allowing any metastability in the signal to settle. After this cycle, the stabilized signal is passed to a second flip-flop, where it is sampled again by the same clock. The second-stage flip-flop ensures that the signal is now stable, valid, and fully synchronized with the new clock domain. This process helps eliminate timing issues when signals transition between different clock domains.

```verilog
always@(posedge sync_clk)
        if(!sync_rst)
          {sync_out, sync_temp} <= 'h0;
        else
          {sync_out, sync_temp} <= {sync_temp, sync_in};
```

Two-flop synchronizer design can be found [here](https://github.com/24x7fpga/iVerilog/blob/master/design/fifo_asyn/fifo_sync.v).


### FIFO Design {#fifo-design}

When working on a complex design, it’s a good practice to break it down into smaller, manageable parts. This not only simplifies the design process but also makes it easier to understand and debug. In Figure 2, you can see the complete design of an asynchronous FIFO, divided into its essential components for clarity.

{{< figure src="/ox-hugo/asyn_fifo.svg" caption="<span class=\"figure-number\">Figure 2: </span>Asynchronous FIFO" >}}


### Pointer Generation and Comparision {#pointer-generation-and-comparision}

Pointer generation and comparison in asynchronous FIFOs involve tracking the read and write pointer across different clock domains.


#### Write Pointer {#write-pointer}

All the variables discussed in this section are generated within the `write clock domain`, ensuring they are synchronized and operate in alignment with the write-side logic of the asynchronous FIFO.

-   Write Address: An N-bit binary counter is used to generate the address for writing to the FIFO when it's not full and the write enable signal is high.

-   Write Pointer: A binary-to-Gray code converter is implemented to translate the write address from binary to Gray code, allowing for synchronization with the read clock domain.

-   Full Flag: The write pointer (in Gray code) is compared with the read pointer (also in Gray code) from the two-flop synchronizer to generate the full flag. If the first two most significant bits (MSBs) of the write and read pointers are inverted, and the remaining bits (from the 3rd MSB to the LSB) are the same, the full flag is set high, indicating the FIFO is full.

N = FIFO Address Width

```verilog
// write address counter: binary counter
assign wr_bin_nxt = (wr_en && !wr_ful_reg) ? (wr_bin_reg + 'h1) : wr_bin_reg;

// write pointer counter: gray counter
assign wr_gry_nxt = (wr_bin_nxt >> 1) ^ wr_bin_nxt;

// full condition
assign wr_ful_nxt =  (wr_gry_nxt == {~r2w_rd_ptr[`ADDR_WIDTH:`ADDR_WIDTH-1], r2w_rd_ptr[`ADDR_WIDTH-2:0]});

// write address and write pointer
assign wr_addr = wr_bin_reg[`ADDR_WIDTH-1:0];
assign wr_ptr  = wr_gry_reg;
```

Note: The `wr_gry_nxt` i.e., is the next write pointer in Gray code is compared against the write clock synchronized read pointer `r2w_rd_ptr` in Gray code.

Complete code for the write pointer design can be found [here](https://github.com/24x7fpga/iVerilog/blob/master/design/fifo_asyn/fifo_wr_ptr.v).


#### Read Pointer {#read-pointer}

All the variables discussed in this section are generated within the `read clock domain`, ensuring they are synchronized and operate in alignment with the read-side logic of the asynchronous FIFO.

-   Read Address: An N-bit binary counter is used to generate the address forreading from FIFO when it's not empty and the read enable signal is high.

-   Read Pointer: A binary-to-Gray code converter is implemented to translate the read address from binary to Gray code, allowing for synchronization with the write clock domain.

-   Empty Flag: The empty signal is set when the write pointer is equal to the read pointer (both in Gray code).

<!--listend-->

```verilog
// read address counter: binary counter
assign rd_bin_nxt = (rd_en && !rd_emp_reg) ? (rd_bin_reg + 'h1) : rd_bin_reg;

// read pointer counter: gray counter
assign rd_gry_nxt = (rd_bin_nxt>>1) ^ rd_bin_nxt;

// empty condition
assign rd_emp_nxt = (rd_gry_nxt == w2r_wr_ptr);

// read address and read pointer
assign rd_addr  = rd_bin_reg[`ADDR_WIDTH-1:0];
assign rd_ptr   = rd_gry_reg;
```

Note: The `rd_gry_nxt` i.e., is the next read pointer in Gray code is compared against the read clock synchronized write pointer `w2r_wr_ptr` in Gray code.

Complete code for read pointer design can be found [here](https://github.com/24x7fpga/iVerilog/blob/master/design/fifo_asyn/fifo_rd_ptr.v).


### Asynchronous FIFO Depth Calculation {#asynchronous-fifo-depth-calculation}

When designing a FIFO, knowing the read clock domain's frequency and the write burst length is crucial for calculating the required FIFO depth. Follow these steps to determine the appropriate depth:

1.  Calculate the time to write one data item: Determine how long it takes to write a single data item into the FIFO buffer, based on the write clock speed.

2.  Calculate the total write time for the data burst: Multiply the time per data item by the total number of data items in the burst to find the total time required to write the entire burst.

3.  Determine how many data items can be read during the write duration: Based on the read clock frequency, calculate how many data items can be read from the FIFO during the total write time.

4.  Calculate the minimum FIFO depth: Subtract the number of items read during the write duration from the burst length. The difference gives you the minimum FIFO depth required to ensure no data loss or overflow during operation.


###  {#d41d8c}

An excellent article that explains FIFO depth calculation in detail can be found [here](https://hardwaregeeksblog.wordpress.com/wp-content/uploads/2016/12/fifodepthcalculationmadeeasy2.pdf).


### Reference {#reference}

1.  [Cummings, Clifford E. "Synthesis and scripting techniques for designing multi-asynchronous clock designs." SNUG 2001 (Synopsys Users Group Conference, San Jose, CA, 2001) User Papers. 2001.](https://citeseerx.ist.psu.edu/document?repid=rep1&type=pdf&doi=47fa3a7c83edfeea24029df12d0ae32fd9ed7610)
2.  [Cummings, Clifford E. "Simulation and synthesis techniques for asynchronous FIFO design." SNUG 2002 (Synopsys Users Group Conference, San Jose, CA, 2002) User Papers. Vol. 281. 2002.](http://www.sunburst-design.com/papers/CummingsSNUG2002SJ_FIFO1.pdf)
3.  [C. Cummings, P. Alfke, “Simulation and Synthesis Techniques for Asynchronous FIFO Design with Asynchronous Pointer Comparisons” SNUG, 2002.](http://www.sunburst-design.com/papers/CummingsSNUG2002SJ_FIFO2.pdf)
