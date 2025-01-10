+++
title = "Divide by X"
author = ["Kiran"]
date = 2024-12-14T21:47:00-05:00
tags = ["sv"]
draft = false
css = "../../zcustom.css"
+++

[Source](https://github.com/24x7fpga/SV/tree/main/sv_verification/cstrs_challenges/div_by_X) -- [Constraint Challenges]({{< relref "2024_11_12_18_40_22_constraint_challenges.md" >}}) -- [SV Verification Directory]({{< relref "2024_06_27_16_53_00_sv_verification_directory.md" >}})


##  {#d41d8c}


### Problem Statement {#problem-statement}

Create a constraint to generate an `N-bit` random number that is divisible by a given natural number `X`. The value of `X` is parameterized, allowing it to be easily changed for different test scenarios.


### Problem Breakdown {#problem-breakdown}

The key idea is to ensure that the random number generated satisfies the condition below:

\begin{equation}
\label{eq.1}
NUM \quad \\% \quad X == 0
\end{equation}

The modulus operator (%) plays a key role in checking divisibility. To ensure a number is divisible by `X`, the remainder when dividing the number `(NUM)` by `X` must be **zero**.


### Implementation {#implementation}

```verilog
class cstrs_challenge #(int X = 5, N = 4);

   // class properties
   rand bit [N:0] y;

   constraint c {
      y % X == 0;
   }

   // display function
   function void disp();
      $display("Rand Num = %d", y);
   endfunction // disp

endclass // cstrs_challenge
```
