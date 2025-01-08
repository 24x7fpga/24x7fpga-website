+++
title = "Latch and Flip-Flop"
author = ["Kiran"]
date = 2024-12-21T13:07:00-05:00
tags = ["rtl", "sta"]
draft = false
css = "../../zcustom.css"
+++

[Source](https://github.com/24x7fpga/RTL/tree/master/rtl_designs/latch_ff) -- [RTL Design Directory]({{< relref "2024_06_05_00_21_53_rtl_design_directory.md" >}})

Latchs and Flip-Flops are fundamental digital components used to store information. Both play a critical role in sequential circuits, but they differ in their operation and applications.


## Latch {#latch}

A **latch** is level-sensitive memory element that stores a single bit of data. It operates based on the state of an enable signal:

-   Transparent Mode: When the enable signal is active, the latch allows data to flow through, making it "transparent."
-   Hold Mode: When the enable signal is inactive, the latch retains its last state, effectively "latching" the data.

Latches are commonly used in designs where immediate data storage or asynchronous operation is required.

```verilog
always@(en, rst, in)begin
   if(rst)
     l_out <= 1'b0;
   else
     if(en)
        l_out <= in;
end
```

The figure below shows the waveform of a latch. When the enable signal is high, the output is directly connected to the input, regardless of the clock. In this case, the clock is included simply to demonstrate that the output does not depend on it. This behavior makes the latch asynchronous, as the output changes immediately in response to the input when the enable signal is active, without waiting for a clock edge.

{{< figure src="/ox-hugo/latch.png" caption="<span class=\"figure-number\">Figure 1: </span>Latch" >}}


## Flip-Flop {#flip-flop}

A **flip-flop** is a edge sensitive memory element that stores a single bit of data. It operates based on edges of the clock signal. A flip-flop is combining two latches in a master-slave configuration.

-   Positive Edge Trigger: The data is captured on the rising edge (from low to high) of the clock signal and will hold the data until the next rising edge.
-   Negative Edge Trigger: The data is captured on the falling edge (from high to low) of the clock signal and will hold the data until the next falling edge.

<!--listend-->

```verilog
// rising edge
always@(posedge clk)begin
   if(rst)
        d_r_out <= 1'b0;
   else
        if(en)
          d_r_out <= in;
end

// falling edge
always@(negedge clk)begin
   if(rst)
        d_f_out <= 1'b0;
   else
        if(en)
          d_f_out <= in;
end
```

The waveform below illustrates the operation of a D flip-flop. The output of the flip-flop only changes on the rising or falling edge of the clock signal. Once the output is updated, it is held constant until the next change in the clock edge, ensuring stable storage of data between clock transitions.

{{< figure src="/ox-hugo/ff.png" caption="<span class=\"figure-number\">Figure 2: </span>D Flip-Flop" >}}


## Applications {#applications}

-   Latches: Used in asynchronous circuits, temporary data storage, or as part of larger flip-flop designs which will be decribed in the subsequent section.
-   Flip-Flops: Widely used in registers, counters, and synchronous sequential circuits where precise timing is essential.
