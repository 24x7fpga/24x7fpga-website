+++
title = "Design Process for Constraint Challenges"
author = ["Kiran"]
date = 2024-11-12T19:05:00-05:00
tags = ["sv"]
draft = false
css = "../../zcustom.css"
+++

In digital design, automation is key to streamlining workflows. I developed a Python script that generates the required `.sv` files for simulation. This script automates the process of creating all the necessary files for the design, saving time and reducing manual effort. By automatically generating these simulation files, the script helps standardize and expedite the verification process, ensuring consistency and minimizing the chances of human error.


## Folder Structure {#folder-structure}

```bash
Projects
.
├── FPGA_Projects
│   └── SystemVerilog_Verification
│       ├── Readme.org
│       ├── sv_tcl_script
│       ├── sv_templates
│       └── sv_verification
│           ├──   ...
│           ├── cstrs_challenges
│           ├──   ...
│           └──   ...
│
└── Python_Scripts
    └── sv_scripts
        ├── sv_clean.py
        ├── sv_cstrs_design.py
        ├── sv_cstrs_run.py
        ├── sv_design.py
        └── sv_run.py
```

The project directory is organized into two main sections: `FPGA_Projects` and `Python_Scripts`.

-   **FPGA_Projects**: This folder contains all files related to SystemVerilog verification for FPGA projects.
    -   _SystemVerilog_Verification_: The core directory for SystemVerilog-based verification projects.
        -   `Readme.org`: Documentation detailing the project structure, setup, and usage instructions.
        -   `sv_tcl_script`: Stores TCL scripts for automating simulation process using Vivado.
        -   `sv_templates`: Contains template files used for SystemVerilog modules, ensuring consistency and reusability.
        -   `sv_verification`: Holds the verification-specific designs folders.
            -   `cstrs_challenges`: Holds the constrains design challenge examples.
-   **Python_Scripts**: This folder includes Python scripts that support and automate tasks within the **Projects** directory.
    -   _sv_scripts_: A collection of scripts focused on different aspects of the SystemVerilog workflow.
        -   `sv_clean.py`: Removes the generated files and directories from Vivado runs.
        -   `sv_cstrs_design.py`: Files are generated based on the files from the `sv_templates` folder for simulations.
        -   `sv_cstrs_run.py`: Automates running constraint-based checks for verification using Vivado.
        -   `sv_design.py`: Handles file generation for SystemVerilog design modules based on the templates from `sv_templates/design`.
        -   `sv_run.py`: Executes simulation runs using Vivado.

{{< alert warning >}}
Due to limited verification support in Vivado Simulator, EDA Playground is utilized for full SystemVerilog compatibility in design simulations.
{{< /alert >}}

Even the run scripts are create to streamline the process using Vivado, due to its limited support, the scripts are used solely to generate the necessary files to complete the code. Simulations are conducted on EDA Playground. Only `sv_cstrs_design.py` and the template files in the `sv_templates` directory are required to create design files. The `sv_cstrs_design.py` script creates a directory labeled by the user inside the sv_verification directory, while the template files in `sv_templates` serve as a blueprint to streamline simulation.


## Templates {#templates}

Templates for constraint challenges are located in the `constraints` subdirectory within the `sv_templates` directory.

Hierarchical design structure is shown below that maintains modularity and enhances scalability.

```bash
tb.sv
└── test.sv
    └── cstrs_challenge.sv

```

-   `tb.sv`: The top-level testbench file that sets up similation environment.

<!--listend-->

```verilog
`include "test.sv"

module tb_$ARG;

   test t1 ();

endmodule // tb_$ARG
```

-   `test.sv`: This serves as a foundational component in the SystemVerilog Constraints Challenge. It demonstrates how to instantiate a constraint class, randomize its values, and display the results

<!--listend-->

```verilog
`include "cstrs_challenge.sv"

program test();

   cstrs_challenge ct;

   initial begin
      ct = new();         // Create a new instance of the cstrs_challenge class.
      ct.randomize();     // Randomize the fields based on defined constraints.
      ct.disp();          // Display the randomized values.
   end

endprogram // test
```

-   `cstrs_challenge.sv`: This file is dedicated to implementing the constraints challenge. It serves as the primary focus when taking on a new challenge, as it is the only file that requires completion.

<!--listend-->

```verilog
class cstrs_challenge;

   // class properties

   // display function
   function void disp();

   endfunction // disp

endclass // cstrs_challenge
```


## Python Scripts {#python-scripts}

To make it easier to run your Python scripts from any location in the terminal, you can add the directory containing your Python file to your .bashrc (for Bash users) or .zshrc (for Zsh users) configuration file.

```bash
echo 'export PATH="$HOME/Projects/Python_scripts/sv_scripts:$PATH"' >> ~/.zshrc && source ~/.zshrc
```


### Design Script {#design-script}

The Python script `sv_design.py`, located in the `sv_script` directory, simplifies the process of setting up new challenges. Its purpose is to automate the creation of a subdirectory with the challenge name inside the `cstrs_challenge` directory and copy the necessary challenge scripts into the newly created folder.

This script ensures a consistent and efficient workflow for organizing and managing multiple constraints challenges and reducing manual effort.

```python
#!/usr/bin/env python3

import os
import sys

name = sys.argv[1]
home = os.environ['HOME']

proj_path  = home + "/Projects/FPGA_Projects/SystemVerilog_Verification/sv_verification/cstrs_challenges"
temp_path  = home + "/Projects/FPGA_Projects/SystemVerilog_Verification/sv_templates/constraints"

# create a folder is the project name
try:
    os.chdir(proj_path)
    os.mkdir(name)
    print('Directory creation successful ;)')
except:
    print('Directory creation Failed ;(')

# copy the template file into the source directory
try:
    os.system("cp -f "+temp_path+"/* "+proj_path+"/"+name)
    print('Copied the Template Files ;)')
except:
    print('Copying Templates Failed ;(')

# rename top module
try:
    os.chdir(proj_path+"/"+name)
    os.system("mv tb.sv tb_"+name+".sv")
    print('Topmodule Rename Successful ;)')
except:
    print('Topmodule Rename Failed ;(')

# rename the test program
try:
    os.chdir(proj_path+"/"+name)
    os.system("sed -i 's/$ARG/"+name+"/g' tb_"+name+".sv")
    print('Renamed the Program ;)')
except:
    print('Renaming the Program Failed ;(')
```


### Run Script {#run-script}

The script `sv_run.py` is designed to call the `sv_run.tcl` file, which automates the creation and execution of a Vivado project. However, due to Vivado's limited support for verification, this script is not currently in use.

```python
#!/usr/bin/env python3

import os
import sys
import subprocess

name = sys.argv[1]
home = os.environ['HOME']

t_path = home + "/Projects/FPGA_Projects/SystemVerilog_Verification/sv_tcl_script/sv_run.tcl"
d_path = home + "/Projects/FPGA_Projects/SystemVerilog_Verification/sv_verification/"+name

vivado_command = [
    "vivado",
    "-mode", "batch",
    "-source", t_path,
    "-tclargs", name, d_path
]

subprocess.run(vivado_command)
```
