+++
title = "Constraint: Solve-Before"
author = ["Kiran"]
date = 2024-10-22T13:18:00-04:00
tags = ["sv"]
draft = false
css = "../../zcustom.css"
+++

[Source](https://github.com/24x7fpga/SystemVerilog_Verification/blob/main/sv_verification/const_solve_before/tb_const_solve_before.sv) -- [SV Verification Directory]({{< relref "2024_06_27_16_53_00_sv_verification_directory.md" >}})

As the name implies, the `solve-before` constraint ensures that the specified constraint is resolved prior to any other constraints during randomization. This feature becomes particularly useful when you need to control the order of constraint solving to achieve specific results or meet dependencies within the randomization process for varible ordering

In a typical scenario, the constraint solver assigns equal priority to all constraints, meaning that the order in which constraints are resolved is non-deterministic. However, when you require certain variables to be solved in a specific order, the `solve-before` constraint gives you finer control by changing the probability of occurrences.This allows you to enforce ordering among variables and manage dependencies effectively, especially in complex verification environments where variable relationships influence the final randomized results.


#### Syntax {#syntax}

```verilog
constraint <constraint_name> {solve A before B;}
```


##  {#d41d8c}

`Note`: The `randc` variables are always solved before any other constraints. Therefore, variables cannot be declared as `randc` when using the `solve-before` constraint.


#### Example code {#example-code}

-   From the book [SystemVerilog for Verification](https://link.springer.com/book/10.1007/978-1-4614-0715-7).

<!--listend-->

```verilog
class const_ex;

   rand bit       x;
   rand bit [1:0] y;

   constraint const_1 {
      solve x before y;
      //solve y before x;          // do not apply both solve-before constraint at the same time
      (x == 0) -> (y == 0);
   }

   function void disp();
      $display("x = %d, y = %d", x, y);
   endfunction // disp

endclass // const_ex

module tb_const_solve_before;

   const_ex sb;

   initial begin
      sb = new();
      for(int i = 0; i < 16; i++) begin
         sb.randomize();
         sb.disp();
      end
   end

endmodule
```
