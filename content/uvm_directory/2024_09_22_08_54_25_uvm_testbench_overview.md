+++
title = "UVM Testbench Overview"
author = ["Kiran"]
date = 2024-09-22T08:54:00-04:00
tags = ["uvm"]
draft = false
css = "../../zcustom.css"
+++

[UVM Framework Directory]({{< relref "2024_08_28_12_39_50_uvm_framework_directory.md" >}})

The UVM testbench follows a layered architecture, where each component is dedicated to a specific task and interacts with the rest of the testbench through well-defined interfaces. This design promotes modularity and reusability, making it easier to manage and extend the testbench. Figure 1 illustrates a typical UVM testbench architecture.

{{< figure src="/ox-hugo/uvm_tb_overview.svg" caption="<span class=\"figure-number\">Figure 1: </span>UVM Testbench Architecture" >}}


## UVM Testbench {#uvm-testbench}

The `UVM Testbench` is the top-level module in the hierarchy, where both the UVM Test and the Design Under Test (DUT) are instantiated. It also handles the configuration of connections between these components. One of the key features of the `UVM Test` is that it is `dynamically instantiated at run-time`, enabling the testbench to be compiled just once while running various tests, making the verification process more flexible and efficient.


## UVM Test {#uvm-test}

The UVM Test is the top-level component in the UVM testbench hierarchy, dynamically created at run-time. It plays a crucial role in the verification process by performing three key functions:

1.  Instantiating the top-level environment.
2.  Configuring the environment using factory overrides or the configuration database.
3.  Applying stimulus by invoking UVM sequences, which drive the stimulus through the environment to the DUT.


### UVM Sequence {#uvm-sequence}

The UVM Sequence is an `object` that control the generation and flow of stimulus to the driver.


### Config/Factory Overrides {#config-factory-overrides}


## UVM Environment {#uvm-environment}

The UVM Environment is a hierarchical component that groups other verification components that are interrelated. The Top-level Environment encapsulates all the verification components targeting the DUT. Typically, the UVM Agents, UVM Scorboards or even other UVM Environments are instantiated in the top-level Environment.


### UVM Sequencer {#uvm-sequencer}

The UVM Sequencer manages the execution of sequences and is responsible for arbitration when multiple sequences are running. It communicates with drivers by sending sequence items.


### UVM Scoreboard {#uvm-scoreboard}

The UVM Scoreboard collects the resposes from the DUT and checks for correctness by comparing to a reference model know as the `predictor`.


### UVM Agent {#uvm-agent}

The UVM Agent is a hierarchical component that groups three other components. Typically, the grouped components are sequencer, driver and monitor.


#### Sequencer {#sequencer}

The UVM Sequencer manages the stimulus flow.


#### Driver {#driver}

The UVM Driver is responsible for applying stimulus to the DUT interface. It receives individual sequence item transactions from the UVM Sequencer and translates them into pin-level signals for the DUT. The UVM Driver utilizes a Transaction Level Modeling (TLM) port to convert these high-level transactions into the appropriate low-level stimulus for the DUT, ensuring accurate signal application at the interface.


#### Monitor {#monitor}

The UVM Monitor samples the DUT interface. Similary to the Driver, a TLM port is incorporated to converts pin-level reponses to a transactions item for the rest of the testbench components.


## Design Under Test (DUT) {#design-under-test--dut}

As the name suggests, the Design Under Test (DUT) refers to the synthesizable Verilog/SystemVerilog component that is being tested for functional correctness.
