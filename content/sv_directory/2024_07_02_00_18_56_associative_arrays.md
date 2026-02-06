+++
title = "Associative Arrays"
author = ["kiran"]
date = 2024-07-02T00:18:00-04:00
tags = ["sv"]
draft = false
css = "../../zcustom.css"
+++

[Source](https://github.com/24x7fpga/SystemVerilog_Verification/blob/main/sv_verification/associative_arrays/tb_associative_arrays.sv) -- [SV Verification Directory]({{< relref "2024_06_27_16_53_00_sv_verification_directory.md" >}})


## Associative Arrays {#associative-arrays}

Associative arrays in SystemVerilog allow you to index arrays with arbitrary data types. An associative array can be stored by the simulator as a tree or a hash table. An associative array is declared with a data type in square brackets. Only the elements that are explicitly assigned are stored, making it memory-efficient for sparse arrays.

Example of associative arrays in SV is shown below:

```verilog
module associative_arrays ();

  byte assoc[byte];

  byte indx = 1;

  int pow_2[int] = '{0:1, 1:2, 2:4, 3:8, 4:16};

  initial begin

    $display("--------------  Associative Array   --------------");
    $display("%p", pow_2);

    do begin
      assoc[indx] = indx;
      indx = indx << 1;
    end while (indx >= 0);

    $display("--------------  Associative Array   --------------");
    foreach(assoc[i]) $display("\t assoc[%0d] = %0d", i, assoc[i]);


    $display("--------------  Associative Array   --------------");
    for(int i=5; i<10; i++)
      pow_2[i] = 1 << i;
    $display("%p", pow_2);

  end
endmodule
```

[Execute the example in EDA Playground](https://www.edaplayground.com/x/RuMW)
