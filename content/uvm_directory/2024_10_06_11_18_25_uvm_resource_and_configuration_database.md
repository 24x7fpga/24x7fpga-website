+++
title = "UVM Resource and Configuration Database"
author = ["Kiran"]
date = 2024-10-06T11:18:00-04:00
tags = ["uvm"]
draft = false
css = "../../zcustom.css"
+++

[Source](https://github.com/24x7fpga/UVM/blob/main/uvm_verification/config_db/tb_config_db.sv) -- [UVM Framework Directory]({{< relref "2024_08_28_12_39_50_uvm_framework_directory.md" >}})

Data can be shared across the an UVM testbench using two key mechanisms: the configuration database and the resources database. While both serve the purpose of sharing data between components, they are used in distinct contexts. The configuration database is typically used for setting up component-specific configurations, while the resources database is a more general-purpose tool for sharing any kind of data across the testbench.


## Resource Database {#resource-database}

The resources database in UVM is a parameterized container used to store arbitrary data in a global or hierarchical manner. It can hold any type of data, constrained only by the data types available in SystemVerilog. Each resource item can be retrieved either by its name or by its type. Additionally, multiple resources with the same name or type can exist in the database. The resources are stored in two associative arrays, one with the name as the key and the other with the type as the key, both organized in the form of a queue. Resources are added to the resource pool by the `set` method and retrieved by calling `get_by_name` or `get_by_type`. When multiple resources share the same name or type, the one that was added first in the queue takes precedence, following a First In, First Out (FIFO) approach. The `uvm_resource_db` class provides a convenience interface for the resource facility.


## Configuration Database {#configuration-database}

The `uvm_config_db` class provides a simple interface on top of the `uvm_resource_db` to simply the basic interface that is used for configuring uvm_component instances. All of the functions in `uvm_config_db` are **static**, they must be called using the **scope(::)** operator. There are two major use cases of uvm_config_db and they are:

1.  To pass the virtual interfaces from the DUT to the test.
2.  To pass configuration objects down through the testbench hierarchy.


### Set Method {#set-method}

The syntax for the `set` method is shown below:

```verilog
uvm_config_db #(type T = int)::set(uvm_component cntxt, string inst_name, string field_name, T value); // set method
```

-   type T: is the type of resources being added to the database, usually a virtual interface or a configuration object.
-   uvm_component cntxt: the cntxt parameter helps to define the component hierarchy level where the resource is stored or made available. Can have two values "null" and "this". `Null` refers to  via `uvm_root` the resource is accessible to every component, when using `this` the resource is accessible via the provided scope.
-   string inst_name: instance name of the UVM component or hierarchy where the configuration setting is applied.

_cntxt_ and _inst_name_ together form the scope to locate the resouce within the database.

-   string field_name: name given to the resource.
-   T value: actual data or reference to be put in the database.


### Get Method {#get-method}

The syntax for the `get` method is shown below:

```verilog
uvm_config_db#(type T = int)::get(uvm_component cntxt, string inst_name, string field_name, ref T value); // get method
```

-   type T: is the type of resources being retrieved from the database, usually a virtual interface or a configuration object.
-   uvm_component cntxt: the cntxt parameter helps to define the component hierarchy level where the resource is stored or made available. Can have two values "null" and "this". `Null` refers to  via `uvm_root` the resource is accessible to every component, when using `this` the resource is accessible via the provided scope.
-   string inst_name: instance name of the UVM component or hierarchy where the configuration setting is applied.

_cntxt_ and _inst_name_ together form the scope to locate the resouce within the database.

-   string field_name: name given to the resource.
-   T value: holds the retrieved value from the database.

The `get()` method returns 1 if successful, or 0 if no resource exists in the database with the specified name/type.


##  {#d41d8c}

**Note:** The UVM config database is the recommended way to access the resource database [1].


## Component Hierarchy {#component-hierarchy}

Understanding how resources are made available to testbench components through `uvm_config_db` is essential for effective configuration management in UVM. Below are three examples demonstrating how resources can be made accessible to various components in the testbench.

```verilog
// sharing source between all the components
uvm_config_db#(int t)::set(null, "*", "value", value);                             // example 1
// sharing resources to a particular component
uvm_config_db#(int t)::set(this, "uvm_test_top.env.agent.drv", "value", value);    // example 2
// sharing resources to all the components under agent
uvm_config_db#(int t)::set(this, "uvm_test_top.env.agent*", "value", value);       // example 3
```

In the first example, the resource is made available to all components in the testbench. The wildcard `"*"` ensures that any component, regardless of its position in the hierarchy, can retrieve the resource.

In the second example, the resource is constrained based on the hierarchical scope. Here, only the driver component will be able to retrieve the data. For the `get` method to work correctly, the `inst_name` must match exactly to the `set` method to retrieve the resource.

In the third example, any component under the agent hierarchy will be able to retrieve the resource. The wildcard `"*"` is used again to ensure that all components under the agent hierarchy can access the resource.


## Reference {#reference}

1.  [Universal Verification Methodology (UVM) 1.2 User's Guide](https://accellera.org/images/downloads/standards/uvm/uvm_users_guide_1.2.pdf)
2.  [Universal Verification Methodology (UVM) 1.1 Class Reference](https://accellera.org/images/downloads/standards/uvm/UVM_1.1_Class_Reference_Final_06062011.pdf)
3.  [Universal Verficiation Methodology Cookbook](https://verificationacademy.com/resource/128026c9-49b3-3eb8-92a4-08373425cd36)
