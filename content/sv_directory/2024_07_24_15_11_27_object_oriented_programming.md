+++
title = "Object-Oriented Programming"
author = ["Kiran"]
date = 2024-07-24T15:11:00-04:00
tags = ["sv"]
draft = false
css = "../../zcustom.css"
+++

[SV Verification Directory]({{< relref "2024_06_27_16_53_00_sv_verification_directory.md" >}})


## Object-Oriented Programming {#object-oriented-programming}

Object-oriented Programming(OOP) language model for software design that revolves around objects or data instead of logic and functions. It focuses on what the developers want to manipulate rather than how they manipulate them. This approach is well suited for large, complex, and actively updated and maintained.


### OOP for Verification {#oop-for-verification}

Typically, a testbench creates a transaction i.e., generates the necessary input for the DUT based on the defined rules, transmits it to the DUT, receives the response from the DUT, verifies the response against the golden reference, and generates a report. Grouping all the processes together helps in creating and maintaining large testbenches. However, while verifying a large design the process of detecting a bug in the testbench becomes tedious and complex. Also, when verifying a bus transaction, multiple arrays are required to store data and addresses. For N transactions, N\*(multiple arrays) is required. Moreover, these arrays are static, meaning they are allocated at the compile time with a fixed array size, if the verification requires any additional arrays during run time, changes are needed to be made in the source code manually. To overcome these drawbacks, concepts from OOP is applied in SystemVerilog to improve the design's modularity and reusability.

The testbench is divided into blocks and with the help of OOP they communicate with each other. The generator creates transactions and passes them to the next level, The driver sends the transactions to the design. The monitors accept the response. The scoreboard checks the response against the golden reference.


#### Advantages of OOP {#advantages-of-oop}

1.  Modularity: The testbench can be divided into blocks with the help of classes and maintained independently of the source code for other classes.
2.  Reusability: Multiple objects can be constructed once a class is written.
3.  Extensibility: New functionality can be added to an existing class without modifying the underlying code.
4.  Maintainability: Easy and simple to maintain and modify because of OOP's modular structure.


### Nomenclature {#nomenclature}

1.  [Class]({{< relref "2024_07_24_16_47_44_class.md" >}}): is a basic building block containing routines and variables. This is analogous to a **module** in Verilog.
2.  [Handle]({{< relref "2024_07_24_22_09_00_handle.md" >}}): is a **pointer** to the object. It acts as a pointer or link to the actual object stored in memory.
3.  [Object]({{< relref "2024_07_24_17_42_06_object.md" >}}): is an instance of a class. Similar to **instantiating** a module in Verilog.
4.  Property: is a variable that holds data. In Verilog, this is a signal such as a **register** or **wire**.
5.  Method: is the procedural code that manipulates variables, contained in tasks and functions. Similar to **initial** and **always** blocks, in Verilog.
6.  Prototype: the header of a routine that shows the name, type, and argument list plus return type.

In Verilog, you build complex designs by creating modules and instantiating them hierarchically. In OOP you create classes and construct them (create objects) to create a similar hierarchy.

Note: Modules are instantiated during compilation while classes are constructed at run time.


### Reference {#reference}

1.  [Spear, Chris. SystemVerilog for verification: a guide to learning the testbench language features. Springer Science &amp; Business Media, 2008.](https://link.springer.com/book/10.1007/978-1-4614-0715-7)
