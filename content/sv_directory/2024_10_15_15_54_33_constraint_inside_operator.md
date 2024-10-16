+++
title = "Constraint: Inside Operator"
author = ["Kiran"]
date = 2024-10-15T15:54:00-04:00
tags = ["sv"]
draft = false
css = "../../zcustom.css"
+++

[Source](https://github.com/24x7fpga/SystemVerilog_Verification/blob/main/sv_verification/const_inside_op/tb_const_inside_op.sv) -- [SV Verification Directory]({{< relref "2024_06_27_16_53_00_sv_verification_directory.md" >}})

The `inside` operator lets you create sets of values from which a variable can randomly be selected. By default, all values are chosen with equal probability, unless other constraints are applied. You can also include variables in these sets to make the randomization more dynamic.


#### Syntax {#syntax}

```verilog
rand int rn;
constraint set_value {rn inside {1,2,3,4};}   // randomly selects from set of values
constraint set_value {rn inside {10:20};}     // randomly selects values from 10 to 20
constraint set_value {!(rn inside {10:20});}  // randomly selects values outside of the range 10 to 20
```


##  {#d41d8c}


#### Example Code {#example-code}

```verilog
class inside_op;
   rand bit [3:0] a;
   rand bit [3:0] b;
   rand bit [3:0] c;

   constraint const_a {a inside {1,2,3,4};}
   constraint const_b {b inside {[10:15]};}
   constraint const_c {!(c inside {[10:15]});}

   function void disp();
      $display("a = %0d, b = %0d, c = %0d", a, b, c);
   endfunction // disp

endclass // inside_op

module tb_const_inside_op;

   inside_op i_op;

   initial begin
      i_op = new();
      for(int i = 0; i < 16; i++) begin
         assert(i_op.randomize())
           else
         $error("Randomization Failed ;(");
         i_op.disp();
      end
   end

endmodule
```
