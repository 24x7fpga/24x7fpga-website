+++
title = "Generate Diagonal Zeros/Ones"
author = ["Kiran"]
date = 2024-11-29T21:53:00-05:00
tags = ["sv"]
draft = false
css = "../../zcustom.css"
+++

[Source](https://github.com/24x7fpga/SystemVerilog_Verification/tree/main/sv_verification/cstrs_challenges/diag_zero) -- [Constraint Challenges]({{< relref "2024_11_12_18_40_22_constraint_challenges.md" >}}) -- [SV Verification Directory]({{< relref "2024_06_27_16_53_00_sv_verification_directory.md" >}})


## Problem Statement {#problem-statement}

Write a constraint to generate an N×N matrix where diagonal elements are set to zeros (or ones), while the rest of the matrix can be filled with either random values or a predefined value.


### Problem Breakdown {#problem-breakdown}

To make the solution more manageable, we can decompose the constraint into smaller, focused sub-constraints:

1.  Generate an N×N Matrix: Define a rand array to hold the matrix values.

2.  Set the Right Diagonal to Zero: Constrain the elements where the row index (i) equals the column index (j), i=j to be zero.

3.  Set the Left Diagonal to Zero: Constrain the elements where the row index (i) and column index (j) satisfy i+j=N−1 to be zero.

4.  Set the Rest of the Matrix: Allow the remaining elements (non-diagonal elements) to be randomized or assigned specific values.


### Implementation {#implementation}

```verilog
`define N 6
class cstrs_challenge;

   // class properties
   rand bit [3:0] arr[`N][`N];

   // constraint logic
   constraint rt_diag  {foreach(arr[i,j]) i == j                       -> arr[i][j] == 0;}    // right diagonal set to zero
   constraint lt_diag  {foreach(arr[i,j]) i+j == `N-1                  -> arr[i][j] == 0;}    // left diagonal set to zero
   constraint non_zero {foreach(arr[i,j]) !((i == j) || (i+j == `N-1)) -> arr[i][j] == 1;}    // rest of the values set to 1

   // display function
   function void disp();
      foreach(arr[i])begin
         foreach(arr[j])begin
            if(j == `N-1)
              $display("%d", arr[i][j]);
            else
              $write("%d", arr[i][j]);
         end
      end
   endfunction // disp

endclass // cstrs_challenge
```
