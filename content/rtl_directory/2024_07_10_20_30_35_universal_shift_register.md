+++
title = "Universal Shift Register"
author = ["Kiran"]
date = 2024-07-10T20:30:00-04:00
tags = ["rtl"]
draft = false
css = "../../zcustom.css"
+++

[Source](https://github.com/24x7fpga/RTL/tree/master/rtl_designs/univ_shift_reg) -- [RTL Design Directory]({{< relref "2024_06_05_00_21_53_rtl_design_directory.md" >}})


## Universal Shift Register: An Overview {#universal-shift-register-an-overview}

A universal shift register is a digital component that performs more than one data operation. It combines the functionalities of several types of shift registers into one, allowing for greater flexibility in data handling.


### Design: 4-bit Universal Shift Register {#design-4-bit-universal-shift-register}

The figure below shows a design of a 4-bit universal shift register. It is an extension of a [Shift Register]({{< relref "2024_07_06_19_54_50_shift_register.md" >}}) which includes a series of [Multiplexer]({{< relref "2024_06_13_12_22_45_multiplexer.md" >}}) which acts as switch to change the functionality of the register.

{{< figure src="/ox-hugo/universal_shift_register.png" >}}

The select input to the MUX acts as the control signal to the universal shift register. The mode of operation is shown in the table below:

| sel[1] | sel[0] | Mode of Operation |
|--------|--------|-------------------|
| 0      | 0      | No operation      |
| 0      | 1      | Shift Right       |
| 1      | 0      | Shift Left        |
| 1      | 1      | Parallel Load     |


#### Verilog Code: 4-bit Universal Shift Register {#verilog-code-4-bit-universal-shift-register}

```verilog
`timescale 1ns/1ns
module univ_shift_reg #(parameter N =4)(/*AUTOARG*/
   // Outputs
   dout,
   // Inputs
   din, sel, clk, rst
   );
   // outputs
   output [N-1:0] dout;
   // inputs
   input [N-1:0]  din;
   input [$clog2(N)-1:0] sel;
   input		 clk;
   input		 rst;

   /*AUTOREG*/
   // Beginning of automatic regs (for this module's undeclared outputs)
   reg [N-1:0]		dout;
   // End of automatics
   /*AUTOWIRE*/


   logic [N-1:0]	 mux_d;

   always_ff@(posedge clk)
     if(rst)
       dout <= 0;
     else
       dout <= mux_d;

   always_comb begin
      case(sel)
        2'h0 : mux_d = dout;
        2'h1 : mux_d = {din[N-1],dout[N-1:1]};
        2'h2 : mux_d = {dout[N-2:0], din[0]};
        2'h3 : mux_d = din;
        default: mux_d = din;
      endcase // case (sel)
   end

endmodule
```
