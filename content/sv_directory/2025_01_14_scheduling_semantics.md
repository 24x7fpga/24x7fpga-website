+++
title = "Scheduling Semantics"
author = ["Kiran"]
date = 2025-01-14T17:25:00-05:00
tags = ["sv"]
draft = false
css = "../../zcustom.css"
+++

[Source](https://github.com/24x7fpga/RTL/tree/master/rtl_designs/race_condition) -- [SV Verification Directory]({{< relref "2024_06_27_16_53_00_sv_verification_directory.md" >}})


##  {#d41d8c}

In digital design, it's crucial to ensure that simulations accurately reflect real-time hardware behavior. Interestingly, there are instances where a design performs flawlessly on hardware but fails during simulation. One common culprit behind such discrepancies is a race condition—an issue that arises due to timing mismatches between design modules and the verification environment. Effectively identifying and resolving race conditions is critical, but it is even more important to strive for and master designs that are inherently free of race conditions.


### Race Condition {#race-condition}

In SystemVerilog, processes or threads run concurrently, with each thread acting as an independent block of code. This concurrent execution is a powerful feature, but it demands careful attention to ensure that the order of execution remains predictable. Without proper control, this can lead to race conditions, where non-deterministic behavior makes debugging and design verification both challenging and error-prone.


#### Race Condition: Example {#race-condition-example}

To demonstrate an example of a race condition, a simple N-bit adder is implemented. The adder's inputs are sampled only when `arg_vld` is high, and the result is valid only when `res_vld` is high. The complete design example can be found [here](https://github.com/24x7fpga/RTL/blob/master/rtl_designs/race_condition/interface.sv). The following code snippet highlights a section of the design that can cause simulation discrepancies.

```verilog
always@(posedge clk)begin
   if(res_vld)begin
      if(queue.size() == 0)begin
         $error("Failure: Queue Empty ;(");
         $finish;
      end else begin
         exp_res <= queue.pop_front(); // Updated in NBA :: Race Condition
         //   exp_res  = queue.pop_front(); // Updated in Active :: Correct implementation
         if(exp_res == res)
           $display("PASS :: Exp_Result = %d and Result = %d", exp_res, res);
         else begin
            $error("FAIL :: Exp_Result = %d and Result = %d", exp_res, res);
            $finish;
         end
      end // else: !if(queue.size() == 0)
   end // if (res_vld)
end
```

From the discussion on [Blocking and Non-Blocking Assignment]({{< relref "2024_07_26_17_10_23_blocking_and_non_blocking_assignment.md" >}}), it’s clear that a common rule of thumb is to use non-blocking assignments `<=` for sequential logic and blocking assignments `=` for combinational logic. However, blindly following this rule can sometimes lead to unexpected errors, as seen in the example code above. Understanding when and how to apply these assignments correctly is key to avoiding such issues. Figure 1 illustrates the simulation failure.

{{< figure src="/ox-hugo/result.png" caption="<span class=\"figure-number\">Figure 1: </span>Simulation Failure" >}}

The simulation waveform is examined to understand where the failure occurs. The waveform is shown in Figure 2. At time 24ns, the `arg_vld` signal is set high indicating the inputs are valid. The input `a` and `b` are zero, therefore, the result should be zero in the next clock cycle.

The simulation waveform is analyzed to identify the point of failure, as shown in Figure 2. At 24 ns, the `arg_vld` signal is set high, indicating that the inputs are valid. The input values, `a` and `b`, are both zero; therefore, the expected result in the next clock cycle should also be zero.

{{< figure src="/ox-hugo/sim.png" caption="<span class=\"figure-number\">Figure 2: </span>Simulation Waveform Outputs Correct Waveform" >}}

From Figure 2, at 32 ns, the `res_vld` signal is high, indicating that the output is valid. The result value is zero, which aligns with the given inputs. However, as shown in Figure 1, the simulation fails when evaluating an expression to verify the design's functionality.

While waveforms are useful for verifying smaller designs, they become impractical for larger designs comprising multiple components and IPs. Verifying such complex designs solely through waveforms is extremely challenging.

In this case, the simulation fails due to the following main reason:

-   The expression **exp_res &lt;= queue.pop_front()** is evaluated at a different time than the conditional statement **exp_res == res**, leading to a race condition.

To address and resolve challenges like this, it is essential to understand how SystemVerilog scheduling semantics operate.


### Scheduling Semantics {#scheduling-semantics}

Scheduling semantics define how each line of code is evaluated and updated within a single simulation `time slot`. A `time slot` in SystemVerilog scheduling semantics refers to a single unit of simulation time. Within each `time slot`, the simulation is further divided into multiple regions, with each region designated to perform specific tasks such as evaluating, updating, or propagating signals. This subdivision ensures precise control over the order of operations and prevents ambiguity during simulation. Figure 3 illustrates the event regions involved in SystemVerilog scheduling semantics.

{{< figure src="/ox-hugo/event_regions.svg" caption="<span class=\"figure-number\">Figure 3: </span>Flow of Execution in a Time Slot" >}}

-   **Preponed Region**
    -   Beginning of a Time Slot: Marks the start of a simulation time slot.
    -   Sample Values for Concurrent Assertions: Captures the values required for evaluating concurrent assertions at this point in the simulation.
    -   Executed Once Per Time Slot: This step occurs only once during each simulation time slot to ensure accurate assertion evaluation.

-   **Active Region**
    -   Execution of `module` Code: In this region, all blocking and non-blocking assignments within the module are scheduled for execution.
    -   Execute all module blocking assignments: This includes processing statements using `=` for immediate updates.
    -   Evaluate the Right-Hand Side (RHS) of all non-blocking assignments and schedule updates into the NBA region: The computed RHS values are stored and scheduled for later updates in the Non-Blocking Assignment (NBA) region.
    -   Execute all module continuous assignments: Continuous assignments update `assign` statements as per the current input signals.
    -   Evaluate inputs and update outputs of Verilog primitives: Inputs to primitives (like gates) are evaluated, and outputs are updated accordingly.
    -   Execute `$display` and `$finish` commands: Simulation-related tasks such as printing messages and ending the simulation are performed here.

**Note**: The _active region_ is used to schedule blocking and non-blocking assignments, as well as tasks or functions called from module code, focusing primarily on RTL activity. To ensure isolation from RTL execution, it is recommended to use the `program` block for testbench code, as the `program` block executes in the _reactive region_.

-   **In-Active Region**
    -   \#0 Blocking Assignments: These are scheduled in this region.
    -   Best Practice: As suggested in [3], it is advised to avoid executing any statements in this region to prevent unintended simulation behavior.

-   **NBA Region**
    -   Update NBAs: The Left-Hand Side (LHS) of non-blocking assignments, which are evaluated in the active region, are updated in this region. Sequential designs within `always` blocks should be modeled using non-blocking assignments `<=` to accurately represent the pipelined behavior of sequential elements.

-   **Observed Region**
    -   Evaluate Concurrent Assertions: Concurrent assertions sampled in the Preponed Region are evaluated in this region. The pass/fail decision is then scheduled for execution in the **Reactive Region** within the same time slot.

-   **Reactive Region**
    -   Execute all program blocking assignments: Processes blocking assignments `=` initiated within program blocks.
    -   Execute pass/fail code from concurrent assertions: Handles the outcomes of concurrent assertions evaluated earlier in the time slot.
    -   Evaluate the Right-Hand Side (RHS) of program non-blocking assignments: The RHS values of non-blocking assignments `<=` in program blocks are evaluated and scheduled for updates in the Re-NBA Region.
    -   Execute all program continuous assignments: Updates continuous assignments specific to program blocks.
    -   Execute `$exit` and implicit `$exit` commands: Handles commands that terminate the simulation or program execution.

The **Reactive Region** is dedicated to executing verification processes initiated by program blocks. Positioned near the end of the simulation time slot, this region provides access to crucial information for verification purposes:

1.  The current steady-state values from the **Active Region** at the start of the time slot.
2.  The updated steady-state values from the **Active Region** after clock and signal propagation.
3.  The results of all concurrent assertions triggered during this time slot.

This sequencing ensures that the verification processes can accurately analyze the design's behavior within a given simulation cycle.

-   **Re-Inactive Region**
    -   Handling Events in Verification: This region manages events scheduled by #0 delays in program blocks, commonly used in verification.
    -   Safe Usage in Verification: Unlike the **Inactive Region** in RTL design, which is typically avoided to prevent race conditions, the **Re-Inactive Region** is safe for verification code and adds control and predictability to simulation behavior.
    -   Interaction with Reactive Region: The **Re-Inactive Region** works in tandem with the **Reactive Region** in a loop, executing repeatedly until all events in both regions are resolved. This ensures that all verification-related tasks are completed before moving to other parts of the simulation.

-   **Re-NBA Region**
    -   Executing Non-Blocking Assignment Updates: The primary purpose of this region is to update the Left-Hand Side (LHS) variables of non-blocking assignments `<=` that were scheduled in the **Re-Active Region** during their evaluation in the Reactive Region.
    -   Iterative Operation: This region works in a loop alongside the **Reactive** and **Re-Inactive Regions**, ensuring all events within the Reactive Region Set are resolved before proceeding to the next phase of simulation.

-   **Postpone Region**
    -   Execute $strobe and $monitor Commands: The main function of this region is to execute $strobe and $monitor commands, which display the final updated values for the current time slot.
    -   Functional Coverage Collection: This region is also utilized to collect functional coverage for items that rely on strobe sampling.
    -   End of the Time Slot: Marks the conclusion of the current simulation time slot.


##  {#d41d8c}

The `Pre` and `Post` regions in the event scheduler are excluded from discussion here for simplicity. These regions are primarily reserved for Programming Language Interface (PLI) commands, which allow simulation tools to interface with external programs such as C/C++. PLI commands are predominantly used for monitoring and debugging purposes in the design process. Some of the commonly used PLI commands are: `$display`, `$monitor`, `$strobe`, `$finish`, and `$stop`.


#### Race Condition: Resolved Example {#race-condition-resolved-example}

To resolve the issue that occured in the example simple change the **exp_res** expression from non-blocking to a blocking statment: **exp_res = queue.pop_front()**. The code below is snippit of the complete code that is posted [here](https://github.com/24x7fpga/RTL/blob/master/rtl_designs/race_condition/interface.sv).

-   When the expression uses a non-blocking assignment (NBA), the **exp_res** is updated in the NBA Region.
-   The _if condition_, **exp_res == res**, is evaluated in the Active Region, which is why the simulation is reported as a failure.
-   When the expression uses a blocking assignment, **exp_res** is evaluated and updated in the Active Region, and the if statement is also evaluated in the same region. As a result, the simulation does not fail.
-   Although it is recommended to use non-blocking assignments (NBA) for sequential logic inside an always block [4], in this particular case, it should not have been used.

<!--listend-->

```verilog
always@(posedge clk)begin
   if(res_vld)begin
      if(queue.size() == 0)begin
         $error("Failure: Queue Empty ;(");
         $finish;
      end else begin
         // exp_res <= queue.pop_front(); // Updated in NBA :: Race Condition
            exp_res  = queue.pop_front(); // Updated in Active :: Correct implementation
         if(exp_res == res)
           $display("PASS :: Exp_Result = %d and Result = %d", exp_res, res);
         else begin
            $error("FAIL :: Exp_Result = %d and Result = %d", exp_res, res);
            $finish;
         end
      end // else: !if(queue.size() == 0)
   end // if (res_vld)
end
```

The simulation results shown in Figure 4 indicate that after updating the code, no errors are encountered. This highlights the importance of understanding event semantics in SystemVerilog to ensure the correctness of the verification process.

{{< figure src="/ox-hugo/resolved_sim.png" caption="<span class=\"figure-number\">Figure 4: </span>Resolved Simulation" >}}


##  {#d41d8c}

Understanding scheduling semantics is crucial for streamlining the verification process, as it governs the order of execution of tasks such as evaluating expressions, updating values, executing assertions, and evaluating them during simulation. A clear grasp of how events are scheduled ensures that the design behaves as expected and helps avoid timing-related issues, leading to more efficient and reliable simulations.


### Points to Remember {#points-to-remember}

-   **Active Region**: This region is designed to `implement the correct RTL functionality`, where the actual design logic is evaluated.
-   **Preponed, Reactive, and Postponed Regions**: These regions are primarily designed to ensure `the correct execution of verification tasks`.
-   **Preponed, Observed, and Reactive Regions**: These regions are specifically designed for `concurrent assertion checking` to validate design behavior in real-time.
-   **Inactive Region**: This region should be avoided as it can lead to unintended behavior and potential race conditions in the simulation.


#### Reference {#reference}

1.  [1800-2023 - IEEE Standard for SystemVerilog--Unified Hardware Design, Specification, and Verification Language](https://ieeexplore.ieee.org/document/10458102/versions#versions)
2.  [SystemVerilog 3.1a LRM Accellera's Externions to Verilog](https://ece.uah.edu/~gaede/cpe526/SystemVerilog_3.1a.pdf)
3.  [SystemVerilog Event Regions, Race Avoidance &amp; Guidelines](http://www.sunburst-design.com/papers/CummingsSNUG2006Boston_SystemVerilog_Events.pdf)
4.  [Nonblocking Assignments in Verilog Synthesis, Coding Styles That Kills!](http://www.sunburst-design.com/papers/CummingsSNUG2000SJ_NBA.pdf)
