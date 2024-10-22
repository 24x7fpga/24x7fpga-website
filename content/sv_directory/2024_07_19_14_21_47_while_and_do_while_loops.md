+++
title = "While and Do While Loops"
author = ["Kiran"]
date = 2024-07-19T14:21:00-04:00
tags = ["sv"]
draft = false
css = "../../zcustom.css"
+++

[Source](https://github.com/24x7fpga/SystemVerilog_Verification/tree/main/sv_verification) -- [SV Verification Directory]({{< relref "2024_06_27_16_53_00_sv_verification_directory.md" >}})


## While and Do While {#while-and-do-while}

While and do while loops are used for iterative control structures that execute a block of code multiple times based on a condition. These loops are particularly useful when the number of iterations is not known beforehand and depends on the evaluation of the loop condition.


### While Loop {#while-loop}

The "while" loop executes a block code until the specified condition is true. The condition is evaluated before each iteration.


#### Syntax {#syntax}

```verilog
while (condition) begin
   // execute the code
end
```


#### Example Code: [While Loop](https://github.com/24x7fpga/SystemVerilog_Verification/blob/main/sv_verification/while_loop/tb_while_loop.sv) {#example-code-while-loop}

```verilog
module tb_while_loop;

  initial begin

    int i;

    $display("---------- While Loop ----------");
    while (i<16) begin
      $display("I = %0d", i);
      i = i + 1;
    end

  end

endmodule
```


### Do While Loop {#do-while-loop}

The "do while" loop differs from the while based on the evaluation of the condition. In the "do while" loop the block of code is executed once before the condition is evaluated.


#### Syntax {#syntax}

```verilog
do begin
   // execute the code
end
```


#### Example Code: [Do While](https://github.com/24x7fpga/SystemVerilog_Verification/blob/main/sv_verification/do_while_loop/tb_do_while_loop.sv) {#example-code-do-while}

```verilog
module tb_do_while_loop;

  initial begin

    int i;

    $display("---------- Do While Loop ----------");
    do begin
      $display("I = %0d", i);
      i = i + 1;
    end while (i<16);

  end

endmodule
```
