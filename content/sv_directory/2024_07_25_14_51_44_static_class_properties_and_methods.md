+++
title = "Static Class Properties and Methods"
author = ["Kiran"]
date = 2024-07-25T14:51:00-04:00
tags = ["sv"]
draft = false
css = "../../zcustom.css"
+++

[SV Verification Directory]({{< relref "2024_06_27_16_53_00_sv_verification_directory.md" >}})


## Static Class Properties and Methods {#static-class-properties-and-methods}

Every object has its own local variables that are not shared with any other object. When two objects of Ex_class [Class]({{< relref "2024_07_24_16_47_44_class.md" >}}) is created, each object with have its own class properties(variables). In certain instances, a specific variable is required to be shared by all objects of a certain type. Declaring a global variable will make the variable visible to the entire testbench. With the OOP, a static variable can be created inside a class. This variable is shared amongst all the instances of the class.

-   A **static** keyword is used in a class member to denote a class that has static properties or static methods.
-   The static variable declared inside a class with the static keyword shares a single memory location across all the class instances.
-   The static variable implies that only one copy exists.
-   Static methods are the same as static variables that follow class access rules and scope.
-   Static functions and tasks cannot be virtual.
-   Non-static class members can not be accessible from the static method. A static method should consist of only static variables.
-   Non-static functions or tasks can access static variables.
-   Both static methods and static members in a class can be accessed without creating an object.


#### Syntax: Static Variable {#syntax-static-variable}

```verilog
static {data_type} {variable_name};
```


#### Syntax:Static Method {#syntax-static-method}

```verilog
static {task/function} {name}();
```


### Difference between Static Function and Function Static {#difference-between-static-function-and-function-static}

| Static Function                                 | Function Static                                |
|-------------------------------------------------|------------------------------------------------|
| Can access only static variables                | Can access both static and automatic varibales |
| **this** and **super** keyword _cannot_ be used | **this** and **super** keyword _can_ be used   |

```verilog
// static function example
static funtion void disp();
   // execute code
endfunction

//function static example
function static void disp();
   // execute code
endfunction
```
