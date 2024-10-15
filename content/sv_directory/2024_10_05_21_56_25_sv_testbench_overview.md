+++
title = "SV Testbench Overview"
author = ["Kiran"]
date = 2024-10-05T21:56:00-04:00
tags = ["sv"]
draft = false
css = "../../zcustom.css"
+++

[Source](https://github.com/24x7fpga/SystemVerilog_Verification/tree/main/sv_verification/half_adder) -- [SV Verification Directory]({{< relref "2024_06_27_16_53_00_sv_verification_directory.md" >}})

A layered testbench is a modular verification environment where the components are divided into specific layers, with each layer responsible for a distinct task. This structured approach ensures better organization, reusability, and scalability, making it easier to manage complex verification environments. The typical components of a SystemVerilog testbench are illustrated in Figure 1.

{{< figure src="/ox-hugo/sv_testbench_overview.svg" caption="<span class=\"figure-number\">Figure 1: </span>Testbench Environment" >}}

Let’s explore the SystemVerilog verification components using a `Half Adder` as an example.


### Top {#top}

The top module acts as a wrapper that connects the Design Under Test (DUT) to the testbench environment, ensuring seamless communication between the two.

<!--list-separator-->

-  Example

    ```verilog
    module tb_half_adder;

      /*AUTOWIRE*/
      // Beginning of automatic wires (for undeclared instantiated-module outputs)
      wire			c;			// From DUT of half_adder.v
      wire			s;			// From DUT of half_adder.v
      // End of automatics

      intf i_intf();	//interface handle

      test t1(i_intf);	//connect test class handle to interface

      half_adder DUT (/*AUTOINST*/
                      // Outputs
                      .s			(i_intf.s),
                      .c			(i_intf.c),
                      // Inputs
                      .a			(i_intf.a),
                      .b			(i_intf.b));

      initial begin
        $dumpfile("dump.vcd");
        $dumpvars;
      end

    endmodule
    ```


### Design Under Test (DUT) {#design-under-test--dut}

The Design Under Test (DUT) refers to the actual hardware design or module that is being verified within a simulation environment. The DUT is the focus of the verification process, and it can be any digital design component, such as a microprocessor, a memory block, or a custom logic circuit.

<!--list-separator-->

-  Example

    ```verilog
    module half_adder(a,b,s,c);
      input  a,b;
      output s,c;

      xor X1 (s,a,b);
      and A1 (c,a,b);

    endmodule
    ```


### Test {#test}

The test component creates an instance of the environment and configures it for the simulation. Multiple test components can be defined to handle different test cases, ensuring comprehensive verification of the DUT under various scenarios.

<!--list-separator-->

-  Example

    ```verilog
    `include "environment.sv"

    program test(intf i_intf);

      environment env;	   //class handle

      initial begin
        env = new(i_intf); //class object
        env.run();	       //class task
      end

    endprogram
    ```


### Interface {#interface}

All the signals are grouped under a single component, allowing them to be shared across different testbench components using a single reference. This approach simplifies the testbench by eliminating the need to reference each signal individually, promoting cleaner and more maintainable code.

<!--list-separator-->

-  Example

    ```verilog
    interface intf();

      logic a;
      logic b;
      logic s;
      logic c;

    endinterface
    ```


### Clocking Block {#clocking-block}

A clocking block in a testbench is used to manage the timing relationships and synchronization of a group of signals. It provides a clear and organized way to define clocking events, control the sampling and driving of signals, and ensure synchronized operations within the testbench.


### Environment {#environment}

The environment component acts as the top-module for all the key components of a verification environment. It provides a modular structure by instantiating and connecting drivers, monitors, generators, scoreboards, and interfaces.

<!--list-separator-->

-  Example

    ```verilog
    `include "transaction.sv"
    `include "generator.sv"
    `include "driver.sv"
    `include "monitor.sv"
    `include "scoreboard.sv"

    class environment;

      generator		gen;	//create the handle
      driver		drv;
      monitor		mon;
      scoreboard	scb;

      mailbox m1;			//mailbox : Generator to Driver
      mailbox m2;			//mailbox : Monitor to Scoreboard

      event ev;

      virtual intf vif;

      function new (virtual intf vif);
        this.vif = vif;

        m1 = new();		//create the mailbox for connection
        m2 = new();

        gen = new(m1);
        drv = new(vif,m1);
        mon = new(vif,m2);
        scb = new(m2);

        gen.ev = ev;    //connect event from generator class
        scb.ev = ev;    //to scoreboard class
      endfunction


      task test();		//performs the task in the classes
        fork
          gen.main();
          drv.main();
          mon.main();
          scb.main();
        join_any
      endtask

      task run();		//main which calls test() task
        test();
        $finish;
      endtask

    endclass
    ```


### Generator {#generator}

The generator is key component responsible for creating stimulus or transactions that drive the DUT. It can generate both randomized and directed inputs. The generator is typically connected to the driver, which sends the generated stimulus to the DUT.

<!--list-separator-->

-  Example

    ```verilog
    class generator;

      event ev;              // Create event to wait until the scr is verified the transaction

      mailbox gen2drv;       //Create a mailbox: sending info from Generator to Driver

      transaction trans;     //Create an handle of the transaction class

    //Create a constructor of generator class
    //"new" will create a memory for  variable for gen2drv and initialize
    //them with their default values and return the address to generator handle
      function new(mailbox gen2drv);
        trans = new(); 					//object for transaction class
        this.gen2drv = gen2drv; 		//info from the outside class is passed to the
      endfunction						//gen2drv present inside this class

      task main();
        repeat(10)
          begin
            assert(trans.randomize)
              else
            $error("GEN: Randomization Failed");  //randomize the transaction and verify if is randomized
            //trans.randomize();			      //randomize the transaction
            trans.DISP("GEN");	                  //Display the values
            gen2drv.put(trans);			          //put them in the mailbox

            @(ev);                                // wait for the event to be triggered in scoreboard class
          end
      endtask

    endclass
    ```


### Driver {#driver}

The driver component is responsible for translating the stimulus generated by the generator into actual signal activity on the DUT. It interacts with the DUT's via interface (virutal interface) and ensures that the signals are driven correctly according to the specified protocols.

<!--list-separator-->

-  Example

    ```verilog
    class driver;

      virtual intf vif;			//vif is the handle of virtual interface

      mailbox gen2drv;			//handle of the mailbox

      transaction trans;	    //handle of transaction class, to get the data from mailbox

      function new (virtual intf vif, mailbox gen2drv);
        this.vif = vif;
        this.gen2drv = gen2drv;
      endfunction

      task main;
        forever
          begin
            gen2drv.get(trans); //receive the info from generator

            vif.a <= trans.a;	//sample the input for the virtual interface
            vif.b <= trans.b;

            trans.s = vif.s;	//sample the output from the virtual interface
            trans.c = vif.c;	//sampling the output may not be required here

            trans.DISP("DRV");
          end
      endtask

    endclass
    ```


### Monitor {#monitor}

The monitor component is a vital part of the verification environment responsible for observing and collecting data from the DUT. The monitor simply observes and records the signals for later verification.

<!--list-separator-->

-  Example

    ```verilog
    class monitor;

      virtual intf vif;		//declare virtual interface

      mailbox mon2scb;		//declare mailbox to transfer info from monitor to scoreboard

      transaction trans;	//handle for transaction class

      function new (virtual intf vif, mailbox mon2scb);
        this.vif = vif;
        trans = new();
        this.mon2scb = mon2scb;     //constructor or create an object for transaction class

      endfunction

      task main;
        forever
            begin
              #3;				    //sample after 3 ns

              trans.a = vif.a;		//send the data from virtual interface to monitor
              trans.b = vif.b;

              trans.s = vif.s;
              trans.c = vif.c;

              mon2scb.put(trans);	//put the info to send to scoreboard

              trans.DISP("MON");
        end
      endtask

    endclass
    ```


### Scoreboard {#scoreboard}

The scoreboard component in the verification environment validates the outputs of the DUT against the expected results. It acts as a comparison mechanism, collecting data from the DUT and comparing it to the expected outcomes to determine if the DUT is functioning correctly.

<!--list-separator-->

-  Example

    ```verilog
    class scoreboard;

      event ev;            //Create event to trigger the next stimulus in generator class

      mailbox mon2scb;     //Create a mailbox: sending info from Monitor to Scroeboard

      transaction trans;   //Create an handle of the transaction class

      function new (mailbox mon2scb);
        this.mon2scb = mon2scb;
      endfunction

      task main;
        forever
          begin
            mon2scb.get(trans);

            trans.DISP("SCR");

            if( ((trans.a ^ trans.b) == trans.s) && ((trans.a & trans.b) == trans.c) )
              $display("Verification Passed! :)");
            else
              $error("Verification Failed! :(");

            $display("-----------------------");
            ->ev;         //trigger an event to proceed to the next stimulus
          end
      endtask

    endclass
    ```


##  {#d41d8c}

[Execute the code in EDA Playground](https://www.edaplayground.com/x/qt53)
