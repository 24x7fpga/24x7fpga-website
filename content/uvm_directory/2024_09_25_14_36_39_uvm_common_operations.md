+++
title = "UVM Common Operations"
author = ["Kiran"]
date = 2024-09-25T14:36:00-04:00
tags = ["uvm"]
draft = false
css = "../../zcustom.css"
+++

[Source](https://github.com/24x7fpga/UVM/blob/main/uvm_verification/cmn_op/tb_cmn_op.sv) -- [UVM Framework Directory]({{< relref "2024_08_28_12_39_50_uvm_framework_directory.md" >}})

The `uvm_object` provides a set of predefined methods for common operations.

| UVM Method     | Virtual Method | Description                                                 |
|----------------|----------------|-------------------------------------------------------------|
| copy           | do_copy        | Performs a deep copy of an object                           |
| clone          | do_copy        | creates a new object and performs a deep copy of the object |
| compare        | do_compare     | Compares one object  to another of the same dat type        |
| print          | do_print       | Prints the result of convert2string to the terminal         |
| sprint         | do_print       | Returns the result fo the convert2string                    |
| convert2string | -              | Returns a string representation of the object               |


## copy {#copy}

This makes deep copy of a specified object. The copy method is `not virtual` and should `not be overloaded (override)` in derived classes. This design choice is made to maintain consistent copying behaviour among all the objects.


#### Example Code {#example-code}

```verilog
my_obj obj1, obj2;
obj1 = my_data::type_id::create("obj1");
obj2 = my_data::type_id::create("obj2");

obj1.copy(obj2); // copies data from obj2 into obj1
```


## clone {#clone}

Similar to copy method, clone method performs a deep copy of the object without the need for an object constructor. This is particularly useful when you want to generate copies of UVM sequence items, transactions, or any UVM objects while ensuring that the new instance retains the same properties as the original. The default implementation calls `create` followed by `copy`.  As clone is `virtual`, derived classes may override this implementation if desired.


#### Example Code {#example-code}

```verilog
my_obj obj1, obj3;
obj1 = my_data::type_id::create("obj1");

$cast(obj3, obj1.clone()); // copies data from obj1 into obj3
```


## do_copy {#do-copy}

The do_copy method serves as a user-definable hook that is invoked by the copy method. This method is essential for implementing custom copying behavior for objects derived from the base UVM classes. The implementation must call `super.do_copy`, and it must `$cast` the rhs argument to the derived type before copying.


#### Example Code {#example-code}

```verilog
class copy_ex extends uvm_object;
   // factory registration
   `uvm_object_utils(copy_ex)

   // variables
   rand bit [3:0] data1;
   rand bit [7:0] data2;

   //construct class
   function new(string path = "copy_ex");
      super.new(path);
   endfunction

   //do copy method
   virtual function void do_copy(uvm_object rhs);
      copy_ex temp;               // object handle
      $cast(temp, rhs);           // checks for type match
      super.do_copy(rhs);

      this.data1 = temp.data1;
      this.data2 = temp.data2;
  endfucntion

endfunction
```


## compare {#compare}

Deep comparison is essential for verifying the integrity and accuracy of data objects. A method designed for this purpose compares the members of the current data object with those of another object provided as the right-hand side (rhs) argument.When performing a deep comparison, the method will return a value of 1 if all corresponding members match perfectly, indicating a successful comparison. Conversely, it will return 0 if there are any discrepancies between the objects, signaling that the objects do not match. All the the data objects must be registered using `uvm_object_utils_begin` and `` `uvm_object_utils_end `` to use the compare mechanism that is avaiable out of the box in UVM. Again, this a `non-virtual` and `non-overriding` method.


#### Example Code {#example-code}

```verilog
status = obj1.compare(obj2);
$display("Compare = %0d", status)
```


## do_compare {#do-compare}

The do_compare method is user defined hook that is called using the `compare()` method. The data objects need not be explicitly registered using `uvm_object_utils_begin` and `` `uvm_object_utils_end ``. A derived class implementation must call `super.do_compare()` to ensure its base class’ properties are included in the comparison and must `$cast` the rhs argument to the derived type before comparing.


#### Example Code {#example-code}

```verilog
class comp_ex extends uvm_object;
   // factory registration
   `uvm_object_utils(comp_ex)

   // variables
   rand bit [9:0] comp_data;

   //construct class
   function new(string path = "comp_ex");
      super.new(path);
   endfunction

   //do copy method
   virtual function bit do_compare(uvm_object rhs, uvm_comparer comparer);
      int status;                 // variable to hold the result
      comp_ex temp;               // object handle
      $cast(temp, rhs);           // checks for type match
      status = super.do_compare(rhs, comparer) && (comp_data == this.comp_data);
  endfucntion

endfunction
```


## print {#print}

The print method deep-prints the object’s properties in a format and manner specified by the given printer argument; if the printer argument is not provided, the global uvm_default_printer is used. The print method is `non -virtual` and must `not be overloaded`.


#### Example Code {#example-code}

```verilog
obj1.print();
```


## do_print {#do-print}

The do_print method is the user-definable hook called by print and sprint that allows users to customize what gets printed or sprinted beyond the field information provided by the \`uvm_field_\* macros, Utility and Field Macros for Components and Objects.


#### Example Code {#example-code}

```verilog
   // variables
   rand bit [3:0] data1;
   rand bit [7:0] data2;

virtual function void do_print(uvm_printer printer);
   super.do_print(printer);
   printer.print_field_int("data1", data1, $bits(data1), UVM_HEX);
   printer.print_field_int("data2", data2, $bits(data2), UVM_BIN);
endfunction // do_print
```


## convert2string {#convert2string}

This virtual function is a user-definable hook, called directly by the user, that allows users to provide object information in the form of a string. There is no requirement to use a uvm_printer policy object because of which the format and content of the output is fully customizable.


#### Example Code {#example-code}

```verilog
virtual function string convert2string();
    string str = super.convert2string();
    str = {str, $sformatf("data1 = %0d, ", data1)};
    str = {str, $sformatf("data2 = %0d, ", data2)};
    return str;
   endfunction // convert2string
```


## Reference {#reference}

1.  [Erickson, Adam. "Are ovm &amp; uvm macros evil? a cost-benefit analysis." Proceeding of Design and Verification Conference (DVCON). 2011.](https://dvcon-proceedings.org/wp-content/uploads/are-ovm-uvm-macros-evil-a-cost-benefit-analysis.pdf)
2.  [Universal Verification Methodology (UVM) 1.2 Class Reference](https://accellera.org/images/downloads/standards/uvm/UVM_Class_Reference_Manual_1.2.pdf)
