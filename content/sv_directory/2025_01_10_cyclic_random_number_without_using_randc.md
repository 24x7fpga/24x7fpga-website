+++
title = "Cyclic Random Number Without Using Randc"
author = ["Kiran"]
date = 2025-01-10T15:45:00-05:00
tags = ["cstr", "sv"]
draft = false
css = "../../zcustom.css"
+++

[Source](https://github.com/24x7fpga/SV/tree/main/sv_verification/cstrs_challenges/rand_wo_randc) -- [Constraint Challenges]({{< relref "2024_11_12_18_40_22_constraint_challenges.md" >}}) -- [SV Verification Directory]({{< relref "2024_06_27_16_53_00_sv_verification_directory.md" >}})


##  {#d41d8c}


### Problem Statement {#problem-statement}

Generate a sequence of non-repeating random numbers stored in an array. The solution should ensure that each number appears only once and should cover all possible values in the specified range. **Do not** use the `randc` function in SystemVerilog.


#### Randc {#randc}

`Randc` is a built-in command in SystemVerilog designed to generate random variables in a cyclic manner. Unlike standard randomization methods, `randc` ensures that all possible values within the specified range are generated exactly once before any value repeats.


### Problem Breakdown {#problem-breakdown}

-   Create an array to hold all the possible numbers. This array serves as a container for storing numbers.
-   Use a random number generator to produce numbers within the specified range.
-   If the generated random number is **present** in the array, **do not** add it to the array.
-   If the generated random number is **not present** in the array, add it to the array.
-   When the array is full, delete the array and repeat the process.


### Implementation: Version 1 {#implementation-version-1}

In this implementation, a constraint ensures that the generated random number is not already present in the array. To manage the array dynamically, the `post_randomize` function is utilized.

-   Constraint Check: The constraint validates that the newly generated random number is unique and does not exist in the array.
-   Updating the Array: After each successful randomization, the `post_randomize` function updates the array with the new number.
-   Handling Full Arrays: Within the `post_randomize` function, the array is cleared once it is completely full, allowing for a fresh sequence generation if needed.

<!--listend-->

```verilog
class cstrs_challenge1 #(parameter N = 2);

   // class properties
   rand bit [N-1:0] x;
   bit      [N-1:0] q [$];

   constraint c {
      !(x inside {q});
   }

   // post randomization
   function void post_randomize();
      q.push_front(x);
      // if the is == to the number of combinations
      if(q.size() == 2**N)
        q.delete();
   endfunction // post_randomize

   // display function
   function void disp();
      $display("x = %d", x);
   endfunction // disp

endclass // cstrs_challenge1
```


### Implementation: Version 2 {#implementation-version-2}

In the second implementation, the process uses both `pre_randomize` and `post_randomize` functions alongside a constraint to achieve unique random number generation.

-   Index-Based Randomization: Instead of directly randomizing the numbers, the randomized value represents the index of the array.
-   Constraint: A constraint ensures that the randomized index value is within the valid range, i.e., the current size of the array.

How It Works:

-   `pre_randomize` Function:
    -   If the array is empty, this function fills the array with sequential values from 0 to the specified range.
    -   This initializes the array as the source of numbers to be randomized.

-   `post_randomize` Function:
    -   The random index generated during randomization is used to fetch the corresponding value from the array.
    -   After the random number is output, the element at the random index is replaced by the last element in the array.
    -   The array size is then reduced by one, effectively "removing" the used number.

<!--listend-->

```verilog
class cstrs_challenge2 #(int N = 2);

   // class properties
   bit [N-1:0] x;
   rand int  rand_idx;

   bit [N-1:0] q[$];

   constraint c {
      rand_idx >= 0;
      rand_idx < q.size();
   }

   // pre randomization
   function void pre_randomize();
      if(q.size() == 0) begin
         for(int i = 0; i < 2**N; i++) begin
            q.push_back(i);
         end
      end
   endfunction // pre_randomize

   // post randomization
   function void post_randomize();
      x = q[rand_idx];

      q[rand_idx] = q[q.size()-1];

      q.delete(q.size()-1);
   endfunction // post_randomization

   // display function
   function void disp();
      $display("x = %d", x);
   endfunction // disp

endclass // cstrs_challenge2
```
