+++
title = """
  Difference between "=" and "=="
  """
author = ["Kiran"]
date = 2024-10-07T00:08:00-04:00
tags = ["rtl"]
draft = false
css = "../../zcustom.css"
+++

[Design](https://github.com/24x7fpga/iVerilog/blob/master/design/equals/equals.sv) -- [Testbench](https://github.com/24x7fpga/iVerilog/blob/master/tb_design/tb_equals/tb_equals.sv) -- [RTL Design Directory]({{< relref "2024_06_05_00_21_53_rtl_design_directory.md" >}})

In Verilog, **==** and **===** are both used for comparison, but they behave differently in how they handle X (unknown).


## == Equality {#equality}

The **==** operator checks if two values are logically equal. It used for comparisons on only 1's and 0's. If any operand contains an X, the comparison result will be a unknown value.


### Example: {#example}

```verilog
4'b101X == 4'b1011;  // Result: x (unknown)
4'b1X00 == 4'b1100;  // Result: x (unknown)
```


## **===** Equality {#equality}

The **===**  operator is a strict equality check. It treats X as don't-care values. When both RHS and LHS are X's the results will true (high).


### Example: {#example}

```verilog
4'b101X === 4'b101x;  // Result: true
4'bxxxx === 4'bxxxx;  // Result: true
```

Here, **===**  treats X as a distinct value, so the comparison returns false if the two values are not strictly equal, including the X bits.

NOTE: In synthesizable RTL, it is not advisible to use **===** since it will hide "X" propogations issues.


## Points to Remember {#points-to-remember}

1.  **==** : Ignores X and Z and checks for logical equality.
2.  **===** : Considers X and Z in the comparison and checks for strict equality.
