+++
title = "Current, Voltage and Resistor"
author = ["Kiran"]
date = 2025-03-13T12:40:00-04:00
tags = ["ams"]
draft = false
css = "../../zcustom.css"
+++

[AMS Directory]({{< relref "2025_03_13_ams_directory.md" >}})


##  {#d41d8c}


### Current {#current}

Current occurs when electic charge (Q) flows through a wire. The current passing through the wire can be given as the amount of charge **Q** passing through a wire in a given time **t**.

The general definition of current is given as the rate at which the charge passes through a wire.

\begin{equation}
\label{eq.1}
I = \frac{dQ}{dt}
\end{equation}

where:

-   I is current in amperes, A
-   Q is charge in coulomb, Q
-   t is time in seconds, s

Current is measured in _amperes (A)_ and charge is represented as _coulomb Q_.


#### Points to Remember {#points-to-remember}

1.  Each current has a value and a direction
2.  In an open circuit the current is zero.
3.  The amount of charge passing through a wire in a certain amount of time T is given as:

\begin{equation}
\label{eq.2}
Q = \int\_{0}^{T} I \quad dt
\end{equation}

where:

-   Q is charge in coulomb, Q
-   T is time in seconds, s
-   I is current in amperes, A

This describes the that the amount of charge in time T is defined as the area under the curve.


### Voltage {#voltage}

Voltage is the potential difference between two points **A** and **B**, it the measure of amount of energy required to move a positive unit charge from point **A** to point **B** in an electrial circuit.


#### Points to Remember {#points-to-remember}

1.  A voltage has a value and sign.
2.  A voltage diffenerce exists only between two points/nodes.
3.  Votage is zero in a short circuit.


### Resistor {#resistor}

A resistor is passive electonic element that obstructs the flow of current. A resistor dissipates electical energy in the form of heat, unlike active elements that amplify the energy, e.g., transistors and op-amps.


### Ohm's Law {#ohm-s-law}

Ohm’s Law is one of the most fundamental principles in electrical engineering, describing the relationship between voltage (V), current (I), and resistance (R). It states that the voltage across a resistor is directly proportional to the current flowing through it, given by the equation:

\begin{equation}
\label{eq.3}
V = I \times R
\end{equation}

where:

-   V is voltage in volts, V
-   I is cuurent in amperes, A
-   R is resistance in ohm, &Omega;


### Points to Remember {#points-to-remember}

1.  Ideal Wire (Short Circuit): an ideal wire has zero resistance, zero capacitance, and zero inductance. According to Ohm’s Law (V = IR), if resistance (R) is zero, the voltage drop (V) across the wire is also zero—regardless of the current. This means an ideal wire behaves as a short circuit, where the two ends of the wire are at the same potential.

2.  Switch Behavior: An open or off switch acts as an open circuit. In this case, no current flows through the circuit (i.e., the current is zero), regardless of the voltage across the switch. A closed or on switch behaves like a short circuit, allowing current to flow freely. Here, the voltage drop across the switch is zero—just like an ideal wire (ie., short circuit).


#### Reference {#reference}

1.  [Basic Circuits: Behzad Razavi](https://www.youtube.com/playlist?list=PLf3-Y54_5J6NqqY3wbNGWMXNcp6yafKnB)
2.  [Design of Analog CMOS IC by Behazad Razavi](https://www.amazon.com/Integrated-Circuits-Electronics-Computer-Enginering/dp/0072524936/140-2434170-9079053?pd_rd_w=lFu00&content-id=amzn1.sym.081392b0-c07f-4fc2-8965-84d15d431f0d&pf_rd_p=081392b0-c07f-4fc2-8965-84d15d431f0d&pf_rd_r=SCR736BQYSXTEC5X3E10&pd_rd_wg=RQnBY&pd_rd_r=6667b244-37a9-4b2a-885b-239912ea914f&pd_rd_i=0072524936&psc=1)
