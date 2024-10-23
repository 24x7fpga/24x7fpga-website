+++
title = "Constraint: In-line and External"
author = ["Kiran"]
date = 2024-10-22T16:57:00-04:00
tags = ["sv"]
draft = false
css = "../../zcustom.css"
+++

[Source](https://github.com/24x7fpga/SystemVerilog_Verification/blob/main/sv_verification/const_in_ln_extn/tb_const_in_ln_extn.sv) -- [SV Verification Directory]({{< relref "2024_06_27_16_53_00_sv_verification_directory.md" >}})


## In-Line Constraint {#in-line-constraint}

The `in-line` constraint allows for applying constraints directly within the `randomize()` function call. This makes it easier to define temporary or context-specific constraints without modifying the original constraint block. Additional, `with` keyword must be used along with the add-on constraints while calling `randomize()` function.


### Syntax {#syntax}

```verilog
handle_name.randomize() with { <constraint>;}
```


### Example Code {#example-code}

```verilog
class const_ex;

   rand bit [7:0] a;

   constraint const_1 {a inside {[1:100]};}

   function void disp();
      $display("a = %d", a);
   endfunction // disp

endclass // const_ex

module tb_const_in_ln_extn;

   const_ex in_ln;

   initial begin
      in_ln = new();
      for(int i = 0; i < 16; i++) begin
         in_ln.randomize() with {a >= 25; a <= 75;};
         in_ln.disp();
      end
   end

endmodule
```

[Execute the code in EDA Playground](https://www.edaplayground.com/x/feM8)

`Note`: The `with{}` statement operates within the scope of the class. This means that when accessing variables inside the `with{}` block, they must be referenced as instance variables/property rather than using dot notation.


## External Constraint {#external-constraint}

Constraints need not to be defined directly within the class—just like how function or routine bodies can be defined externally. This flexibility allows for greater modularity and code reuse. The constraint class defined with an empty constraint in one file, and then different versions of that constraints can be created in separate test files. This approach allows each test to generate customized stimuli based on its specific requirements, without changing the original class definition.


### Syntax {#syntax}

```verilog
//declare in the class
extern constraint <constraint_name>;
//declare outside the class
constraint <class_name>::<constraint_name>{<constraints>;}
```


### Example Code {#example-code}

```verilog
class const_ex;

   rand bit [7:0] b;

   function void disp();
     $display("b = %d", b);
   endfunction // disp

   // declare exteranl constraint
   extern constraint extn;

endclass // const_ex

// external constraint outside the class
// it can be placed in a seperate file
constraint const_ex::extn{!(b inside {[25:75]}); b <= 100;}

module tb_const_in_ln_extn;

   const_ex in_ln;

   initial begin
      in_ln = new();
      for(int i = 0; i < 16; i++) begin
        in_ln.randomize();
        in_ln.disp();
      end
   end

endmodule
```

[Execute the code in EDA Playground](https://www.edaplayground.com/x/HZVj)


## Points to Remember {#points-to-remember}

1.  Constraints can be placed in a separate file, making them easy to reuse across multiple tests and maintain consistency.
2.  An external constraint applies to all instances of a class, ensuring global consistency in randomization. In contrast, an in-line constraint only affects the specific call to `randomize()` where it is applied.
3.  External constraints offer a straightforward way to modify a class's behavior without needing advanced Object-Oriented Programming (OOP) techniques.
4.  You can only add new constraints using the external method; existing constraints cannot be modified. It’s essential to declare the external constraint prototype in the original class definition to ensure the constraints are properly linked.
