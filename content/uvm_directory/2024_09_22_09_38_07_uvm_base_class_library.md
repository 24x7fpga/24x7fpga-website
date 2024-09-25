+++
title = "UVM Base Class Library"
author = ["Kiran"]
date = 2024-09-22T09:38:00-04:00
tags = ["uvm"]
draft = false
css = "../../zcustom.css"
+++

[UVM Framework Directory]({{< relref "2024_08_28_12_39_50_uvm_framework_directory.md" >}})

The base class library in UVM serve as the building blocks for any testbench and verification environment. These classes provide reusable, modular components that help create a structured and scalable framework, ensuring efficiency and consistency across various test environments.

{{< figure src="/ox-hugo/uvm_base_class.svg" caption="<span class=\"figure-number\">Figure 1: </span>UVM Base Class" >}}

The UVM class library offers a wide range of built-in features for verification, including printing, copying, test phases, and more. Each component in the testbench architecture can be derived from the corresponding UVM class, which enhances readability and reduces development time, making the verification process more efficient and streamlined.

The complete list of class libraries can be found in [UVM 1.2 Class Reference](https://accellera.org/images/downloads/standards/uvm/UVM_Class_Reference_Manual_1.2.pdf) guide.


## Core Base Class {#core-base-class}

-   **uvm_void**: The uvm_void serves as the base class for all UVM classes. This abstract class contains no data members or functions, but it plays an essential role in allowing the creation of generic containers for objects, much like a void pointer in C. It provides the flexibility to store various object types within the UVM framework, enabling greater versatility in verification environments.

-   **uvm_object**: All UVM classes (components or transactions) inherit from uvm_object. It provides basic functionality like copying, printing, comparing, and packing/unpacking objects. Some of the important generic methods are: create(), copy(), compare(), print(), record(), pack(), unpack().

-   **uvm_component**: The uvm_component is the root class for all the component classes. It is a quasi-static object menaing that exist throughout the simulation. Components are addresible via a hierarchical path name. Components can have phases (such as build, connect, run) and are typically instantiated as static objects.

-   **uvm_transaction**: The uvm-transaction is extended from uvm_object, is the root class for all the UVM transaction and transient in nature meaning. It includes timing and event recording, which is essential for transaction-level modeling.


## Extended Classes {#extended-classes}


### Extended from "uvm_transaction" {#extended-from-uvm-transaction}

-   **uvm_sequence_item**: An extension of uvm_transaction designed to represent individual transactions in a sequence. Sequence items are created and sent to drivers via sequencers, facilitating the flow of data during simulations. Key methods include randomize(), do_copy(), do_compare(), do_pack(), and do_unpack(), which help in generating randomized data, copying items, and serializing objects.

-   **uvm_sequence**: This class models a collection of uvm_sequence_item objects and is responsible for controlling the generation of stimuli and transaction flow to drivers. Sequences can be hierarchical, where a parent sequence triggers multiple child sequences, enabling complex stimulus scenarios. Important methods include start(), which begins the sequence, body(), which defines the sequence logic, and wait_for_grant(), which handles sequence arbitration.


### Extended from "uvm_component" {#extended-from-uvm-component}

-   **uvm_sequencer**: The sequencer manages the execution of sequences and handles arbitration when multiple sequences are running. It communicates with drivers by sending sequence items for processing. Common methods include get_next_item() and send_item().

-   **uvm_driver**: The driver takes transactions from the sequencer and applies stimulus to the Design Under Test (DUT). It processes sequence items and drives the DUT accordingly. Key methods are get_next_item() and drive().

-   **uvm_monitor**: A monitor passively observes signals from the DUT, collecting data for functional coverage, analysis, and checking. Unlike the driver, it does not apply stimulus but generates events for use by other components like checkers and scoreboards.

-   **uvm_agent**: An agent groups the driver, sequencer, and monitor into a single unit. It can operate in an active mode (containing a driver) or a passive mode (only monitoring signals).

-   **uvm_scoreboard**: The scoreboard is responsible for collecting data from the DUT and comparing it to reference models to verify correctness. It plays a vital role in functional checking.

-   **uvm_env**: This is a container class used to build larger, hierarchical environments. An environment typically consists of multiple agents, scoreboards, and other components, organizing the testbench structure.

-   **uvm_test**: The top-level test class in UVM. Each specific test inherits from uvm_test, configuring and running the verification environment with targeted stimulus to meet the verification objectives.
