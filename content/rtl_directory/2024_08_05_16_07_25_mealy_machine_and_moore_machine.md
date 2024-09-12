+++
title = "Mealy Machine and Moore Machine"
author = ["Kiran"]
date = 2024-08-05T16:07:00-04:00
tags = ["rtl"]
draft = false
css = "../../zcustom.css"
+++

[Design](https://github.com/24x7fpga/iVerilog/tree/master/design/fsm_seq) -- [Testbench](https://github.com/24x7fpga/iVerilog/blob/master/tb_design/tb_fsm_seq/tb_fsm_seq.v) -- [RTL Design Directory]({{< relref "2024_06_05_00_21_53_rtl_design_directory.md" >}})


## Finite State Machine {#finite-state-machine}

A Finite State Machine (FSM) is a mathematical model used to represent a system with a finite number of states and a set of transitions between those states, driven by inputs. Each state can be considered as a decision node, where the decision is based on the input and or the input and the current decision tree node.


#### Components of an FSM: {#components-of-an-fsm}

-   States: Unique condition that define the state of the system.
-   Inputs: Inputs are signals or data that trigger a state change.
-   Transitions: Rules that dictate how the FSM moves from one state to another based on inputs.
-   Outputs: Values that the FSM produces, based either on the state (Moore) or state and inputs (Mealy).


##  {#d41d8c}

The two main types of FSMs:

1.  Mealy Machine: The output is a function of the present state and the present input.
2.  Moore Machine: The output is a function of only the present state.


### Mealy Machine {#mealy-machine}

In a Mealy machine, the output depends on both the current state and the input. This means the system reacts instantly to input changes, with the output potentially updating even before transitioning to a new state. As inputs change, the machine can adjust its behavior dynamically, making it responsive and efficient in scenarios where quick reactions are needed.

{{< figure src="/ox-hugo/mealy_machine.svg" caption="<span class=\"figure-number\">Figure 1: </span>Mealy Machine" >}}

As shown in Figure 1, the diagram illustrates two primary components: the state register and the combinational logic. The `state register` stores the current state of the system. It updates on each clock cycle and serves as a reference for the machine's current condition. The `combinational logic` block takes both the output from the state register (representing the current state) and the external input signals. Together, these determine both the next state of the system and the output at that moment.


### Moore Machine {#moore-machine}

In a Moore machine, the output is determined entirely by the current state of the system, not the inputs. This means that the output only updates when the system transitions from one state to another. As a result, the output remains stable and consistent within each state, making the behavior of the Moore machine more predictable and easier to manage.

{{< figure src="/ox-hugo/moore_machine.svg" caption="<span class=\"figure-number\">Figure 2: </span>Moore Machine" >}}

In a Moore machine, the system's output is determined solely by its current state, as shown in Figure 2. The `combinational logic` block calculates the output based purely on the value stored in the state register, independent of any external inputs.


### Difference between Mealy and Moore FSM {#difference-between-mealy-and-moore-fsm}

|                             | Mealy Machine                                                              | Moore Machine                                                 |
|-----------------------------|----------------------------------------------------------------------------|---------------------------------------------------------------|
| **Output Logic Complexity** | May require complex output logic, since it depends on both input and state | Has simpler output logic, depending only on the current state |
| **Output Dependency**       | Output depends on both the input and the present state                     | Output depends solely on the present state                    |
| **Output Response Time**    | Output changes immediately when the input changes                          | Output changes only when the state changes                    |
| **Hardware Requirements**   | Typically requires less hardware for circuit implementation                | Usually requires more hardware for circuit implementation     |
| **Output Timing**           | Generates asynchronous outputs                                             | Generates synchronous outputs                                 |
| **State Requirement**       | Generally requires fewer or the same number of states as a Moore machine   | Often requires more states than a Mealy machine               |


### Coding Style {#coding-style}

Following a good coding style is essential for creating clear, reliable, and maintainable RTL designs. There are a few key approaches and coding practices commonly used in FSM design.


#### State Encoding {#state-encoding}

State encoding in a Finite State Machine (FSM) refers to how each state is represented using binary values in the hardware. The choice of encoding has a significant impact on the overall design, affecting factors like complexity, speed, and resource usage. Different encoding techniques can be chosen based on specific design needs, whether the focus is on performance, power efficiency, or minimizing hardware area.

-   **Binary:** In a Binary or sequential encoding scheme, each state is assigned a unique binary number. For an `n` FSM state, the total number of registers required is `log2(n)` bits.

<!--listend-->

```verilog
parameter START = 2'b00,
          STAT1 = 2'b01,
          STAT2 = 2'b10,
          END   = 2'b11;

reg [1:0] st_reg, st_nxt;
```

-   **Onehot Encoded:** In the Onehot encoded scheme, each state is represented by a unique binary value where only one bit is `high (hot)` at any given time. For an `n` FSM states, the required state register is `n` bits.

<!--listend-->

```verilog
parameter START = 4'b0001,
          STAT1 = 4'b0010,
          STAT2 = 4'b0100,
          END   = 4'b1000;

reg [3:0] st_reg, st_nxt;
```

-   **Gray Code:** In the Gray Code encoding scheme, each state is encoded in such a way that only one-bit changes between any consecutive states.

<!--listend-->

```verilog
parameter START = 2'b00,
          STAT1 = 2'b01,
          STAT2 = 2'b11,
          END   = 2'b10;

reg [1:0] st_reg, st_nxt;
```


##  {#d41d8c}

| Encoding style | Bits Required | Pros                    | Cons                               |
|----------------|---------------|-------------------------|------------------------------------|
| Binary         | log2(n)       | Minimizes flip-flops    | Complex next-state logic           |
| One-Hot        | n             | Simple and fast         | Requries as many registers as bits |
| Gray Code      | log2(n)       | Reduces switching noise | More Complex design                |

When implementing a Finite State Machine (FSM) on an FPGA, the one-hot encoding scheme is often recommended. This approach is well-suited for FPGAs as they pack a large number of flip-flops (FFs), which allow for straightforward and efficient state management. One-hot encoding simplifies the state transitions and can lead to faster operation because each state is represented by a single flip-flop, making the logic for state transitions and output straightforward.

In contrast, when designing for Application-Specific Integrated Circuits (ASICs), flip-flops come at a premium in terms of both area and cost. Therefore, careful consideration must be given to the state encoding scheme used. In ASIC designs, binary or Gray code encoding might be preferred due to their reduced flip-flop requirements. This choice helps in minimizing the hardware footprint and managing costs, although it can result in more complex state transition logic.

By selecting the appropriate state encoding based on the target technology—FPGA or ASIC—you can optimize the FSM design for both performance and resource efficiency.


### Design Practices for Synthesizable Finite State Machines (FSMs) {#design-practices-for-synthesizable-finite-state-machines--fsms}

When designing a synthesizable Finite State Machine (FSM), there are several approaches you can take, each with its own advantages and use cases. Here are three common practices:


#### One Always Block {#one-always-block}

In this approach, both state transitions and output logic are handled within a single always block. This method integrates the state register update and output generation into one place, simplifying the design and making the FSM easier to understand.

<!--list-separator-->

-  Advantages

    -   Simplifies the FSM design by placing all logic in a single always block.
    -   Makes the design easier to read and manage, especially for smaller FSMs.

<!--list-separator-->

-  Example Use Case

    -   Ideal for straightforward FSMs where combining logic does not increase complexity.


#### Two Always Blocks {#two-always-blocks}

This practice separates the FSM into two always blocks: one for state transitions and another for output logic. The first block handles state updates based on the clock and reset signals, while the second block generates outputs based on the current state and inputs.

<!--list-separator-->

-  Advantages

    -   Enhances clarity by separating state transitions from output logic.

<!--list-separator-->

-  Example Use Case

    -   Suitable for medium complexity FSMs where separation of concerns improves design readability and manageability.


#### Three Always Blocks: {#three-always-blocks}

In this approach, the FSM is divided into three distinct always blocks: one for state register updates, one for next-state logic, and one for output logic. The third always is implemented for registered outputs of the FSM.

<!--list-separator-->

-  Advantages

    -   Registering the outputs ensures that the output is glitch-free.

<!--list-separator-->

-  Example Use Case

    -   Best for complex FSMs where a glitch-free output is necessary.

    Choosing the right practice depends on the complexity of your FSM and your design goals. Each approach offers different levels of simplicity, and clarity, helping you tailor your design to meet specific needs effectively.


### Points to Remember {#points-to-remember}

1.  Use parameters to define state encodings.
2.  Make `state` and `next state` declarations right after the parameter declarations.
3.  Code all sequential always block using nonblocking assignments and all combinational always block using blocking assignments to avoid Verilog simulation race conditions.
4.  After defining the state encodings at the top of the FSM module, never use the state encodings again in the RTL code.
5.  Avoid the one always block FSM coding style unless required.
6.  It is recommended to use a two always block coding style to code FSM designs with combinational outputs. This style is efficient and easy to code and can also easily handle Mealy FSM designs.
7.  Use a three always block coding style to code FSM designs with registered outputs for a glitch-free output.


### References {#references}

1.  [Cummings, Clifford E. "The fundamentals of efficient synthesizable finite state machine design using nc-verilog and buildgates." Proceedings of International Cadence Usergroup Conference. 2002.](http://www.sunburst-design.com/papers/CummingsICU2002_FSMFundamentals.pdf)
2.  [Cummings, Clifford E. "Synthesizable Finite State Machine Design Techniques Using the New SystemVerilog 3.0 Enhancements." SNUG (Synopsys Users Group San Jose, CA 2003) Proceedings (2003).](http://www.sunburst-design.com/papers/CummingsSNUG2003SJ_SystemVerilogFSM_rev1_1.pdf)
