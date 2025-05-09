+++
title = "UVM Workflow in Vivado"
author = ["Kiran"]
date = 2024-09-04T14:44:00-04:00
tags = ["uvm"]
draft = false
css = "../../zcustom.css"
+++

[UVM](https://github.com/24x7fpga/UVM) -- [UVM Vivado Script](https://github.com/24x7fpga/UVM/blob/main/uvm_tcl_script/uvm_run.tcl) -- [UVM Python Script](https://github.com/24x7fpga/PythonScripts/blob/master/uvm_scripts/uvm_run.py)

The UVM (Universal Verification Methodology) workflow is centered around creating a testbench that integrates key UVM components such as drivers, monitors, and scoreboards, alongside the essential UVM library setup. Numerous commercial tools are available for simulating and verifying digital designs, including ModelSim, Cadence Xcelium, Synopsys VCS, Aldec Riviera-PRO, and Vivado Simulator. Another excellent option for experimenting with small-to-medium-sized verification projects is [EDA Playground](https://www.edaplayground.com/). EDA Playground offers the advantage of requiring no installation and provides access to multiple simulators for the design. Personally, I opted to use Vivado Simulator for the UVM verification process, as it seamlessly integrates into my existing workflow.

Vivado installation guide can be found [here](https://24x7fpga.com/rtl_directory/2024_07_28_10_50_56_vivado_installation/).

{{< alert warning >}}
<div class="alert-heading">

Restricted Language Support

</div>

The Vivado simulator offers limited support for SystemVerilog features (e.g., the unique keyword and others). Similarly, the QuestaSim Starter Edition has restrictions, particularly with advanced verification features of SystemVerilog. As a result, I have adopted EDA Playground as my primary platform for developing and testing verification environments due to its comprehensive support for multiple simulators.
{{< /alert >}}


## Vivado Setup for UVM Simulation {#vivado-setup-for-uvm-simulation}

To use a pre-compiled UVM library, you need to adjust two key project settings. Open the project settings and navigate to the simulation section. Here, you will need to configure the settings to ensure the UVM library is properly utilized.

-   Under the compilation tab, add "-L UVM" to _xsim.compile.xvlog.more_options_.

{{< figure src="/ox-hugo/uvm_comp.png" >}}

-   Under the elaboration tab, add "-L UVM" to _xsim.elaborate.xelab_more_options_.

{{< figure src="/ox-hugo/uvm_elab.png" >}}

When using a tcl file to simulate the design, the following commands should be added to use the UVM library.

```tcl
set_property -name {xsim.compile.xvlog.more_options} -value {-L uvm} -objects [get_filesets sim_1]
set_property -name {xsim.elaborate.xelab.more_options} -value {-L uvm} -objects [get_filesets sim_1]
```

-   [Xilinx Support](https://support.xilinx.com/s/article/1070861?language=en_US)


## UVM Folder Structure {#uvm-folder-structure}

The [UVM](https://github.com/24x7fpga/UVM) folder on GitHub is organized into three key subfolders:

1.  [uvm_tcl_script](https://github.com/24x7fpga/UVM/tree/main/uvm_tcl_script): This folder contains a Tcl file used for running simulations in Vivado. It provides the necessary script to automate the simulation process.
2.  [uvm_templates](https://github.com/24x7fpga/UVM/tree/main/uvm_templates): This folder includes all the UVM component files. These templates are used to generate the necessary UVM files based on your specific verification design, facilitated by a Python script.
3.  [uvm_verification](https://github.com/24x7fpga/UVM/tree/main/uvm_verification): Contains all the verification designs. This folder houses a set of designs for verification purposes.

<!--listend-->

```linux
UVM
.
├── uvm_tcl_script
│   └── uvm_run.tcl
├── uvm_templates
│   └── < UVM Component files >
└── uvm_verification
    └── test
    .
    .
    .
```


## Python Script {#python-script}

Python scripts are utilized to streamline the setup of UVM verification environments. When [uvm_design.py] executed, the script creates a new folder within the _uvm_verification_ directory, named after the specified design. Inside this folder, it generates essential files such as the driver, sequencer, monitor, and scoreboard, based on predefined UVM templates.

Additionally, the [uvm_run.py](https://github.com/24x7fpga/PythonScripts/tree/master/uvm_scripts) script can be used to automatically create a Vivado project within the design folder. The project will be named _verif_, facilitating a smooth setup for your verification workflow.

These scripts automate and simplify the process of initializing UVM projects and Vivado environment, enhancing efficiency in the verification tasks.

```bash
uvm_design.py <design_name>     // create the design
uvm_run.py    <design_name>     // execute the simulation
uvm_clean.py                    // removes the project and log files created by Vivado
```

The Python scripts that I use in my workflow are organized in a dedicated folder available at  [Python Scripts](https://github.com/24x7fpga/PythonScripts/tree/master) on GitHub. To ensure you can run these scripts from any directory in your terminal, you need to add the folder path to your .zshrc or .bashrc file. This step is essential for seamless execution of the scripts across your system.


## Verification Flow {#verification-flow}

1.  Create the Design: Execute the [uvm_design.py] Python script to generate the design files. This script will create a design folder and populate the _uvm_verification_ directory with all necessary files.

2.  Complete the UVM Verification Files: Finalize and customize the UVM verification files to suit your specific verification needs.

3.  Run the Design: Use the [uvm_run.py](https://github.com/24x7fpga/PythonScripts/tree/master/uvm_scripts) script to initiate the simulation of your design.


## Example Test {#example-test}

To test the UVM scripts, a simple test design is created which is shown below.

```verilog
`include "uvm_macros.svh"
 import uvm_pkg::*;

module tb_test;

   initial begin
        `uvm_info("RUN_TEST", "Vivado Script Simulation Test", UVM_NONE);
   end

endmodule
```

The primary objective of this design is to print a string using the `uvm_info` function. This design serves as a basic example to ensure that the UVM scripts are functioning correctly and that the information is being output as expected.

```bash
uvm_verification
.
└── test
    ├── tb_test.sv
    └── verif
        ├── tb_test.cache
        ├── tb_test.hw
        ├── tb_test.ip_user_files
        ├── tb_test.sim
        └── tb_test.xpr
```

The design files are organized under the _test_ directory, ensuring that all source code is conveniently located in one place. Testbench top module is labeled as the design name prefixed with 'tb'. For the design example, the design name is 'test', and 'tb_test' is the testbench top module. When you execute the [uvm_run.py](https://github.com/24x7fpga/PythonScripts/tree/master/uvm_scripts) script, a new Vivado project is generated within the _verif_ directory. This structure helps maintain a clear separation between design sources and verification projects, streamlining the workflow and project management.

{{< figure src="/ox-hugo/uvm_sim.png" >}}

Below is the result of the simulation. The design outputs the string "[RUN TEST] Vivado Script Simulation Test" as intended.

{{< figure src="/ox-hugo/uvm_sim_result.png" >}}
