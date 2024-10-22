+++
title = "Constraint: Implication and Equivalence"
author = ["Kiran"]
date = 2024-10-16T13:50:00-04:00
tags = ["sv"]
draft = false
css = "../../zcustom.css"
+++

[Source](https://github.com/24x7fpga/SystemVerilog_Verification/blob/main/sv_verification/const_imply/tb_const_imply.sv) -- [SV Verification Directory]({{< relref "2024_06_27_16_53_00_sv_verification_directory.md" >}})


### Implication Operator {#implication-operator}

The implication operator `->` is another form of conditional `(if)` constraint. Where the antecedent (condition A) is on left hand side of the `->` and consequent (condition B) is on the right hand side of the `->` operator.


##  {#d41d8c}


#### Syntax {#syntax}

```verilog
constraint <constrain name> {A -> B;}
```

The expression A -&gt; B is equivalent to the expression (!A || B).

-   If `A` is true, then `B` must also hold true.
-   If `A` is false, then `B` is not constraint according to the implication.

    The truth table below shows the logical value of A and B.

| A -&gt; B | B = false | B = true |
|-----------|-----------|----------|
| A = false | true      | true     |
| A = true  | false     | true     |

`Note:` The implication operator is partly bidirectional constraint, meaning A-&gt;B does not imply that B-&gt;A.


### Equivalence Operator {#equivalence-operator}

The equivalence operator `<->` is bidirectional where A &lt;-&gt; B is defined as ((A -&gt; B) &amp;&amp; (B-&gt;A)). This operator is same as logical XNOR. The truth table below shows the logical values of A and B.

| A &lt;-&gt; B | B = false | B = true |
|---------------|-----------|----------|
| A = false     | true      | false    |
| A = true      | false     | true     |


#### Syntax {#syntax}

```verilog
constraint <constraint_name> {A <-> B;}
```


#### Example Code {#example-code}

```verilog
class const_ex;

   rand bit       a;
   rand bit [3:0] b;
   rand bit [3:0] c;
   rand bit	  d;
   rand bit [3:0] e;
   rand bit [3:0] f;

   // -> similar to if-else
   constraint cond_1 {b > 1; b < 9; (a == 1)  -> (c == b);}
   // equivalence operator is similar to xnor (not supported in Vivado)
   constraint cond_2 {f > 1; f < 9; (d == 1) <-> (e == f);}

   function void disp();
      $display("a = %d, b = %d, c = %d :::: d = %d, e = %d, f = %d", a, b, c, d, e ,f);
   endfunction // disp

endclass // const_ex

module tb_const_imply;

   const_ex imp;

   initial begin
      imp = new();
      for(int i = 0; i < 10; i ++) begin
         imp.randomize();
         imp.disp();
      end
   end

endmodule
```

[Execute the code in EDA Playground](https://www.edaplayground.com/x/gHyr)
