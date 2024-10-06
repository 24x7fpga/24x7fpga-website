+++
title = "SV Verification Workflow in Vivado"
author = ["Kiran"]
date = 2024-10-05T18:24:00-04:00
tags = ["sv"]
draft = false
css = "../../zcustom.css"
+++

[SystemVerilog](https://github.com/24x7fpga/SystemVerilog_Verification) -- [SV Vivado Script](https://github.com/24x7fpga/SystemVerilog_Verification/blob/main/sv_tcl_script/sv_run.tcl) -- [SV Python Script](https://github.com/24x7fpga/PythonScripts/tree/master/sv_scripts)

SystemVerilog was introduced to simplify the verification process by incorporating software features like Object-Oriented Programming (OOP). It significantly enhances testbench development, making the verification of complex designs more manageable. For those looking to experiment with different tools, [EDA Playgorund](https://www.edaplayground.com/)  offers a convenient platform to perform verification using a variety of commercial simulators, such as Cadence Xcelium, Synopsys VCS, and Aldec Riviera-PRO.

In my own workflow, I prefer using Vivado Simulator, as it integrates seamlessly with my design flow. Vivado installation guide can be found [here](https://24x7fpga.com/rtl_directory/2024_07_28_10_50_56_vivado_installation/).


## Project Folder {#project-folder}

The main directory is named `Projects` and is located in the home directory. Within this, there are two key subfolders:

1.  `FPGA_Projects:` This folder contains all the FPGA-related projects.
2.  `Python_Scripts:` This folder holds Python scripts related to the FPGA projects.

This structure keeps projects and supporting scripts well-organized for efficient workflow management. The folder structure I use is outlined below:

```bash
Projects
.
├── FPGA_Projects
│   ├── SystemVerilog_Verification
│   │   ├── Readme.org
│   │   ├── sv_tcl_script
│   │   └── sv_verification
│   ├── UVM
│   │   ├── uvm_tcl_script
│   │   ├── uvm_templates
│   │   └── uvm_verification
│   ├── VanillaFPRO
│   │   ├── Design_Tests
│   │   ├── Drivers
│   │   ├── HDL
│   │   ├── Projects
│   │   └── ZyboZ7_BoardFiles
│   ├── ZyboZ7
│   │   ├── BoardFiles
│   └── iVerilog
│       ├── README.org
│       ├── design
│       ├── doc
│       └── tb_design
│
└── Python_Scripts
    ├── iVerilog
    ├── sv_scripts
    ├── uvm_scripts
    └── vivado
```

**Note:** To run the Python scripts from any directory in your terminal, you’ll need to add the folder/file path to your .zshrc or .bashrc file. This ensures the scripts are accessible globally, regardless of your current working directory.


## Verification Folder Structure {#verification-folder-structure}

The [SystemVerilog_Verification](https://github.com/24x7fpga/SystemVerilog_Verification/tree/main) repository on GitHub is structured into two key sections:

1.  [sv_tcl_script](https://github.com/24x7fpga/SystemVerilog_Verification/tree/main/sv_tcl_script): This folder contains a Tcl script designed to automate the simulation process in Vivado, making it easier to run and manage simulations efficiently.
2.  [sv_verification](https://github.com/24x7fpga/SystemVerilog_Verification/tree/main/sv_verification): This folder includes a collection of verification designs, providing a comprehensive set of examples for verifying SystemVerilog code.

<!--listend-->

```bash
SystemVerilog_Verification
.
├── sv_tcl_script
|   └── sv_run.tcl
└── sv_verification
    ├── associative_arrays
        └── tb_associative_arrays.sv
    .
    .
    .
```


## Python Script {#python-script}

A Python script, [sv_design.py](https://github.com/24x7fpga/PythonScripts/blob/master/sv_scripts/sv_design.py), is designed to automate the creation of a project folder within the `sv_verification` directory. When executed, it generates the necessary SystemVerilog (SV) verification files, with the top-level testbench file labeled as `tb_<design_name>.sv`.

To simulate the design, the [sv_run.py](https://github.com/24x7fpga/PythonScripts/blob/master/sv_scripts/sv_run.py) script is used, which calls the Vivado simulator. This script, in turn, invokes the [sv_run.tcl](https://github.com/24x7fpga/SystemVerilog_Verification/blob/main/sv_tcl_script/sv_run.tcl) file located in the `sv_tcl_script` folder. The project folder will be named `verif` within the design directory.

Additionally, the sv_clean.py script is available to remove the project folder and clean up Vivado log files, ensuring a tidy workspace.

```bash
sv_design.py <design_name>    // create the design
sv_run.py    <design_name>    // execute the simulation
sv_clean.py                   // removes the project and log files created by Vivado
```


## Steps to Create and Simulate a SV Verification Project {#steps-to-create-and-simulate-a-sv-verification-project}

-   Open your terminal and create the design environment by executing the `sv_design.py` script. For example, to create a verification project named "test", run the following command:

<!--listend-->

```bash
sv_design.py test
```

-   A folder named `test` will be generated inside the `sv_verification` directory, and the top-level file will be named `tb_test.sv`.

-   Complete the necessary design files, then execute the `sv_run.py` script to begin the simulation. For example:

<!--listend-->

```bash
sv_run.py test
```

-   This will create a project folder named `verif` inside `sv_verification/test` and display the simulation results in the terminal.
