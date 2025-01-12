+++
title = "D Flip-Flop: Operation"
author = ["Kiran"]
date = 2024-12-21T17:41:00-05:00
tags = ["rtl", "sta"]
draft = false
css = "../../zcustom.css"
+++

[Source](https://github.com/24x7fpga/RTL/tree/master/rtl_designs/d_ff_en) -- [RTL Design Directory]({{< relref "2024_06_05_00_21_53_rtl_design_directory.md" >}})


##  {#d41d8c}

A **flip-flop**, also known as a register, is a fundamental building block in digital circuits used to store a single bit of data. The stored data remains stable until a new input is latched, triggered by an external clock signal. Depending on the design, the data is captured on either the rising edge or falling edge of the clock, making flip-flops essential for synchronizing data in sequential logic circuits.

{{< figure src="/ox-hugo/d_ff.svg" caption="<span class=\"figure-number\">Figure 1: </span>D Flip-Flop" class="center !important" width="400px" >}}

The truth table of a simple D-FF is shown below:

| CLK                 | D | Q | Q' | Description     |
|---------------------|---|---|----|-----------------|
| LOW  (falling edge) | X | Q | Q' | No Change       |
| HIGH (rising edge)  | 0 | 0 | 1  | Reset Q -&gt; 0 |
| HIGH (rising edge)  | 1 | 1 | 0  | Set Q -&gt; 1   |

The D-FF behavior is controlled by a clock signal. When the clock is **low**, the output remains unchanged, regardless of the input. The flip-flop simply holds or stores its previous value. When the clock is **high**, the flip-flop becomes active. On the rising edge of the clock, the output follows the input, capturing its value in the next clock cycle.

{{< figure src="/ox-hugo/ff_wave.svg" caption="<span class=\"figure-number\">Figure 2: </span>D-FF Waveform" >}}


### Transmission Gate {#transmission-gate}

Before diving into the internal structure of the D flip-flop, let's take a moment to revisit the concept of the transmission gate.

A transmission gate is a fundamental building block in digital design, often used for constructing latches and flip-flops. It acts as an electronic switch that allows or blocks the flow of signals based on a control input. Understanding how a transmission gate works will provide valuable insight into the inner workings of a D flip-flop.

{{< figure src="/ox-hugo/trans_gate.svg" caption="<span class=\"figure-number\">Figure 3: </span>Transmission Gate" class="center !important" >}}

The table below summarizes the swithcing behavior of the PMOS and NMOS transistors. This interplay determines whether the transmission gate is turned on (conducting) or off (blocking):

| Gate Input | PMOS | NMOS |
|------------|------|------|
| 0          | ON   | OFF  |
| 1          | OFF  | ON   |

By utilizing the complementary behavior of PMOS and NMOS transistors, the transmission gate ensurese efficient signal transfer while minimizing distortion and power loss.

The truth table below illustrates the functionality of a transmission gate.

-   When the `control` signal is set to **high**, the input signal `A` is passed directly to the output `B`, enabling signal transmission.
-   Conversely, when the control signal is set to **low**, the gate is open, and the output remains in a high-impedance `Z` state, effectively disconnecting the input and output.

| Control | A | B |
|---------|---|---|
| 1       | 0 | 0 |
| 1       | 1 | 1 |
| 0       | 0 | Z |
| 0       | 1 | Z |

With this foundation, let’s now explore the internal structure of the D flip-flop in detail.


### Operation {#operation}

To fully grasp the operation of a D flip-flop, it's helpful to examine its internal structure. At its core, the D flip-flop consists of two latches connected in series: a master latch and a slave latch.

As shown in Figure 3, the master latch is active during one phase of the clock cycle, while the slave latch operates during the opposite phase. This configuration ensures that data is reliably captured and transferred at the clock's rising or falling edge, depending on the design.

{{< figure src="/ox-hugo/ff.svg" caption="<span class=\"figure-number\">Figure 4: </span>D Flip-Flop Internal Structure" >}}

The master-slave arrangement is the key to the D flip-flop’s edge-triggered behavior, making it a vital component in digital systems for storing and synchronizing data. The configuration shown in Figure 4 consists of a negative D latch (active when the clock is low) as the master, cascaded with a positive D latch (active when the clock is high) as the slave. Together, these latches operate in sequence to form a positive edge-triggered D flip-flop ensureing the data is captured only on the rising edge of the clock.

When working with transmission gates, it is crucial to **carefully** consider the clock `connection` and its `polarity` relative to the control signal. The correct configuration ensures that the transmission gate operates as intended, enabling or disabling signal flow at precise moments within the clock cycle. A misconfigured clock or polarity mismatch can lead to incorrect behavior.


#### CLOCK LOW {#clock-low}

Figure 5 illustrates the internal structure of a D Flip-Flop when the clock signal is **low**.

-   Master D Latch Operation: When the clock signal is low, the master D latch captures the input data. The data flows sequentially through the internal points labeled A, B, C, and D, ensuring the latch is updated with the input value.

-   Slave D Latch Operation: During this time, the slave D latch holds the previous value, as its internal points W, X, Y, and Z remain disconnected from the new data. This mechanism prevents the output from changing until the clock signal transitions.

{{< figure src="/ox-hugo/ff_animate_low.svg" caption="<span class=\"figure-number\">Figure 5: </span>Clock is LOW" >}}


#### CLOCK HIGH {#clock-high}

Figure 6 illustrates the internal structure of a D Flip-Flop when the clock signal is **high**.

-   Master D Latch Operation: When the clock signal transitions to high, the master D latch holds the data captured during the previous low clock phase. From the diagram, the data follows the path A → B → C → D, where points A and D are connected to ensure the data is securely stored.

-   Slave D Latch Operation: At this stage, the slave D latch becomes active, and the data path extends from points B → W → X. The output of the flip-flop is sourced directly from the master D latch, allowing the stored data to be propagated. During the clock low phase this data will be stored in the slave latch.

{{< figure src="/ox-hugo/ff_animate_high.svg" caption="<span class=\"figure-number\">Figure 6: </span>Clock is HIGH" >}}
