+++
title = "Shallow Copy and Deep Copy"
author = ["Kiran"]
date = 2024-07-26T09:41:00-04:00
tags = ["sv"]
draft = false
css = "../../zcustom.css"
+++

[SV Verification Directory]({{< relref "2024_06_27_16_53_00_sv_verification_directory.md" >}})


## Shallow Copy and Deep Copy {#shallow-copy-and-deep-copy}

SystemVerilog provides two means of copying or duplicating objects to keep a method from modifying the original. The two methods are shallow copy and deep copy, and understanding these methods is crucial for effective memory management.


### Shallow Copy {#shallow-copy}

Shallow copy is a method where the objects are duplicated to a new memory location. If the class contains a handle to another class, only the handle's value is copied not the lower level object, meaning both the original and copied object share the same instances of nested objects.


#### Class assignment and Shallow Copy {#class-assignment-and-shallow-copy}

| Class Assignment                                | Shallow Copy                                                             |
|-------------------------------------------------|--------------------------------------------------------------------------|
| obj1 = obj2                                     | obj1 = new obj2                                                          |
| Both the objects point to the same memory       | Allocates memory, copies the variable, and returns the memory handle     |
| Changes made to any object will reflect in both | Changes made to the copied objects will not reflect in the parent object |


#### Example Code {#example-code}

The example shown below shows the example of shallow copy and the **limitation** of nested objects in shallow copy. Along with the difference in class assignment and shallow copy.

```verilog
// class
class grade;
  string mrk;

  function new (string mrk);
    this.mrk = mrk;
  endfunction

endclass

// class
class name;
  string student;
  grade mrk;                  // class handle

  function new (string student, string mrk);
    this.student = student;
    this.mrk = new(mrk);      // construct the object
  endfunction

endclass



module shallow_copy;

  name std1, std2, std3;	  // class handle

  initial begin
    std1 = new("John", "B"); // construct
    $display("------------------------- Display -------------------------");
    $display("STD1 => Student Name = %s; Grade = %s", std1.student, std1.mrk.mrk);
    std2 = new std1;         // shallow copy
    $display("---------------------- Shallow Copy -----------------------");
    $display("STD2 => Student Name = %s; Grade = %s", std2.student, std2.mrk.mrk);
    $display("------------------ Change Name and Grade ------------------");
    std2.student = "Noah";
    std2.mrk.mrk = "A";
    $display("STD2 => Student Name = %s; Grade = %s", std2.student, std2.mrk.mrk);
    $display("---------------- Limitation of Shallow Copy ---------------");
    $display("STD1 => Student Name = %s; Grade = %s => %s's Grade is also changed", std1.student, std1.mrk.mrk, std1.student);
    std3 = std1;
    $display("--------------------- Class assignment --------------------");
    $display("STD3 => Student Name = %s; Grade = %s", std3.student, std3.mrk.mrk);
    std3.student = "Theo";
    std3.mrk.mrk = "C";
    $display("------------------ Change Name and Grade ------------------");
    $display("STD3 => Student Name = %s; Grade = %s", std3.student, std3.mrk.mrk);
    $display("--------------------- Class assignment --------------------");
    $display("----------------- Points to the same memory ---------------");
    $display("----------------- Original is also changed ----------------");
    $display("STD1 => Student Name = %s; Grade = %s", std1.student, std1.mrk.mrk);

  end

endmodule
```

[Execute the code in EDA Playground](https://www.edaplayground.com/x/PkaX)


### Deep Copy {#deep-copy}

One method to overcome the limitation of shallow copy is to write a copy function inside the class to make a copy of the object. This method is called deep copy, which duplicates not only the object itself but also all the objects referenced by the original object, creating independent copies of these nested objects.


#### Example Code {#example-code}

```verilog
// class
class grade;
  string mrk;

  function new(string mrk);
    this.mrk = mrk;
  endfunction

  //deep copy function
  function grade copy();
    copy = new(this.mrk);
  endfunction

endclass

// class
class name;
  string student;
  grade mrk;                     // class handle

  function new(string student, string mrk);
    this.student = student;
    this.mrk = new(mrk);         // construct the object
  endfunction

  // deep copy function
  function name copy();
    copy = new(this.student, this.mrk.mrk);
  endfunction

endclass



module deep_copy;

  name std1, std2;	       // class handle

  initial begin
    std1 = new("John", "B");     // construct
    $display("------------------------- Display -------------------------");
    $display("STD1 => Student Name = %s; Grade = %s", std1.student, std1.mrk.mrk);
    std2 = std1.copy();          // deep copy
    $display("----------------------- Deep Copy -------------------------");
    $display("STD2 => Student Name = %s; Grade = %s", std2.student, std2.mrk.mrk);
    $display("------------------ Change Name and Grade ------------------");
    std2.student = "Noah";
    std2.mrk.mrk = "A";
    $display("STD2 => Student Name = %s; Grade = %s", std2.student, std2.mrk.mrk);
    $display("------------------ Advantage of Deep Copy -----------------");
    $display("STD1 => Student Name = %s; Grade = %s => %s's Grade is NOT changed", std1.student, std1.mrk.mrk, std1.student);
  end

endmodule
```

[Execute the code in EDA Playground](https://www.edaplayground.com/x/vce5)


### Point to Remember {#point-to-remember}


#### Shallow Copy: {#shallow-copy}

-   Copies the object’s attributes but not the nested objects.
-   Shared references to nested objects, leading to potential side effects.
-   Faster to create but less independent.


#### Deep Copy: {#deep-copy}

-   Copies the object and all nested objects, creating entirely independent instances.
-   No shared references, avoiding unintended side effects.
-   More computationally intensive but safer for independent object manipulation.
