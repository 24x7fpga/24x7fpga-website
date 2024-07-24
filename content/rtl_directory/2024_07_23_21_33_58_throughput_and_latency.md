+++
title = "Throughput and Latency"
author = ["Kiran"]
date = 2024-07-23T21:33:00-04:00
tags = ["rtl"]
draft = false
css = "../../zcustom.css"
+++

## Introduction {#introduction}

Speed in digital design can have three primary meanings/definitions based on the context of the problem. There are as follows:

1.  Throughput: refers to the amount of time a system takes to process the data per clock cycle, measured in bits per second.
2.  Latency: refers to the time between the input and the processed output, measured as clock cycles.
3.  Timing: refers to the logic delays between the sequential elements, the standard metric is clock period or frequency.

Here, the impacts of timing based on architecture designs and optimizations will be discussed.

-   Building architecture designs that can process many bits per second increases the throughput rate.
-   Minimizing the delay from the input to the output builds low-latency architecture.
-   Timing optimizations reduce the combinational delay of the critical path.
    -   Adding registers to divide the combinational logic structure.
    -   Parallel structures for separating sequentially executed operations into parallel operations.
    -   Flattening the logic structures specific to priority encoded signals.
    -   Register balancing to redistribute combinational logic around pipelined registers.
    -   Reordering paths to divert operations from a critical path to a noncritical path.


## High Throughput {#high-throughput}

To achieve maximum throughput, it is necessary to process a maximum number of bits. In digital design, this is achieved through pipeline. An important concept of pipelined design is that of "unrolling the loop". This can be understood with an example given where the cube of the input is calculated.

<a id="code-snippet--cube-using-loop"></a>
```verilog
module power3(
 output [7:0] Xpower,
 output       finished,
 input  [7:0] X,
 input        clk, start
);

reg [7:0] cnt;
reg [7:0] Xpower;

assign finished = (cnt ==0);

always@(posedge clk)begin
 if(start)begin
  Xpower <= X;
  cnt    <= 2;
 end
else if(!finished)begin
  cnt    <= cnt - 1;
  Xpower <= Xpower * X;
 end
end
endmodule
```

In the above example, to calculate the cube number, the same hardware resources are used until the result is computed. This iterative method is similar to software implementation. The performance of this implementation is

-   Throughput = 8/3 ~ 2.7 bits/clock.
-   Latency = 3 clocks.
-   Timing = one multiplier delay in the critical path.

Throughput is ~ 2.7 bits/clock because there are 8 bits and 3 clock cycles to calculate the result. Therefore, in one clock cycle, 2.7 bits are being processed.

In the pipelined design as shown in the code below.

<a id="code-snippet--cube-using-pipeline"></a>
```verilog
module power3(
 output [7:0] Xpower,
 input  [7:0] X,
 input        clk, start
);

reg [7:0] X!, X2;
reg [7:0] Xpower1, Xpower2;

always@(posedge clk)begin
 // pipeline stage 1
 x1      <= X;
 Xpower1 <= X;
 //pipeline stage 2
 X2      <= X1;
 Xpower2 <= Xpower1 * X1;
 // pipeline stage 3
 Xpower  <= Xpower2 * X2;
end
endmodule
```

-   Throughput = 8/1 = 8 bits/clock.
-   Latency = 3 clocks.
-   Timing = one multiplier delay in the critical path.

Here, the throughput performance is increased by a factor of 3.

When an algorithm with an 'n' iterative loop is unrolled, the performance throughput increases by the factor of n. The pipelined structure requires additional hardware resources.


## Low Latency {#low-latency}

A design is considered to have low latency when the time taken for the data to traverse through the input to the output is the least by minimizing the intermediate delays. Low-latency design requires parallelism, removal of pipelining may reduce the throughput or the max clock speed in a design. In the example of the cube of a value, the product of each stage must wait until the next clock edge before it is propagated to the next stage. By removing the pipelined registers, we can minimize the input-to-output timing.

<a id="code-snippet--cube-low-lat"></a>
```verilog
module power3(
 output [7:0] Xpower,
 input  [7:0] X,
 input        clk, start
);

reg [7:0] X!, X2;
reg [7:0] Xpower1, Xpower2;

assign Xpower = Xpower2 * X2;

always@(posedge clk)begin
 // pipeline stage 1
 x1      <= X;
 Xpower1 <= X;
 //pipeline stage 2
 X2      <= X1;
 Xpower2 <= Xpower1 * X1;
end
endmodule
```

-   Throughput = 8 bits/clock (assuming one input per clock)
-   Latency = Between one and two multiplier delays, 0 clocks.
-   Timing = Two multiplier delays in the critical path.

Latency is improved by removing pipelined registers. The penalty for removing pipelined registers is an increase in combinatorial delay between registers.


## Timing {#timing}

Timing refers to the clock speed of a design. The maximum delay between any two sequential elements in a design will determine the maximum clock speed. One cannot know whether a pipelined topology will run faster than an iterative one without knowing the details of the implementation. The maximum speed, or the maximum frequency, can be defined in the equation below ignoring clock-to-clock jitter.


### Maximum Frequency {#maximum-frequency}

```latex
F_{max} = dfrac{1}{T_{clk-q} + T_{logic} + T_{routing} + T_{setup} - T_{skew}}
```
