+++
title = "Constraint: Array Randomization"
author = ["Kiran"]
date = 2024-10-15T17:48:00-04:00
tags = ["sv"]
draft = false
css = "../../zcustom.css"
+++

[Source](https://github.com/24x7fpga/SystemVerilog_Verification/blob/main/sv_verification/const_arr_rand/tb_const_arr_rand.sv) -- [SV Verification Directory]({{< relref "2024_06_27_16_53_00_sv_verification_directory.md" >}})

The `unique` keyword ensures that variables or elements within an array have unique values. This can be applied to fixed arrays, associative arrays, dynamic arrays, or queues, making it useful for scenarios where duplication of values is not allowed.


#### Syntax {#syntax}

```verilog
rand int arr[10];
constraint unique_array {unique {arr};} // ensure all the array elements are unique
```


##  {#d41d8c}

`Note:` Vivado simulator does not support the `unique` keyword. Use EDA Playground or other simulator when using the `unique` keyword.


#### Example Code {#example-code}

```verilog
typedef enum {s0, s1, s2, s3, s4 ,s5, s6 ,s7} type_sv;

class arr_ex;

   rand bit [3:0] arr_st[8];
   rand bit [3:0] arr_dy[ ];
   rand bit [3:0] arr_as[type_sv];

   type_sv sv;

   constraint const_arr_st {unique{arr_st};}
   constraint const_arr_dy {unique{arr_dy}; arr_dy.size == 4;}
   constraint const_arr_as {unique{arr_as}; arr_as.size == sv.num;}

   function void disp_st();
      $display("--------- Static Array ------------");
      foreach(arr_st[i])
        $display("arr_st[%0d] = %0d", i, arr_st[i]);
   endfunction // disp_st

   function void disp_dy();
     $display("--------- Dynamic Array ------------");
      foreach(arr_dy[i])
        $display("arr_dy[%0d] = %0d", i, arr_dy[i]);
   endfunction // disp_dy

   function void disp_as();
     $display("------- Associative Array ----------");
      foreach(arr_as[i])
        $display("arr_as[%0s] = %0d", i.name(), arr_as[i]);
   endfunction // disp_as

endclass

module tb_const_arr_rand;

   arr_ex const_arr;

   initial begin
     const_arr = new();
     const_arr.randomize();
     const_arr.disp_st();
     const_arr.disp_dy();
     const_arr.disp_as();
   end

endmodule
```

[Execute the code in EDA Playground](https://www.edaplayground.com/x/BRUs)
