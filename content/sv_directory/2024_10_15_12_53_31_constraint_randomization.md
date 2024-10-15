+++
title = "Constraint Randomization"
author = ["Kiran"]
date = 2024-10-15T12:53:00-04:00
tags = ["sv"]
draft = false
css = "../../zcustom.css"
+++

[SV Verification Directory]({{< relref "2024_06_27_16_53_00_sv_verification_directory.md" >}})

In SystemVerilog, `constraints` are expressions that must be satisfied by the constraint solver during randomization. These constraints guide the solver to generate valid random values that meet the desired conditions.


#### Syntax {#syntax}

```verilog
constraint <constraint_name> {condition 1; condition 2; .... condition N}
```


##  {#d41d8c}

Constraints in SystemVerilog only support 2-state values (no X or Z) and work bidirectionally, meaning the value of one variable can influence others. The solver must find a solution if possible, handling complex equations and expressions with integral types (like bit, logic, integer, or enum), unless the constraints are unsolvable.

A class object can be randomized by calling the `randomize()` method. Additionally, the `pre_randomize()` and `post_randomize()` methods are invoked automatically before and after the `randomize()` call.

-   pre_randomize( ): Use this method to reset variables, prepare states, or initialize variables before the randomization begins.
-   post_randomize( ): This method allows you to make final adjustments or implement display logic after randomization is complete.

These callbacks offer greater control over the randomization process.


### Points to Remember {#points-to-remember}

1.  Randomizing `real` variables is not yet defined in the LRM [2].
2.  The constraint solver is specific to the simulation vendor, and a constraint-random test may not give the same results when run on different simulators, or even different versions of the same tool.
3.  SystemVerilog allows to randomize only integral variables, which includes 2-state and 4-state, though randomization only generates 2-state values.
4.  Cannot randomize strings or refer to th e handle in a constraint.


### Reference {#reference}

1.  [SystemVerilog LRM](https://ieeexplore.ieee.org/document/10458102/keywords#keywords)
2.  [Spear, Chris. SystemVerilog for Verification: A Guide to Learning the Testbench Language Features. Vol. 161. Springer, 2008.](https://3ec1218usm.wordpress.com/wp-content/uploads/2016/12/book_systemverilog_for_verification.pdf)
3.  [Yehia, Ahmed. "The top most common systemverilog constrained random gotchas." Proceedings of 2014 Design and Verification Conference and Exhibition United States (DVCon 2014). 2014.](https://dvcon-proceedings.org/wp-content/uploads/the-top-most-common-systemverilog-constrained-random-gotchas.pdf)
