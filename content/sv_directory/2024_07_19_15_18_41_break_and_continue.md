+++
title = "Break and Continue"
author = ["Kiran"]
date = 2024-07-19T15:18:00-04:00
tags = ["sv"]
draft = false
css = "../../zcustom.css"
+++

[SV Verification Directory]({{< relref "2024_06_27_16_53_00_sv_verification_directory.md" >}})


## Break and Continue {#break-and-continue}

Break and continue in SytemVerilog is used to control the loops. When a "break" statement is encounted, the loop is terminated and control moves to outside of the loop. Whereas, when a "continue" statement is encounted, the consecutive blocks of code is skipped and the control is moved to the next loop iteration value.


#### Example Code: Break and Continue {#example-code-break-and-continue}

```verilog
module break_and_continue;

  initial begin
    int i = 0;
    $display("---------- Even Number ----------");
    forever begin
      if(i % 2 == 0) begin
        $display("%0d is an Even Number", i);
        i = i + 1;
        continue;
      end else begin
        if(i > 20)
          break;
      end
      i = i + 1;
      #1;
    end
  end

endmodule
```

[Execute the code in EDA Playground](https://www.edaplayground.com/x/JTaV)
