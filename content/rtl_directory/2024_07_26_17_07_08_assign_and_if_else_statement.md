+++
title = "Assign and If-Else Statement"
author = ["Kiran"]
date = 2024-07-26T17:07:00-04:00
tags = ["rtl"]
draft = false
css = "../../zcustom.css"
+++

[Source](https://github.com/24x7fpga/RTL/tree/master/rtl_designs/assign_vs_if) -- [RTL Design Directory]({{< relref "2024_06_05_00_21_53_rtl_design_directory.md" >}})


## Assign Statement {#assign-statement}

The 'assign' statement is used to model combinational logic by creating continuous assignments. It is typically implemented where the output continuously reflects the changes in the input without any delay. The assign statement is used with signals declared as wire, tri, or other net types, but not with reg types. However, in SystemVerilog, logic type can be used in continuous assignments, similar to wire.


### Syntax {#syntax}

```verilog
assign <wire name> = expression;
```


### Example Code {#example-code}

The assign statement shown in the example code will implement a simple 2-to-1 MUX. The output c is continuously assigned the value of 'a' if sel is high or 'b' if sel is low.

```verilog
module assign_vs_if(/*AUTOARG*/
   // Outputs
   c,
   // Inputs
   a, b, sel
   );

   // outputs
   output       [1:0] c;
   // inputs
   input        [1:0] a, b;
   input	          sel;

   // assign statement
   assign c = sel ? a : b;

endmodule
```


## If-Else Statement {#if-else-statement}

The if-else statement is a basic building block of any programming language. It is used to make decisions based on the condition. In hardware design, if-else statements lead to priority encoding, where the first condition takes the priority over other. The variable being assigned must be of type **reg** when using if-else statement.


### Syntax {#syntax}

```verilog
if(condition)begin
  // execute the block of code if the condition is true
else
  // execute the block of code if the condition is false
end
```


### Example Code {#example-code}

The if statement checks whether sel is high, if sel is high, the output is set to 'a' else to 'b'. The if-else statement implements a simple 2-to-1 MUX.

```verilog
module assign_vs_if(/*AUTOARG*/
   // Outputs
   d,
   // Inputs
   a, b, sel
   );

   // outputs
   output logic [1:0] d;
   // inputs
   input        [1:0] a, b;
   input	          sel;

   // if-else statement
   always_comb
     if(sel)
       d = a;
     else
       d = b;

endmodule
```


## Difference {#difference}

While both the assign statement and the if-else statement implements a MUX during implementing,it's crucial to understand their behavior during simulation. When 'sel' is x or z, the **assign** statement merges a and b to evaluate the output where as in an **if-else** statement, the condition is evaluated to be false, leading to different simulation results.

{{< figure src="/ox-hugo/assign_vs_if_wave.png" >}}
