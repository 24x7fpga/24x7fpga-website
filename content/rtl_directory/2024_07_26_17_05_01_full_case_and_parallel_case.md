+++
title = "Full Case and Parallel Case"
author = ["Kiran"]
date = 2024-07-26T17:05:00-04:00
tags = ["rtl"]
draft = false
css = "../../zcustom.css"
+++

[Design] -- [Testbench] -- [RTL Design Directory]({{< relref "2024_06_05_00_21_53_rtl_design_directory.md" >}})


## Full Case and Parallel Case {#full-case-and-parallel-case}

The _case_ statement is a fundamental construct used for conditional branching across various programming languages. In SystemVerilog, it is implemented as a simple if-else-if statement. SystemVerilog introduces two specialized variations of the case statement, casex and casez, that allow for handling "don't care" conditions in the case items, discussed in [Casex and Casez]({{< relref "2024_07_25_11_36_52_casex_and_casez.md" >}}).

The case statement can be caterogired as **full** and **parallel** based on the how the case items are being matched to case expression.


### Full Case {#full-case}

A **full** case statement is where every possible value of the case expression is matched to a case item or to a default case statement. Not having a default statement will not result in a full case statement. When the case item does not have a matching case expression the output will hold the last assigned value and the synthesis will infer a latch.

From an HDL simulation perspective, a "full" case statement is a case statement in which every
possible binary, non-binary and mixture of binary and non-binary patterns is included as a case
item in the case statement [1].

From a synthesis tool perspective, a "full" case statement is a case statement in which every
possible binary pattern is included as a case item in the case statement [1].

-   Full Case removes latches from the design.
-   Number of case item is directly proportional to the number of case expression.
-   The **full_case** directive can be used to explicitly indicate that the case statement is full.


### Parallel Case {#parallel-case}

A case statement checks the case expression against multiple cases and executes the corresponding branch that matches the case expression. By default, the synthesis tools generate a priority encoder, which checks cases sequentially. The **parallel** case tells the synthesis tool that each case item has only one matching case expression allowing the synthesis tool to optimize the hardware by removing priority checks, leading to simpler and faster hardware implementation.

-   Parallel Case removes large, slow-priority encoders.
-   The **parallel_case** directive is used to explicitly indicate that the case statement is parallel.


### Points to Remember {#points-to-remember}

1.  Non-Parallel Case: The order of the case branches is significant, as the conditions are evaluated sequentially, and the first matching condition is selected for execution.

2.  Parallel Case: The order of the case branches is irrelevant, as the synthesis tool assumes that the conditions are mutually exclusive, allowing for simultaneous evaluation without prioritization.


### Reference {#reference}

1.  ["full_case parallel_case", the Evil Twins of Verilog Synthesis](http://www.sunburst-design.com/papers/CummingsSNUG1999Boston_FullParallelCase_rev1_1.pdf)
2.  [Cummings, Clifford. "SystemVerilog’s priority &amp; Unique–A Solution to Verilog’s full_case &amp; parallel_case Evil Twins." SNUG. Israel 282282 (2005).](http://www.sunburst-design.com/papers/CummingsSNUG2005Israel_SystemVerilog_UniquePriority.pdf)
