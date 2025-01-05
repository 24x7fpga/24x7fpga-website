+++
title = "Valid-Ready Protocol"
author = ["Kiran"]
date = 2024-11-29T18:17:00-05:00
tags = ["rtl"]
draft = false
css = "../../zcustom.css"
+++

[Source](https://github.com/24x7fpga/RTL/tree/master/rtl_designs/valid_ready) -- [RTL Design Directory]({{< relref "2024_06_05_00_21_53_rtl_design_directory.md" >}})

The **valid-ready** protocol is a fundamental handshake mechanism in digital design, commonly used in protocols like AXI. It ensures smooth data flow between a producer (the data source) and a consumer (the data sink), enabling reliable and synchronized communication in hardware systems.

{{< figure src="/ox-hugo/valid_ready.svg" caption="<span class=\"figure-number\">Figure 1: </span>Valid-Ready Flow Control" class="center !important" width="600px" >}}


## Guidelines {#guidelines}

The valid and ready signals are integral to many handshaking protocols, ensuring reliable data transfer between components in a digital system. Let’s break down their behavior:

1.  A data transfer occurs **only when** both the `valid` and `ready` signals are high simultaneously. This ensures that the producer has `valid data` to send and the consumer is ready to receive it.

2.  The producer asserts the `valid` signal **high** whenever `valid data` is available for transfer. **Importantly, the valid signal remains high until the transaction is complete**.

3.  The `valid` signal from the producer must be **independent** of the `ready` signal from the consumer. This design ensures that the producer can signal the availability of data without waiting for the consumer's readiness.


## Valid-Ready Slice {#valid-ready-slice}

A valid-ready slice is a pipelined register or buffer that temporarily stores data that ensures that data flow in the data path improving timing and handling backpressure effectively.

A valid-ready slice is a critical component in digital design that enhances the reliability and efficiency of data transfer in a system. Simply put, it is a pipelined register or buffer that temporarily stores data in producer and consumer modules.

{{< figure src="/ox-hugo/valid_ready_slice.svg" caption="<span class=\"figure-number\">Figure 2: </span>Valid-Ready Slice" class="center !important" width="550px" >}}


### Key Benefits of Valid-Ready Slice {#key-benefits-of-valid-ready-slice}

1.  Improved Timing: By introducing a pipelined stage, the valid-ready slice helps break down long combinational paths, ensuring the system meets timing requirements in high-speed designs.

2.  Effective Backpressure Handling: In scenarios where the consumer cannot immediately process incoming data, the valid-ready slice manages the backpressure by regulating the valid and ready signals, preventing data loss or overwrites.

3.  Seamless Data Flow: The slice ensures smooth data transfer between producer and consumer modules, even when they operate at different speeds or in different clock domains.

4.  Pipelined Data Processing: In a processing pipeline, a slice ensures smooth data flow without overwriting or stalling between stages.


## Design Implementation {#design-implementation}

The first guideline is straightforward to implement: the `dwn_data` signal is latched onto `up_data` only when the `dwn_valid` and `up_rdy` signals are both high. This ensures that data transfer occurs only under the right conditions, maintaining synchronization and avoiding potential issues like overwrites or invalid data propagation.

```verilog
always_ff@(posedge clk)begin
   if(dwn_vld & up_rdy)
     up_data <= dwn_data;
end
```

At first glance, the second guideline seems straightforward, but its implementation can be a bit tricky due to interactions with the third guideline. Let’s break it down:

-   The `up_valid` signal must stay asserted (high) until the `up_rdy` signal is asserted by the consumer.
-   On the other hand, the `dwn_valid` signal can be deasserted once the data has been successfully latched onto the slice. This ensures compliance with the third guideline.

<!--listend-->

```verilog
always_ff@(posedge clk)begin
   if(rst)
      up_vld_reg <= 1'b0;
   else begin
      if(dwn_vld & up_rdy)
             up_vld_reg <= 1'b1;
      else if(up_vld & dwn_rdy)
             up_vld_reg <= 1'b0;
   end
end

assign dwn_rdy = up_rdy;
assign up_vld  = up_vld_reg;
```

The design appears to function seamlessly without any issues. **But does it really?** To truly understand its behavior, let’s dive deeper and examine the waveform below.

{{< figure src="/ox-hugo/var1.svg" caption="<span class=\"figure-number\">Figure 3: </span>Valid-ready Version 1" >}}

The waveform illustrates how `dwn_vld` and `up_rdy` signals are randomly asserted and deasserted over time. This randomness introduces an element of unpredictability that challenges the robustness of the design. Let’s analyze the behavior step-by-step and uncover whether the design holds up under these conditions.

-   At point **a**:
    -   The signal `up_rdy` is asserted (set high), indicating that the upstream block is ready to read data.
    -   Data from the slice is read out since `up_vld` and `up_rdy` both are high.

-   At point **c**:
    -   The signal `up_vld` is low, indicating that no valid data is stored in the slice.

-   At point **b**:
    -   The downstream signal `dwn_vld` is asserted (set high), indicating the downstream block a valid data is available.
    -   But the `dwn_rdy` is not set high even though the slice does not hold any valid data.
    -   The implemented logic does not latches data at this point, leading to inefficiency in the handshake mechanism.

-   At point **d**:
    -   The upstream signal `up_rdy` is high again, signaling data is ready to be read out.
    -   Since there is no valid data available, nothing is read out, wasting this opportunity.

-   At point **e**:
    -   A valid handshake finally occurs when both `dwn_vld` and `up_rdy` are high, and valid data is latched into the slice.
    -   This marks the point of successful data transfer but underscores the inefficiency leading up to it.


### Observation {#observation}

-   Inefficient Data Handling: Data failing to latch at **b** when there is no valid data present in the slice reduces the overall efficiency of the valid-ready mechanism.
-   Delayed Latching: The lack of synchronization between `dwn_rdy` and `up_vld` leads to unnecessary toggling and delays.
-   Error-Prone Design: The logic, as currently implemented, introduces inefficiencies that can compound in high-throughput systems as it cabe be seen in the comparison table.


## Updated Valid-Ready Implemenation {#updated-valid-ready-implemenation}

The data fails to latch at **b** because `dwn_rdy` was not set high when there was no valid data stored in the slice. An additional logic must be added in the if statement to latch data when the data is read from the slice.

During the analysis of the valid-ready handshake mechanism in the previous section, an intriguing issue arose. At point **b**, the data failed to latch. **Why?** The culprit was the `dwn_rdy` signal, which was not set high when no valid data was stored in the slice.

This behavior exposes a flaw in the previous logic. The absence of `dwn_rdy` prevents the system from latching data, even though it should. To resolve this, an additional condition must be incorporated into the **if statement** to ensure data is latched whenever it is read from the slice.

```verilog
always_ff@(posedge clk)begin
  if(dwn_vld & (up_rdy | dwn_rdy))
    up_data <= dwn_data;
end
```

This ensure that the data is latch onto the slice when a `dwn_vld` is high and `up_rdy` or `dwn_rdy` is high. The next step is to carefully implement logic for `dwn_rdy`.

To enhance the functionality of the valid-ready handshake mechanism, additional logic has been implemented to ensure that data is reliably latched onto the slice. This happens when `dwn_vld` is high, accompanied by either `up_rdy` or `dwn_rdy` being high.

While this improvement addresses the immediate data latching issue, the next critical step is to carefully design and implement the logic for `dwn_rdy`. This signal plays a pivotal role in ensuring that downstream readiness is accurately conveyed, enabling seamless data flow and improving overall system efficiency.

```verilog
always_ff@(posedge clk, posedge rst)begin
   if(rst)
     up_vld_reg <= 1'b0;
   else begin
      if(dwn_vld & (up_rdy | dwn_rdy))
              up_vld_reg <= 1'b1;
      else if(up_vld & dwn_rdy)
              up_vld_reg <= 1'b0;
    end
end

assign dwn_rdy = up_rdy | ~up_vld_reg;
assign up_vld  = up_vld_reg;
```

Let’s dive deeper into the enhanced implementation of the `dwn_rdy` condition and understand its advantages with a waveform example.

{{< figure src="/ox-hugo/var2.svg" caption="<span class=\"figure-number\">Figure 4: </span>Valid-Ready Version 2" >}}

The `dwn_rdy` signal is asserted high whenever `up_rdy` is high and `up_vld_reg` is low. This ensures the slice is immediately ready to accept new data after the current data is read out from the slice. Additionally, in the **if statement**, the `dwn_rdy` condition is checked to determine whether the slice contains valid data.

This design not only addresses inefficiencies in the earlier implementation but also optimizes data flow through the slice.

-   At point **a**:
    -   The signal `up_rdy` is asserted, indicating that the upstream block is ready to read data.
    -   Since both `up_vld` and `up_rdy` are high, data from the slice is successfully read out.

-   At point **b**:
    -   The signal `dwn_vld` is high, indicating valid data is present at the slice.
    -   With the updated implementation, `dwn_rdy` is also set high, allowing data to be latched onto the slice and made available in the next clock cycle.

-   At point **c**:
    -   This point indicates the arrival of new valid data, as reflected by `up_vld` being set high.

-   At point **d**:
    -   The `up_rdy` signal is high, and valid data is read out.
    -   After the data is read, `up_vld` is set low, but `dwn_rdy` is immediately asserted high, preparing the slice to receive the next set of data.

-   At point **e**:
    -   New data becomes available with `up_vld` set high, demonstrating a seamless transition to the next operation.

These changes not only ensure smoother data transitions but also significantly enhance the overall reliability and performance of the system. By analyzing the waveform, it’s evident how these optimizations contribute to better performance and reliability in handshake logic systems.


## Comparision Table {#comparision-table}

To evaluate the efficiency of the valid-ready handshake protocol, simulations were conducted using Icarus Verilog. In these simulations, random values of dwn_vld and up_rdy were applied to compare the performance of two different design versions.

To quantify the improvements made in the handshake mechanism, a performance comparison was conducted between Version 1 and Version 2. The table below summarizes the results, showing the total number of transactions completed by each version over varying numbers of clock cycles.

| No. of Clock Cycles | Total No. of Transactions in Version 1 | Total No. of Transactions in Version 2 | Difference |
|---------------------|----------------------------------------|----------------------------------------|------------|
| 16                  | 57                                     | 59                                     | 2          |
| 32                  | 63                                     | 66                                     | 3          |
| 64                  | 80                                     | 92                                     | 12         |
| 128                 | 117                                    | 138                                    | 21         |

The results highlight a critical takeaway: over time version 2 leads to significantly improved performance where the total number of transaction in a given time increases, making it the superior choice for systems requiring high transaction throughput and the correct implementation of valid-ready protocol.

While the valid-ready protocol might seem straightforward at first glance, its implementation demands meticulous analysis. If not carefully designed, the protocol can introduce subtle bugs and inefficiencies, potentially compromising system performance.
