+++
title = "Constraint: Weighted Distribution"
author = ["Kiran"]
date = 2024-10-22T14:42:00-04:00
tags = ["sv"]
draft = false
css = "../../zcustom.css"
+++

[Source](https://github.com/24x7fpga/SystemVerilog_Verification/blob/main/sv_verification/const_dist/tb_const_dist.sv) -- [SV Verification Directory]({{< relref "2024_06_27_16_53_00_sv_verification_directory.md" >}})

The weighted distribution constraint is used with the `dist` operator to control the probability of specific values being generated during randomization. This is useful when certain values have to appear more or less frequently in a set of random outcomes.


#### Syntax {#syntax}

The `dist` keyword is utilized to generated weighted distribution during randomization.

```verilog
constraint <constraint_name> {varible_name dist {1 := 4, [2:5] := 8};}   // value := weight
constraint <constraint_name> {varible_name dist {1 :/ 4, [2:5] :/ 8};}   // value :/ weight
```

-   `:=`: This operator specifies that the weight is the same for every value within the specified range.
-   `:/`: This operator ensures that the weight is equally divided among all values in the range.

In the above example:

-   First constraint: The probability of generating the values 1, 2, 3, 4, and 5 will follow a weighted ratio of 4 : 8 : 8 : 8 : 8, respectively.
-   Second constraint: The probability for the same values will follow a weighted ratio of 4 : 8/4 : 8/4 : 8/4 : 8/4, which simplifies to 4 : 2 : 2 : 2 : 2.

For a single value, both the `:=` and `:/=` operators behave the same, as the specified weight is directly applied. However, the difference becomes evident when applied to a range of values. The probability of a value in a range when using `:/` operator is calculated as:

\begin{equation}
\label{eq.1}
Probability = \frac{Specified \\;  weight}{Total \\; number \\; of\\; values}
\end{equation}


##  {#d41d8c}

`Note`: The `dist` operation cannot be applied to `randc` variables. Additionally, the distribution expression must contain at least one rand variable to be valid.


#### Example Code {#example-code}

```verilog
class const_ex;

   rand bit [2:0] a;
   rand bit [2:0] b;

   constraint const_1 {a dist{1:=4,[2:5]:=8};}
   constraint const_2 {b dist{1:/4,[2:5]:/8};}

   function void disp;
      $display("a = %d, b = %d", a, b);
   endfunction // disp

endclass // const_ex

module tb_const_dist;

   const_ex dist_ex;
   bit [3:0] a1,a2,b1,b2 = 0;

   initial begin
      dist_ex = new();
      for(int i = 0; i < 16; i++) begin
         dist_ex.randomize();
         dist_ex.disp;
         // count no. of 1s
         if(dist_ex.a == 1)
           a1 = a1+1;
         else
           a2 = a2+1;
         // count no. of 1s
         if(dist_ex.b == 1)
           b1 = b1+1;
         else
           b2 = b2+1;
      end
      $display("Occurrence of    1  in a is %d\n", a1,
               "Occurrence of [2:5] in a is %d\n", a2,
               "Occurrence of     1 in b is %d\n", b1,
               "Occurrence of [2:5] in b is %d"  , b2);
   end

endmodule // tb_const_dist
```
