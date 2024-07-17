+++
title = "Credit-Based Flow Control"
author = ["Kiran"]
date = 2024-07-17T09:17:00-04:00
tags = ["article"]
draft = false
css = "../../zcustom.css"
+++

Flow control is a crucial synchronization technique for data transmission. It ensures the efficient flow of data between the transmitter and the receiver by maintaining a balance between the data production rate of the sender and the data consumption rate of the receiver. The data that is being transmitted is buffered to maintain the balance between the transmitter and the receiver. The control mechanism holds the intermediate data in the buffer until the receiver is ready to process the data. The most commonly implemented control mechanism in hardware is "Valid/Ready Handshake Protocol", where the sender asserts a "valid" signal when the data is ready to be transmitted, the receiver asserts a "ready" signal when it is ready to receive data, data transfer occurs only when both valid and ready signals are asserted. This mechanism was studied in detail in a pipeline design example in [A Case Study on Effective Pipeline Design in Digital Systems]({{< relref "2024_06_18_16_45_29_a_case_study_on_effective_pipeline_design_in_digital_systems.md" >}}). Here, we will look at a flow control mechanism that is mostly implemented in Network-on-Chips (NoCs) called credit-based flow control.


## Credit-Based Flow Control {#credit-based-flow-control}

Credit-based flow control is a mechanism where credits (counter value) are provided to the sender based on the receiver's buffer size to prevent data loss and overflow. The sender transmits data to the receiver, and each time the sender transmits the data, the credit counter is decremented by one. The sender will transmit data only when the credit counter is not zero. On the receiver side, the data is received by the buffer. The receiver pops the data from the buffer when the receiver is ready to process the data. When the receiver pops the data, the receiver frees up the buffer space and sends a credit back to the sender, which increments the sender's credit counter.


### Pipeline Module {#pipeline-module}

The pipeline module is implemented from the [article](https://verilog-meetup.com/2024/06/16/focus-on-microarchitecture/). The discussion presented here is an extension of the pipeline module with valid/ready and backpressure from this [article](https://verilog-meetup.com/2024/06/20/a-case-study-on-effective-pipeline-design-in-digital-system/). The control signals for the implemented pipeline module consists of only "valid" signal, the "ready" signal is stripped off from the pipeline design since a FIFO is implemented at the receiver to capture the transmitted data. The pipeline module will process the data as long as the "valid" is high.


##  {#d41d8c}

The figure below shows the implemented credit-based flow control with the pipeline module.

{{< figure src="/ox-hugo/credit_based_flow2.png" >}}

A FIFO buffer is implemented at the receiver's side with the size of the pipeline depth. At the start, the sender is informed of the depth size of the receiver. The "up_vld" is asserted when the data is ready to be transmitted. The "up_rdy" is asserted as long as the credit counter is not zero. The sender transmits the data only when up_vld and up_rdy are set high. The sender does not transmit the data when the credit counter is zero. At the receiver side, the FIFO receivers the transmitted data. The data from the FIFO is valid as long as the FIFO is not empty, indicating that there is data in the FIFO buffer. The "dwn_vld" signal is asserted when the FIFO is not empty. The "dwn_rdy" signal is set high when the receiver is ready to process the data. Data from the FIFO is popped when the FIFO is not empty and when the receiver is ready to process the data. When the receiver pops the data from the FIFO buffer, a credit signal is sent to the sender to increment the credit counter indicating that there is buffer space available for the sender to send more data. If the sender has sent all its credits and has not received new credits yet, it must wait. As soon as it receives new credits, it will resume sending data.


### Perks {#perks}

1.  Efficient hardware utilization when compared to valid/ready control.
2.  Performance of the design can be increased with increase buffer size.


## Reference {#reference}

1.  [Padua, David, ed. Encyclopedia of parallel computing. Springer Science &amp; Business Media, 2011.](https://books.google.com/books?hl=en&lr=&id=Hm6LaufVKFEC&oi=fnd&pg=PR1&dq=encyclopedia+of+parallel+computing&ots=uGzOcPBfmT&sig=YwwAkUdRf4b50_MPzZSKh49w1mE#v=onepage&q=encyclopedia%20of%20parallel%20computing&f=false)
