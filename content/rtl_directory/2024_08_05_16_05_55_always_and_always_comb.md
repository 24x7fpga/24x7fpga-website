+++
title = "Always@(*) and Always_Comb"
author = ["Kiran"]
date = 2024-08-05T16:05:00-04:00
tags = ["rtl"]
draft = false
css = "../../zcustom.css"
+++

[Design](https://github.com/24x7fpga/iVerilog/blob/master/design/always_at_comb/always_at_comb.sv) -- [Testbench](https://github.com/24x7fpga/iVerilog/blob/master/tb_design/tb_always_at_comb/tb_always_at_comb.sv) -- [RTL Design Directory]({{< relref "2024_06_05_00_21_53_rtl_design_directory.md" >}})


## Always@(\*) and Always_Comb {#always-----and-always-comb}

In digital design, both `always @(*)` in Verilog and `always_comb` in SystemVerilog are commonly used to model combinational logic. While they serve a similar purpose, there are important differences that make `always_comb` the preferred choice in modern design flows. In this article, we'll explore these differences and explain why always_comb offers enhanced safety and robustness over `always @(*)` in SystemVerilog-based designs.


### Always@(\*) {#always}

In Verilog, the `always @(*)` construct was introduced to simplify the sensitivity list in combinational logic. Instead of manually specifying every signal in the sensitivity list, `always @(*)` automatically infers all the signals that are read within the always block. This feature not only saves time but also improves coding efficiency by eliminating potential errors from manually listing signals.


#### Limitation {#limitation}

-   Triggered by sensitivity list changes: The always block only executes when there is a change in the signals that are part of the sensitivity list.
-   Incomplete sensitivity list: The block may not infer all necessary signals, leading to potential simulation mismatches.


### Always_Comb {#always-comb}

Introduced in System Verilog, the functionality of the `always_comb` is the same as the `always@(*)` but it overcomes the limitation of the `always(*)` block.


#### Advantages {#advantages}

-   Automatically executes at zero simulation time: Ensures the combinational block is triggered at the very start of the simulation.
-   Sensitive to changes within the function: Monitors changes in signals and updates the logic accordingly.
-   Prevents concurrent writes: Ensures that no other processes are allowed to write to the same variables, reducing race conditions.
-   Time constraints: Unlike other blocks, always_comb does not allow time-consuming operations (e.g., #delay), maintaining true combinational behavior.


### Points to Remember {#points-to-remember}

1.  Always use `always_comb` to model combinational logic for safer and more reliable code.
