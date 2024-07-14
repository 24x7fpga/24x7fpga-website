+++
title = "Fixed-Size Arrays"
author = ["Kiran"]
date = 2024-06-29T23:25:00-04:00
tags = ["sv"]
draft = false
css = "../../zcustom.css"
+++

[SV Verification Directory]({{< relref "2024_06_27_16_53_00_sv_verification_directory.md" >}})


## Fixed-Size Arrays {#fixed-size-arrays}

An array is a collection of elements stored sequentially. An array in Verilog should be declared with upper and lower bounds. The lower bound in most of the arrays is zero. Fixed-size arrays have constant size and do not change during run time. The array size is set at compile time.


### Single Dimension Array {#single-dimension-array}

There are two ways in SystemVerilog to declare single-dimension arrays which are shown below:

```verilog
int arr1 [15:0];  // 16 elements with index from 0,1,...,15 verbose declaration
int arr2 [16];    // 16 elements with index from 0,1,...,15 compact declaration
```


### Multidimensional Arrays {#multidimensional-arrays}

Multidimensional arrays can be created by specifying the dimensions after the variable name.

```verilog
int array1 [7:0] [0:3];  // verbose declaration
int array2 [8] [4];      // compact declaration
```

The above instruction creates four arrays, each of 8 bits.

SystemVerilog will return a default value for the array element that is read outside of the array bounds. If the array type is of 4-state type, the default value is "X" and if the array is 2-state type the default value is "0". If a net is not driven, then the net value is "Z".


### Array Initialization {#array-initialization}

An array can be initialized using an apostrophe followed by curly braces.

```verilog
array1 = '{4,3,2,1,0};  // assign the array
array2 = '{default:2};  // all the elements in the array are set to 2
```

Fixed-Size Array example is shown below:

```verilog
module tb_arrays();

  int arr1 [3:0];
  int arr2 [2][4];
  int arr3 [2][4][8];

   initial begin

     arr1 = '{4,3,2,1};
     arr2 = '{'{1,2,3,4},'{5,6,7,8}};

     for(int i = 0; i < 2; i = i+1)begin
       for(int j = 0; j < 4; j = j+1)begin
         for(int k = 0; k < 8; k = k+1)begin
           arr3[i][j][k] =  (32 * i) + (8 * j) + k;
         end
       end
     end


     $display("-------------- Array 1 --------------");
     foreach(arr1[i]) $display("\t arr1[%0d] = %0d ", i, arr1[i]);

     $display("-------------- Array 2 --------------");
     foreach(arr2[i,j]) $display("\t arr2[%0d][%0d] = %0d ", i, j, arr2[i][j]);

     $display("-------------- Array 3 --------------");
     foreach(arr3[i,j,k]) $display("\t arr3[%0d][%0d][%0d] = %0d ", i, j, k, arr3[i][j][k]);

   end // initial begin

endmodule
```

[Execute the example in EDA Playground](https://www.edaplayground.com/x/seRT)
