+++
title = "Repeat and Forever"
author = ["Kiran"]
date = 2024-07-19T14:46:00-04:00
tags = ["sv"]
draft = false
css = "../../zcustom.css"
+++

[SV Verification Directory]({{< relref "2024_06_27_16_53_00_sv_verification_directory.md" >}})


## Repeat and Forever Loops {#repeat-and-forever-loops}

The "repeat" and "forever" loops are another looping constructs available constructs in SystemVerilog, to execute a loop of code for a specific number of times or indefinitely. These loops are especially useful in testbenches and design verification when you need controlled or infinite iterations.


### Repeat {#repeat}

The repeat loop executes a block of code a specified number of times. This loop is handy when you know the exact number of iterations required.


#### Syntax: Repeat Loop {#syntax-repeat-loop}

```verilog
repeat (count) begin
   // execute the code
end
```


#### Example Code: Repeat Loop {#example-code-repeat-loop}

```verilog
module repeat_code;

  initial begin
    int i = 0;
    $display("---------- 3 times table ----------");
    repeat(11)begin
      $display("3 x %0d = %0d", i, i*3);
      i = i + 1;
    end
  end

endmodule
```

[Execute the code in EDA Playground](https://www.edaplayground.com/x/Hvp7)


### Forever Loop {#forever-loop}

The "forever" loop executes a block of code indefinitely until it is interrupted by a disable statement, a break statement, or some external condition like a simulation time limit.


#### Syntax: Forever Loop {#syntax-forever-loop}

```verilog
forever begin
   // execute the code
end
```


#### Example Code: Forever Loop {#example-code-forever-loop}

```verilog
module forever_code;

  initial begin
    int i = 0;
    $display("---------- 3 times table ----------");
    forever begin
      i = i + 1;
      #1;
      $display("3 x %0d = %0d", i, i*3);
      if(i==10)
        break;
    end
  end

endmodule
```

[Execute the code in EDA Playground](https://www.edaplayground.com/x/Hvp7)
