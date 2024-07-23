+++
title = "Frequency Divider"
author = ["Kiran"]
date = 2024-07-22T15:24:00-04:00
tags = ["rtl"]
draft = false
css = "../../zcustom.css"
+++

[Design](https://github.com/24x7fpga/iVerilog/blob/master/design/freq_div/freq_div.v) -- [Testbench](https://github.com/24x7fpga/iVerilog/blob/master/tb_design/tb_freq_div/tb_freq_div.v) -- [RTL Design Directory]({{< relref "2024_06_05_00_21_53_rtl_design_directory.md" >}})


## Frequency Divider: An Overview {#frequency-divider-an-overview}

A frequency divider is a digital circuit that reduces the frequency of the main clock by a certain factor. They are typically used in clock generation circuits, timers, counters, etc. Frequency dividers can be categorized into two even and odd frequency dividers based on the factor N. If N is an even number the frequency divider is categorized under an even frequency divider and when N is an odd number it is categorized as an odd frequency divider.


### Frequency Divider: Even {#frequency-divider-even}


#### N = 2, 4, 8 {#n-2-4-8}

Even frequency dividers divide the input frequency by an even integer, it can be designed using simple counters. Figure shows an example of even frequency dividers where the factor N is 2, 4, 8.

{{< figure src="/ox-hugo/freq_div_2.svg" >}}

The waveform is even frequency dividers is shown below:

{{< figure src="/ox-hugo/freq_div_2_wave.svg" >}}


#### N = 6 {#n-6}

The above-designed frequency dividers are simple to design since the dividing factor is in powers of 2. When the dividing factor is not a power of 2, a mod-N counter should be implemented rather a than simple counter and additional logic should be added to achieve the required frequency. Below is an example of N = 6 frequency divider.

{{< figure src="/ox-hugo/freq_div_6.svg" >}}

The figure below shows the waveform for frequency divided by 6.

{{< figure src="/ox-hugo/freq_div_6_wave.svg" >}}


#### Verilog Code: Even {#verilog-code-even}

```verilog
module freq_div_even #(parameter n = 4) (/*AUTOARG*/
   // Outputs
   div2, div4, div6,
   // Inputs
   clk, rst
   );

   input clk;
   input rst;

   output div2;
   output div4;
   output div6;

   /*AUTOREG*/
   /*AUTOWIRE*/

   reg  [$clog2(n)-1:0] cnt_reg, cnt_reg6;
   wire [$clog2(n)-1:0] cnt_nxt, cnt_nxt6;

   reg			cnt6_q1;

   always@(posedge clk)
     begin
             if(!rst)begin
               cnt_reg  <= 'h0;
               cnt_reg6 <= 'h0;
             end else begin
               cnt_reg  <= cnt_nxt; //
               cnt_reg6 <= cnt_nxt6;
             end
     end // always@ (posedge clk)

   assign cnt_nxt  = cnt_reg + 'h1; // simple counter
   assign cnt_nxt6 = (cnt_reg6 == 5) ? 'h0 : cnt_reg6 + 'h1;

   // frequency divided by 2
   assign div2 = cnt_reg[1];

   // frequency divided by 4
   assign div4 = cnt_reg [2];

   // frequency divided by 6
   always@(posedge clk)
     begin
             cnt6_q1 <= cnt_reg6[1];
     end

   assign div6 = cnt_reg6[2] | cnt6_q1;

endmodule // freq_div_even
```


### Frequency Divider: Odd {#frequency-divider-odd}

When designing frequency divider is odd number, it is necessary to use mod-n counter and additional logic similar to frequency divider where N = 6.


#### Verilog Code: Odd {#verilog-code-odd}

```verilog
module freq_div_odd #(parameter n = 3) (/*AUTOARG*/
   // Outputs
   div3, div5, div7,
   // Inputs
   clk, rst
   );

   input clk;
   input rst;

   output div3;
   output div5;
   output div7;

   /*AUTOREG*/
   /*AUTOWIRE*/

   reg  [$clog2(n)-1:0] cnt_reg3, cnt_reg5, cnt_reg7;
   wire [$clog2(n)-1:0] cnt_nxt3, cnt_nxt5, cnt_nxt7;


   reg 			cnt3_q1, cnt5_q1, cnt7_q2;

   always@(posedge clk)
     begin
             if(!rst)begin
         cnt_reg3 <= 'h0;
               cnt_reg5 <= 'h0;
               cnt_reg7 <= 'h0;
             end else begin
               cnt_reg3 <= cnt_nxt3;
               cnt_reg5 <= cnt_nxt5;
               cnt_reg7 <= cnt_nxt7;
             end
     end

   assign cnt_nxt3 = (cnt_reg3 == 2) ? 'h0 : cnt_reg3 + 'h1;
   assign cnt_nxt5 = (cnt_reg5 == 4) ? 'h0 : cnt_reg5 + 'h1;
   assign cnt_nxt7 = (cnt_reg7 == 6) ? 'h0 : cnt_reg7 + 'h1; // redundant

   // frequency divided by 3
   always@(negedge clk)
     begin
             cnt3_q1 <= cnt_reg3[1];
     end

   assign div3 = cnt_reg3[1] | cnt3_q1;

   //frequency divided by 5
   always@(negedge clk)
     begin
             cnt5_q1 <= cnt_reg5[1];
     end

   assign div5 = cnt_reg5[1] | cnt5_q1;

   //frequency divided by 7
   always@(negedge clk)
     begin
             cnt7_q2 <= cnt_reg7[2];
     end

   assign div7 = cnt_reg7[2] | cnt7_q2;

endmodule // freq_div_odd
```
