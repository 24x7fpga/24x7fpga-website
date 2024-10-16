+++
title = "Constraint: Inside Operator"
author = ["Kiran"]
date = 2024-10-15T15:54:00-04:00
tags = ["sv"]
draft = false
css = "../../zcustom.css"
+++

[Source] -- [SV Verification Directory]({{< relref "2024_06_27_16_53_00_sv_verification_directory.md" >}})

The `inside` operator lets you create sets of values from which a variable can randomly be selected. By default, all values are chosen with equal probability, unless other constraints are applied. You can also include variables in these sets to make the randomization more dynamic.


## Example {#example}

```verilog
rand int rn;
constraint set_value {rn inside {1,2,3,4};}   // randomly selects from set of values
constraint set_value {rn inside {10:20};}     // randomly selects values from 10 to 20
constraint set_value {!(rn inside {10:20});}  // randomly selects values outside of the range 10 to 20
```
