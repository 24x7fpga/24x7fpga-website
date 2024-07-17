+++
title = "A Case Study on Effective Pipeline Design in Digital Systems"
author = ["Kiran"]
date = 2024-06-18T16:45:00-04:00
tags = ["article"]
draft = false
css = "../../zcustom.css"
+++

[Published Article](https://verilog-meetup.com/2024/06/20/a-case-study-on-effective-pipeline-design-in-digital-system/)

Throughput and latency are fundamental concepts in moderm digital system. Throughput refers to the time the system takes to process the data per clock cycle where latency refers to the time it takes for the data to travese from one location to another to complete an operation. High throughput and low latency are essentail for today's fast-paced digital world. For example, higher throughput ensure smooth and uninterrupted audio and video streaming and low latency provides seamless and responsive gaming experience reducing lag. It is necessary to strike a balance between high throughput and low latency. By processing more data simultaneously, you can boost throughput, but only if the system can handle the additional load. Therefore, digital designers need to find the right balance depending on the specific requirement for the system. The two most commonly implemented statergies to improve throughput is pipeling and parallel processing.


### Pipelining {#pipelining}

Pipelining is a process of breaking down a complex task into multiple smaller tasks grouped into stages. A task in between stages is execute in a linear fashion allowing data to flow from one stage to the other contributing to the overall task.


### Parallel Processing {#parallel-processing}

Parallel processing involves dividing a task into smaller sub-tasks that can be process simultaneouly in parallel.


## Basic Pipelining {#basic-pipelining}

Two fundamental control signals, "valid/ready", play a critical role in managing data flow and ensuring smooth operation in pipelined systems. Valid signal indicates the current data on the pipeline is valid for processing in the concurrent stage, hence, the valid signal is asserted high when the data is ready for the next stage. Ready signal indicates that the next stage in the pipeline is ready to accept the data from the current stage, hence, the next stage asserts the ready signal high when its is ready to receive data from the previous pipeline stage. The ready/valid signal work in conjuction to ensure smooth flow of data in the pipeline when both valid and ready signals are asserted. This ensures that data is transfered only when the next stage is prepared to process it, preventing data loss or corruption.

<a id="figure--Figure 1: Pipeline Stage with Valid-Ready Signals"></a>

{{< figure src="/ox-hugo/Pipeline2.svg" caption="<span class=\"figure-number\">Figure 1: </span>Pipeline Stage with Valid/Ready Signals" class="center !important" width="500px" >}}

<a id="figure--Figure 2: Waveform depecting Valid-Ready Assertions"></a>

{{< figure src="/ox-hugo/vld_rdy.svg" caption="<span class=\"figure-number\">Figure 2: </span>Valid/Ready Assertions" class="center !important" >}}


## Pipeline with Upstream and Downstream Signals {#pipeline-with-upstream-and-downstream-signals}

<a id="figure--Figure 3: Pipeline stage"></a>

{{< figure src="/ox-hugo/Pipeline.svg" caption="<span class=\"figure-number\">Figure 3: </span>Pipeline stage with Valid and Read signals" class="center !important" width="800px" >}}

When designing a pipeline stage is necessary to define an understanding nomenclature to aviod any confusion. Generally, inputs to the pipeline stage are referred to as upstream signals and the outputs from the pipeline stage are called downstream signals. For the purposes of simplicity, since the data flows is from input to output (left to right), all the signals to the left of the pipeline stage is labled with a suffix "up_" and the all the signals to the right of the pipeline stage is labeled with a suffix "dwn_". The two control signals valid and ready are labeled as "vld" and "rdy" respectively.

Interface signals of the nth pipeline stage among N pipeline stages:

1.  Upstream

2.  up_data: input data from the (n-1)th pipeline stage.
3.  up_vld: input signal indicating that the data on the up_data line is valid and ready for processing.
4.  up_rdy: output signal to the previous (n-1)th stage to indicate the pipeline stage is ready to receive new data.

5.  Downstream

6.  dwn_data: output to the (n+1)the pipeline stage from the nth pipelined stage after processing.
7.  dwn_vld: output signal indicating that the data on the dwn_data line is valid for the next (n+1)th pipeline stage.
8.  dwn_rdy: input signal from the (n+1)th stage indicating that it is ready to receive the new data.

When up_vld is high and up_rdy is asserted the up_data is latched into the pipeline stage. Similarly, when dwn_vld is high and dwn_data is asserted the data stored in the pipeline is transfered onto the dwn_data signal.


## What is Back Pressure? {#what-is-back-pressure}

When the upstream stage (producer) generates data at a faster rate than the downstream stage (consumer) to accept the data, there will be data loss as the consumer is unable to accept the data at the rate at which it is being produced. To ensure no data loss, a control mechanism called backpressure is introduced so that the upstream stages does not generate a new data until the downstream is ready to accept new data. This mechanism is crucial for maintaining system stability and prevent data loss. Buffers are used to store data temporaily between stages to prevent data loss. Backpressure involves a feedback mechanism to continously monitor each stages, the downstream stages will signal upstream stages to stop data production until when backpressure occurs.


## Kinds of Buffers {#kinds-of-buffers}

In pipeline design, buffers are essential components for managing data flow and implementing backpressure. They temporarily store data between stages to prevent data loss and ensure smooth operation. Different types of buffers are utilized for various purposes, each with unique characteristics and use cases. Here are the few types of buffers used for backpressure in pipeline design:


### Global Stall {#global-stall}

The entire pipeline is halted or stalled when a global stall occurs. The data does not flow through the pipeline until the consumer is ready to accept the new data. Figure 4, shows logic for global stall.

<a id="figure--Figure 4: Global Stall"></a>

{{< figure src="/ox-hugo/Global_Stall.svg" caption="<span class=\"figure-number\">Figure 4: </span>GLobal Stall Logic" class="center !important" width="350px" >}}


### Half-Performance Buffer {#half-performance-buffer}

Half performance buffer or more generally know as the Half rate buffer, where each stage of the pipeline takes two clock cycles to process the data instead of one. Since each pipeline takes two clock cycles to process the data, there is more time available to process the date in between the pipeline stages. Half rate buffer increases latency and reduces throughput.

<a id="figure--Figure 5: Half-performance buffer"></a>

{{< figure src="/ox-hugo/Half_Performance_Buffer.svg" caption="<span class=\"figure-number\">Figure 5: </span>Half-Performance Buffer" class="center !important" width="400px" >}}


### Skid Buffer {#skid-buffer}

Skid buffer also know as double buffer is a technique used in pipeline design where two storage elements are used to store data to improve performance and throughput.

<a id="figure--Figure 6: Skid Buffer"></a>

{{< figure src="/ox-hugo/Skid_Buffer.svg" caption="<span class=\"figure-number\">Figure 6: </span>Skid Buffer" class="center !important" width="500px" >}}


### Two-depth FIFO {#two-depth-fifo}

Two-depth FIFO is a simple FIFO with only two depths. If there is a stall then the second storage location in FIFO is used to store data.

<a id="figure--Figure 7: 2-Depth FIFO"></a>

{{< figure src="/ox-hugo/2_depth_FIFO.svg" caption="<span class=\"figure-number\">Figure 7: </span>2-Depth FIFO" class="center !important" width="375px" >}}


## Design Example {#design-example}

Let's consider the design example sqrt(A + sqrt(b + sqrt(c))) from [paper](). The square root block is designed based on the algorithm from Hacker's Delight by Henry Warren. The square root design is itself build with N stage pipeline which gives rise to a fixed latency of N clock cycles, meaning the when the input X is given to the square root design it take N clock cycles to process the data and produce an output Y. Two valid signals "x_vld" and "y_vld" indicate the presence of valid data on the input and output respectively. The figure 2, shows the waveform of the implemented square root design with N latency.

<a id="figure--Figure 7: Waveform of the Square Root Design"></a>

{{< figure src="/ox-hugo/waveform1.svg" caption="<span class=\"figure-number\">Figure 8: </span>N stage pipelined squre root design" >}}

The source code for the squre root module can be found [here](). Using this square root module to implement the pipelined sqrt(A + sqrt(B + sqrt(C))) requires the designer to carefully align the data so that there is no data loss. It is necessary to ensure the data is aligned by adding buffers to correctly perform the operation. The figure 8, shows the design with pipeline stages and the appropriate valid/ready signals at each block.

<a id="figure--Figure 8: Example Design"></a>

{{< figure src="/ox-hugo/pipeline_example.svg" caption="<span class=\"figure-number\">Figure 9: </span>Example Design" >}}

Assuming the square root block takes N clock cycles to produce the result, the buffers to align the data on the B line should have depth of N stages. Similary, on the A line the buffer stage should of depth 2\*n+1, 2\*n corresponds to two square root block and 1 corresponds to the pipeline stage between the two square root block. These buffer stages can de design using one of the four buffers described before. One of the simplest way to align the data coming from the square root block and the buffer block is to use shift register with N depth stage and 2\*N+1 deep stages. As long as N is minimum the you can get away with simple shift registers. What if the design is large and N is large? In such cases it is not advisble to use shift registers. A N-depth FIFO is more suitable is such senarios. The above example with N=4 stages is implemented with a FIFO to align the data and the perfomance of the system is tabulated below:

| Buffer Type             | N | No. of tranactions | No. of cycles |
|-------------------------|---|--------------------|---------------|
| Global Stall            | 4 | 61                 | 726           |
| Half-performance Buffer | 4 | 96                 | 726           |
| Skid Buffer             | 4 | 127                | 726           |
| 2-depth FIFO            | 4 | 168                | 726           |

Note: when implementing a 2-depth FIFO design to achieve maximum perfomance the fifo buffer length must be double.
