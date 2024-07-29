+++
title = "Four Pillars of OOP"
author = ["Kiran"]
date = 2024-07-29T10:30:00-04:00
tags = ["sv"]
draft = false
css = "../../zcustom.css"
+++

[SV Verification Directory]({{< relref "2024_06_27_16_53_00_sv_verification_directory.md" >}})


## Four Pillars of OOP {#four-pillars-of-oop}

The are four pillars of Object-Oriented Programming (OOP) are:

1.  Abstraction
2.  Encapsulation
3.  Inheritance
4.  Polymorphism


### Abstraction {#abstraction}

Abstraction is the concept of hiding the complex implementation details and showing only the essential features of the object. It allows users to interact with objects through a simplified interface, without needing to understand the underlying complexity.


### Encapsulation {#encapsulation}

This principle involves bundling the data (attributes) and the methods (functions) that operate on the data into a single unit called an object. It restricts direct access to some of the object's components, which is a means of preventing unintended interference and misuse of the data.


### Inheritance {#inheritance}

Inheritance allows a new class to inherit properties and methods from an existing class. This promotes code reusability and establishes a relationship between the parent (superclass) and child (subclass) classes.


### Polymorphism {#polymorphism}

Polymorphism enables objects to be treated as instances of their parent class rather than their actual class. It allows one interface to be used for a general class of actions, with specific behavior being determined at runtime. This can be achieved through method overriding (runtime polymorphism) and method overloading (compile-time polymorphism).

These concepts are intricately integrated into SystemVerilog to ease the process of verification.
