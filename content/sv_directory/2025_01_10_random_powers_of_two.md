+++
title = "Random Powers of Two"
author = ["Kiran"]
date = 2025-01-10T19:30:00-05:00
tags = ["cstr", "sv"]
draft = false
css = "../../zcustom.css"
+++

[Source](https://github.com/24x7fpga/SV/tree/main/sv_verification/cstrs_challenges/pwr_two) -- [Constraint Challenges]({{< relref "2024_11_12_18_40_22_constraint_challenges.md" >}}) -- [SV Verification Directory]({{< relref "2024_06_27_16_53_00_sv_verification_directory.md" >}})


##  {#d41d8c}


### Problem Statement {#problem-statement}

Write a SystemVerilog constraint to generate random numbers that are powers of two. The generated numbers should only include values such as 2, 4, 8, 16, 32, and so on, up to a specified maximum limit. Ensure the constraint restricts randomization to these valid values only and **do not** use the multiplication operator.


### Problem Breakdown {#problem-breakdown}

Create an array to hold the numbers that are powers of two. Generate a random number that is greater than 1 and less than the specified range. Write a constraint to check if the number is power of two.

-   Create an Array of Powers of Two: First, define an array to hold all numbers that are powers of two within the specified range. For example, if the range is up to 64, the array will include {1,2,4,8,16,32,64}.
-   Generate a Random Number: Randomize a number within the range greater than 1 and less than the specified upper limit.
-   Constraint to Validate: Write a constraint to ensure the generated number is a power of two. If the number is a power of two add it to the array if not continue generating random numbers.


### Implementation: Version 1 {#implementation-version-1}

In SystemVerilog, the built-in function called `$onehot` whih is used to check if a given vector consists of only one set bit. Write a constraint that checks whether the generated random number has only one set bit in the vector. If its true add the number in the array in `post_randomize` function.

In SystemVerilog, the built-in function `$onehot` is used to determine if a given vector has exactly one bit set to 1. This functionality is highly useful when generating random numbers that need to follow a one-hot encoding pattern.

-   Constraint for One-Hot Validation: Use the `$onehot` function in a constraint to check if the generated random number is one-hot encoded (i.e., it has only one bit set to 1).

-   Store Valid Numbers in an Array: After randomization, use the `post_randomize` function to store the number in array. The constraint will ensure the generated random number is a power of two.

<!--listend-->

```verilog
 class cstrs_challenge1 #(parameter N = 32, R = 100);

   // class properties
   rand bit [N-1:0] num;
   bit [N-1:0]	    arr[$];

   // constraints
   constraint c1 {num > 1; num <= R; !(num inside {arr});}
   constraint c2 {$onehot(num);}

   // post randomize
   function void post_randomize();
     arr.push_back(num);
   endfunction // post_randomize

   // display function
   function void disp();
      foreach(arr[i])
        $display("arr[%d] = %d", i, arr[i]);
   endfunction // disp

endclass // cstrs_challenge1
```


### Implementation: Version 2 {#implementation-version-2}

In the second implementation, a mathematical observation is leveraged to efficiently generate random numbers that are powers of two. Let’s break it down with an example:

Consider a number that is a power of two, such as 8 = 8′b1000:

-   Observation: For a number that is a power of two, only one bit is set to 1 in its binary representation.

-   Subtraction Trick: Subtracting 1 from a power-of-two number flips all bits below the set bit to 1. For example, 8−1=7=8′b01118−1=7=8′b0111.

-   Bitwise AND: Performing a bitwise AND operation between the original number and the result of the subtraction gives 0, 8 &amp; 7 = 8′b1000 &amp; 8′b0111 = 8′b0000.

<!--listend-->

```verilog
class cstrs_challenge2 #(parameter N = 32, R = 100);

   // class properties
   rand bit [N-1:0] num;
   bit [N-1:0]	    arr[$];

   // constraint
   constraint c1 {num > 1; num <= R; !(num inside {arr});}
   constraint c2 {(num & (num-1)) == 0;}

   // post randomize
   function void post_randomize();
     arr.push_back(num);
   endfunction // post_randomize

   // display function
   function void disp();
      foreach(arr[i])
        $display("arr[%d] = %d", i, arr[i]);
   endfunction // disp

endclass // cstrs_challenge2
```


### Implementation: Version 3 {#implementation-version-3}

In the third implementation, a simple yet effective approach is used to generate powers of two. Instead of directly randomizing the number, we randomize the bit position and set it high while keeping all other bits as 0.

-   Randomize the Bit Position: Generate a random number within the range of the bit width. This number represents the position of the bit to be set.

-   Set the Bit High: The left shift operator (&lt;&lt;) is used to set a specific bit high in a the vector based on a randomly generated bit position.

<!--listend-->

```verilog
class cstrs_challenge3 #(parameter N = 32, R = 100);

   // class properties
   rand bit [N-1:0] num;
   bit [N-1:0]	    arr[$];

   // constraint
   constraint c1 {num > 1; num <= $clog2(R);}

   // post randomize
   function void post_randomize();
      arr.push_back(1 << num);
   endfunction // post_randomize

   // display function
   function void disp();
      foreach(arr[i])
        $display("arr[%d] = %d", i, arr[i]);
   endfunction // disp

endclass // cstrs_challenge3
```
