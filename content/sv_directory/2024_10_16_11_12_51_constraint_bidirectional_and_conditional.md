+++
title = "Constraint: Bidirectional and Conditional"
author = ["Kiran"]
date = 2024-10-16T11:12:00-04:00
tags = ["sv"]
draft = false
css = "../../zcustom.css"
+++

[Source](https://github.com/24x7fpga/SystemVerilog_Verification/blob/main/sv_verification/const_bidir_cond/tb_const_bidir_cond.sv) -- [SV Verification Directory]({{< relref "2024_06_27_16_53_00_sv_verification_directory.md" >}})

The code inside a `constraint` block does not execute sequentially. This allows multiple conditions to be declared within the same block without any specific order and all the conditions will be checked. One key feature of SystemVerilog constraints is their bidirectional nature—the solver evaluates both sides of an expression to satisfy the constraints. Moreover, conditional statements such as `if-else` are supported within constraint blocks. These statements enable expressions to become active or inactive based on specific conditions, providing flexibility in complex scenarios.


##  {#d41d8c}


#### Example Code {#example-code}

```verilog
class const_ex;

   rand bit       a;
   rand bit [3:0] b;
   rand bit [3:0] c;

   constraint cond_1 {b > 1; b < 9; if(a) c == b; else c != b;}

   function void disp();
      $display("a = %0d, b = %0d, c = %0d", a, b, c);
   endfunction // disp

endclass // const_ex

module tb_const_bidir_cond;

   const_ex bi_con;

   initial begin
      bi_con = new();
      for(int i = 0; i < 16; i++) begin
         bi_con.randomize();
         bi_con.disp();
      end
   end

endmodule
```
