+++
title = "Voltage Source, Current Source and Dependent Source"
author = ["kiran"]
date = 2025-03-30T13:46:00-04:00
tags = ["ams"]
draft = false
css = "../../zcustom.css"
+++

[AMS Directory]({{< relref "2025_03_13_ams_directory.md" >}})


##  {#d41d8c}


### Voltage Source {#voltage-source}

A voltage source is an electrical component that provide constant or varying voltage between two terminals. It is a key component in circuit that is responsible for pushing electric charge through a closed circuit.


#### Variable and Constant Voltage Source {#variable-and-constant-voltage-source}

A variable voltage source is one that varies its voltage over time. THe variations can be adjusted manully via nknob or automatic via signal generator. These sources are useful for testing and controlling circuits with different voltage requirements.

A constant voltage source delivers a fixed voltage over time. It maintains the same voltage level even if the current drawn by the load changes. A 5V battery always produced a 5V voltage drop across its terminals.


#### Independent and Dependent Voltage Source {#independent-and-dependent-voltage-source}

An independent voltage source provides a voltage that is not influenced by any other quantity in the circuit. Its value remains fixed (in the case of a constant voltage source) or changes in a defined manner (in the case of a variable voltage source), regardless of what’s happening elsewhere in the circuit.

A dependent voltage source, on the other hand, has a voltage that depends on another quantity in the circuit—such as a current or voltage at a different part of the circuit.

{{< figure src="/ox-hugo/voltage_source.svg" caption="<span class=\"figure-number\">Figure 1: </span>Voltage Source" width="600px class center !important" >}}

Figure 1 shows the standard circuit symbols used to represent constant, variable, and dependent voltage sources. These symbols help distinguish between a fixed power supply, an adjustable one, and a source that is controlled by another part of the circuit.

Dependent voltage sources are especially useful for modeling more complex electronic systems. A common example is in amplifier circuits, where the output voltage is controlled by the input signal, making dependent sources a key building block in analog circuit design.

{{< figure src="/ox-hugo/dependent_voltage.svg" caption="<span class=\"figure-number\">Figure 2: </span>Example Dependent Source" width="600px" >}}

In this example 2(a), the supply voltage provided by the  Voltage-Dependent Voltage Source (VDVS) is proportional to a controlling voltage elsewhere in the circuit. Mathematically, it is expressed as:

\begin{equation}
\begin{aligned}
V\_2 = \textit{k} \times V\_1
\end{aligned}
\end{equation}

where:

-   \\(V\_1\\) is the controlling voltage
-   \\(k\\) is the gain factor
-   \\(V\_2\\) is the voltage supplied by VDVS

Using Ohm's Law, the current through the resistor \\(R\_1\\) is given by:

\begin{equation}
\begin{aligned}
I\_1 = \frac{V\_2}{R\_1}
\end{aligned}
\end{equation}

Here, \\(V\_2\\)​ is the voltage provided by the VDVS, which is equals to \\(k \times V\_1\\)​. So the current is:

\begin{equation}
\begin{aligned}
I\_1 = \frac{k \times V\_1}{R\_1}
\end{aligned}
\end{equation}

Figure 2(b) shows the current (\\(I\_1\\)) vs. voltage (\\(V\_1\\)) graph for an ideal VDVS. Since current \\(I\_1\\) varies linearly with the controlling voltage \\(V\_1\\)​, the plot is a straight line. The slope of the graph is:

\begin{equation}
\begin{aligned}
slope = \frac{k}{R\_1}
\end{aligned}
\end{equation}

This slope reflects how strongly the controlling voltage influences the current in the circuit. A higher \\(k\\) or lower \\(R\_1\\)​ leads to a steeper line—meaning more current for the same input voltage.


### Current Source {#current-source}

A current source is an electrical component designed to deliver a constant current, regardless of the voltage across its terminals. It is a two-terminal active device that maintains a fixed current through a circuit, even if the voltage across it varies.
A current source is the opposite of a voltage source, which maintains a constant voltage irrespective of the current drawn.

Current source provide a good model for some other devices such as transistors.

{{< figure src="/ox-hugo/current_source.svg" caption="<span class=\"figure-number\">Figure 3: </span>Current Source" width="300px" >}}


#### Points to Remember {#points-to-remember}

1.  A voltage source is a two-terminal device that maintains a specified voltage across its terminals, regardless of the load or the amount of current drawn from it. It `enforces a constant voltage` in the circuit.
2.  A current source is a two-terminal device that delivers a specified current, regardless of the voltage that appears across its terminals. It `enforces a constant current` through the circuit, no matter the load.
3.  A current source must always be part of a closed circuit. If it is left in an open-circuit condition, it means there is infinite resistance in the path of the current. According to Ohm’s Law, \\(V=I \cdot R\\), if the resistance \\(R\\) becomes infinite while the current \\(I\\) remains fixed (as enforced by the current source), the resulting voltage \\(V\\) also becomes infinite—which is physically impractical and can damage real-world components.


### Dependent Source {#dependent-source}


#### Reference {#reference}

1.  [Basic Circuits: Behzad Razavi](https://www.youtube.com/playlist?list=PLf3-Y54_5J6NqqY3wbNGWMXNcp6yafKnB)
2.  [Design of Analog CMOS IC by Behazad Razavi](https://www.amazon.com/Integrated-Circuits-Electronics-Computer-Enginering/dp/0072524936/140-2434170-9079053?pd_rd_w=lFu00&content-id=amzn1.sym.081392b0-c07f-4fc2-8965-84d15d431f0d&pf_rd_p=081392b0-c07f-4fc2-8965-84d15d431f0d&pf_rd_r=SCR736BQYSXTEC5X3E10&pd_rd_wg=RQnBY&pd_rd_r=6667b244-37a9-4b2a-885b-239912ea914f&pd_rd_i=0072524936&psc=1)
