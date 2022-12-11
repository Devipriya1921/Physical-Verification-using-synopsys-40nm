# Physical Verification (PV) Using Synopsys 40nm

## Table of Contents

1. [Overview](#overview)
   - [VLSI Design Flow chart](#vlsi-design-flow-chart)
   - [KeyTerms](#keyterms)
   - [Before steps to Physical Verification](#before-steps-to-physical-verification) 
        - [PHYSICAL DESIGN STEPS](#physical-design-steps)
2. [Introduction to Physical Verification](#introduction-to-physical-verification)
   - [DRC (Design Rule Check)](#drc-design-rule-check)
   - [LVS (Layout vs Schematic)](#lvs-layout-vs-schematic)
   - [ARC (Antenna Rule Checking)](#arc-antenna-rule-checking)
   - [ERC (Electrical Rule Checking)](#erc-electrical-rule-checking)
3. [Verification Tools](#verification-tools)
   - [Overview](#overview)
        - [Tools to Invoke](#tools-to-invoke)
   - [Understanding of Synopsys EDA Tools for Verification](#understanding-of-synopsys-eda-tools-for-verification)
        - [IC Validator (ICV)](#ic-validator-icv)
             - [ICV DRC](#icv-drc)
             - [ICV LVS](#icv-lvs)
4. [Designing of an Inverter](#designing-of-an-inverter)
   - [CMOS Inverter Working](#cmos-inverter-working)
        - [Inverter Schematic Diagram](#inverter-schematic-diagram)
   - [Library Characterization](#library-characterization)
        - [How Does Library Characterization Work?](#how-does-library-characterization-work)
   - [Library and Cell View creation](#library-and-cell-view-creation)
   - [Schematic Creation](#schematic-creation)
   - [Symbol Creation](#symbol-creation)
5. [Analysis of an Inverter](#analysis-of-an-inverter)
   - [Inverter Static Characteristics or VTC](#inverter-static-characteristics-or-vtc)
   - [Inverter Dynamic Characteristics](#inverter-dynamic-characteristics)
   - [Creating TestBench](#creating-testbench)
   - [Simulation on PrimeWave](#simulation-on-primewave)
        - [DC Analysis](#dc-analysis)
   - [Netlist and log Display](#netlist-and-log-display)
        - [Synopsys Design Constraints](#synopsys-design-constraints)
        - [Netlist run](#netlist-run)
        - [Log](#log)
6. [Layout Creation](#layout-creation)
   - [Schematic Driven Layout (SDL)](#schematic-driven-layout-sdl)
        - [Execution](#execution)
   - [Routing](#routing)
        - [Execution](#execution)
7. [Physical Verification-SignOff Checks](#physical-verification-signoff-checks)
   - [DRC](#drc)
        - [Execution](#execution)
   - [LVS](#lvs)
        - [LVS Flow](#lvs-flow)
            - [Commonly faced LVS issues](#commonly-faced-lvs-issues)
            - [IR Drop Analysis](#ir-drop-analysis)
        - [Execution](#execution)
   - [XOR](#xor)
        - [Execution](#execution)
8. [Acknowledgements](#acknowledgements)
9. [Author](#author)
10. [References](#references)


## Overview

### VLSI Design Flow chart 

Very Large Scale Integration(VLSI) is the process of making Integrated Circuits (ICs) by combining a number of components like resistors, transistors, and 
capacitors on a single chip.

VLSI Design is an iterative cycle. Designing a VLSI Chip includes a few problems such as functional design, logic design, circuit design, and physical design.
The design is verified for accuracy by the process of simulation. 

If any design errors are found at any stage of verification, at least one of the previous design steps must be repeated to correct the error during the 
process of designing.

<img width="160" alt="vlsi design flow cart" src="https://user-images.githubusercontent.com/83152452/206857301-e219cb5e-8009-4609-a63f-21dd8cca4019.png">

1. System specification: The objective of the desired final product is written in this step. During system specification, the designated cost of the system, 
its performance, architecture, and how the system will communicate with the external world are to be determined. During this step, the design specification 
should be provided by the users or clients.

2. Architectural design: The basic architecture of the desired design must meet the system specifications of the desired design. The architecture of the 
desired design is decided and the layout for the same is designed by design engineers. Architectural design includes the integration of analog and 
mixed-signal blocks, memory management, internal and external communication, power requirements, and choice of process technology and layer stacks.

3. Functional design or Behavioural design: It consists of refining the design specification of the desired design in order to design the functional behavior 
of the desired system. The main objective of this is to generate design a high-performance architectural design within the cost requirements posed by the 
specifications.

4. Logic Design: In this step, the structure of the desired design is added to the behavioral representation of the desired design. The main specifications 
to be considered for logic design are logic minimization, performance enhancement, and testability. Logic design must also consider the problems associated 
with test vector generation, error detection, and error correction. Many logic synthesis tools have been developed for the automation of the process of logic 
design.

5. Circuit Design: In this step, the logic blocks of the desired design are replaced by the electronic circuits, which are consists of electronic devices 
such as resistors, capacitors, and transistors. Circuit simulation of the desired design is done at this stage, in order to verify the timing behavior of 
the desired system. Kirchhoff’s laws are used to know the behavior of the electronic circuit in terms of node voltages and branch circuits. The result of 
integrodifferential equations is then solved in discrete- time. SPICE is a well-known program for circuit simulation.

6. Physical Design: In this step, the actual layout of the desired system is done, where all the components will be placed in the circuit and all these 
components are interconnected. The actual layout of the desired system can affect the area, correctness, and performance of the final desired product. The 
correctness of the chip is also controlled by the physical design. A circuit design that passes the test of a circuit simulator may be faulty after it has 
been packaged. This is because of geometric design rule errors. These design rules must be followed to ensure the correctness of the chip fabrication. Errors 
such as short circuits, open circuits, open channels, etc may result if the design rules are not respected.

7. Fabrication: After the actual layout and verification of the desired design, the design is sent for manufacturing. The handoff of the desired design to 
the manufacturing process is called tapeout. Generation of the data for manufacturing is referred to as streaming out. The desired design is onto the 
different layers of the design using the photolithographic process. ICs are manufactured on round silicon wafers with a diameter from 200mm to 300mm, these 
ICs are then tested and are marked as either functional or defective ICs.

8. Packaging and Testing: After fabrication of desired design, functional chips are then packed. Packaging is configured early in the desired design process 
and the application along with the cost and form factor requirements. Packaged types may include Dual In-Line Packaged (DIPs), Pin Grid Array (PGAs), and 
Ball Grid Arrays (BGAs). After a die is positioned in the package cavity, its pins are connected to the pins of the package, e.g., with wire bonding or 
solider bumps (flip-chip). The package of the desired design is then sealed and then sent to the end-users or clients.


### KeyTerms

- Custom : Is a method of creating integrated circuits that specifies the architecture (Like, Analog) of each individual transistors as well as inter connections (via, contacts).
- Custom Jobs : Custom Compiler Design Checking and Physical Verification
- Design Specifications: Customer requirements  PDK file, Input elements, Technology file
- Schematic Capture: Circuit creation
- Symbol Creation: Creating the schematic into symbol 
- Simulation: Pre-Layout Simulation  PrimeWave
- Layout: SDL, LE(Layout Editor)
- Parasitic extraction: Extracting the binary coefficient values of resistor and capacitor.


### Before steps to Physical Verification

![VLSI-DESIGN-FLOW-page-001-1](https://user-images.githubusercontent.com/83152452/206857564-3322a286-3f32-4e14-b80a-e629faec6ad1.jpg)

The main steps in the physical design flow are:

- Design Netlist (after Synthesis)
- Floorplanning
- Partitioning
- Placement
- Clock-Tree Synthesis (CTS)
- Routing
- Physical Verification
- GDS II Generation

The physical design flow uses the technology libraries that are provided by the fabrication houses. Technologies are commonly classified on the basis of 
minimal feature size. The basic sizes available are 2µm, 1 µm, 0.5 µm, 90nm, 45nm, 18nm, 14nm, etc. They may also be classified according to the manufacturing
process like: n-well process, twin well process, SOI process etc.

The steps of design flow are explained below:

DESIGN NETLIST: Physical Design is based on a netlist which is the end result of Synthesis process. The netlist contains information on the cells used, their 
interconnections, area, and other details. Synthesis tools that are used are:

Cadence RTL Compiler/Build Gates
Synopsys Design Compiler

During the synthesis process, all the constraints are applied to ensure the design meets the functionality and speed. Once the timing and functionality is 
verified, it is sent for physical design flow.

#### PHYSICAL DESIGN STEPS

1. FLOOR-PLANNING: The first step in physical design is floor planning. In this process, the structures are identified which should be placed together meeting the performance and timing requirements. The desire to have everything close to everything else is followed. But based on the area and hierarchy, a suitable floor plan is decided. Floor planning is defined as taking account of macros used in the design, memory, other IP cores and their placement needs, the routing possibilities and also the area of the entire design.  Area and Speed are two factors which can be trade off against one another. Optimizing the design for minimum area  allows the design to use fewer resource and thus increasing the speed of the system.

As a general rule, data-path benefit most from floor planning, and other logics like state machines, or some random logic are placed to the left section of 
the place and route software.

2. PARTITIONING: It is a process of dividing the chips into small blocks. This is done mainly to separate functional block and also to make placement and 
route easier. Partitioning can be done in the designing phase as the design engineer usually divides the whole system to sub-blocks and then proceeds to 
design each module. These sub modules/blocks are later connected in TOP LEVEL module.

3. PLACEMENT:  Before the placement, all Wire Load Models(WLM) are removed. Placement uses RC values from Virtual Route(VR) to calculate timing.
Placement can be done in four phases:

I. Pre-placement optimization: In this process optimization happens before netlist is placed. In this process high-fan out nets are collapsed downsizing the cells.

II. In placement optimization: In this process logic is re-optimized according to the VR. Cell bypassing, cell moving, gate duplication, buffer insertion, etc. 
can be performed in this step.

III. Post Placement optimization: Netlist is optimized with ideal clocks before CTS. It can fix setup, hold violations. Optimization is done based on global 
routing.

IV. Post placement optimization after CTS optimization: Optimization is performed after the CTS optimization is done using propagated clock. It tries to 
preserve the clock skew.

4. CLOCK TREE SYNTHESIS (CTS): 

I. Ideal clock before CTS: The goal of CTS is to minimize the skew and insertion delay. Clock is not propagated before CTS.  If clock is divided the separate 
skew analysis is done. Global skew achieves zero skew between two synchronous pins without considering logic relationship and local skew achieves zero skew 
between two synchronous pins while considering logic relationship. If clock is skewed intenetionally to improve the setup slack then it is known as useful skew.

II. Clock After CTS: In clock tree optimization cloak can be shielded to remove noise. But shielding increases area by 12% to 15%. Optimization is achieved 
by gate sizing, buffer sizing, level synthesis and HFN synthesis. Hold slack is improved in optimization after CTS. As a result of CTS a lot of buffers are added.

5. ROUTING: There are two types of routing in physical design process:

I. Global Routing: In this type of routing a loose route is generated for each net with estimated values. Rough estimation of values can be done by calculating 
the  delays for fanout of wire. Global Routing is further divided into Line Routing and Maze Routing.

II. Detailed Routing: In detailed routing the actual geometry layout of each net is calculated i.e actual delays of wire is calculated. The actual delays can 
be obtained by several optimizations like Timing optimization, CTS, etc.

6. PHYSICAL VERIFICATION: Physical verification checks the correctness of generated layout design. This includes:

I.   DRC (Design Rule Check)
II.  LVS(Layout vs. Schematic)
III. ARC (Antenna Rule Checking)
IV. ERC (Electrical Rule Checking)

7. GDS II: Graphic Database System: GDS II is a database file format which is the industry standard for data exchange of IC layout artwork. It is a binary file representing planar geometric shapes, text labels and other information about the layout in hierarchical form.



## Introduction to Physical Verification

<img width="635" alt="design flow" src="https://user-images.githubusercontent.com/83152452/206853853-4f7fdd9d-5c5a-49ac-9fde-0f176eec808f.png">

After routing, your PnR tool should give you zero DRC/LVS violations. However, the PnR tool deals with abstracts like FRAM or LEF views. We use dedicated 
physical verification tools for signoff LVS and DRC checks. Some of these are Hercules from Synopsys, Assura from Cadence and Calibre from MentorGraphics.

The major checks are, as mentioned above in previous section:

- DRC
- LVS
- Antenna
- ERC

### DRC (Design Rule Check)

DRC checks determine if the layout satisfies a set of rules required for manufacturing. The most common of these are spacing rules between metals, minimum 
width rules, via rules etc.There will also be specific rules pertaining to your technology. An input to the design rule tool is a ‘design rule file’ 
(called a runset by Synopsys’ hercules). 

The design rules ensure sufficient margins to correctly define the geometries without any connectivity issues due to proximity in the semiconductor manufacturing processes, so as to ensure that most of the parts work correctly. The minumum width rules exists for all 
mask layers, and spacing between the same layers are also specified. Spacing rules may change depending on the width of one or both of the layers as well. 
There can also be rules between two different layers, and specific via density rules etc.

![DRC](https://user-images.githubusercontent.com/83152452/206858473-94def312-5982-4008-8841-efe07e49112f.png)

If the design rules are violated, the chip may not be functional. DRC checking software, like Assura, Hercules or Calibre usually takes the layout in any of 
the supported formats, like GDSII.

### LVS (Layout vs Schematic)

LVS is another major check in the physical verification stage. Here you are verifying that the layout you have created is functionally the same as the 
schematic/netlist of the design-that you have correctly transferred into geometries your intent while creating the design. So all the connections should be 
proper and there shouldn’t any missing connections etc.

The LVS tool creates a layout netlist, by extracting the geometries. This layout netlist is compared with the schematic netlist. The tool may require some 
steps to create either of these netlists(e.g. nettran run in synopsys)

If the two netlists match, we get an LVS clean result. Else the tool reports the mismatch and the component and location of the mismatch. Along with formal 
verification, which verifies if your pre-layout netlist matches the post-layout netlist,LVS verifies the correctness of the layout w.r.t intended functionality.

Some of the LVS errors are:

1. Shorts – Wires that should not be connected are overlapping.
2. Opens – Connections are not complete for certain nets.
3. Parameter mismatch – LVS also checks for parameter mismatches. e.g. It may match a resistor in both layout and schematic, but the resistor values may be 
different. This will be reported as a parameter mismatch.
4. Unbound pins – If the pins don’t have a geometry, but all the connection to the net are made, and unbound pin is reported.

### ARC (Antenna Rule Checking)

Process antenna effect or “plasma induced gate oxide damage” is a manufacturing effect. i.e. this is a type of failure that can occur solely at the 
manufacturing stage. This is a gate damage that can occur due to charge accumulation on metals and discharge to a gate through gate oxide.

In the manufacturing process, metals are built layer by layer. i.e. metal1 is deposited first, then all unwanted portions are etched away, with plasma etching. The metal geometries when they are exposed to plasma can collect charge from it. Once metal1 is completed, via1 is built, 
then metal2 and so on. So with each passing stage, the metal geometries can build up static electricity. The larger the metal area that is exposed to the 
plasma, the more charge they can collect. If the charge collected is large enough to cause current to flow to the gate, this can cause damage to the gate 
oxide. This happens because since the layers are built one-by-one, a source/drain implant may not be available for discharge as in fig.b.

![process-antenna](https://user-images.githubusercontent.com/83152452/206858434-cbb91f3c-7a4c-4f63-89e5-519536c72bb1.png)

Antenna rules are normally expressed as an allowable ratio of metal area to gate area. Each foundry sets a maximum allowable antenna ratio for its processes. 
If the metal area–which is cumulative, i.e. the sum of the ratios of all lower layer interconnects in addition to the layer in check–is greater than the 
allowable area, the physical verification tool flags an error.For example, let’s say maximum allowable antenna ratio for metal1 is 400. If the gate area is 1 
sq.u and if the metal area connecting to the gate is 500 sq.u, there will be a process antenna violation.

### ERC (Electrical Rule Checking)

ERC (Electrical rule check) involves checking a design for all electrical connections that are considered dangerous.

1. Floating gate error – If any gate is unconnected, this could lead to leakage issues.
2. VDD/VSS errors – The well geometries need to be connected to power/Ground and if the PG connection is not complete or if the pins are not defined, the 
whole layout can report errors like “NWELL not connected to VDD.



## Verification Tools 

### Overview 

1. Custom Compiler: 
- The Synopsys Custom Compiler design environment is a modern solution for full-custom analog, custom digital, and mixed-signal IC design. Custom Compiler provides design entry, simulation management and analysis, and custom layout editing features. 
- The Custom Compiler design environment includes features for mixed-signal design entry, design debug, simulation management, analysis, and reporting. 
- For layout, Custom Compiler provides fast and user-friendly polygon editing features and boosts productivity with its pioneering visually-assisted automation flow. Visually assisted automation is an innovative approach that delivers 2-10X better layout productivity—especially for difficult FinFET-based designs. 
- Custom Compiler includes built-in verification features to catch physical and electrical errors during layout. These include design rule checking, electromigration checking, and resistance and capacitance extraction. 
- The Custom Compiler design environment makes it easy to communicate design intent and achieve analog design closure, with support for templates and early parasitic simulation.
- For more information you may check at https://www.synopsys.com/implementation-and-signoff/custom-design-platform/custom-compiler.html


2. IC Validator:
- IC Validator physical verification is a comprehensive and high-performance signoff solution that improves productivity for customers at all process nodes, from mature to advanced. IC Validator offers the industry’s best distributed processing scalability to over 4,000 CPU cores. 
- The tool’s performance and scalability enabled some of the industry’s largest reticle limit chips with billions of transistors, same-day design rule checking (DRC), layout versus schematic (LVS), and fill turnaround time.
- IC Validator is seamlessly integrated with the Synopsys Fusion Compiler RTL-to-GDSII solution and IC Compiler II place and route system in the Fusion Design Platform. 
- This integrated fusion technology accelerates design closure for manufacturing by enabling independent signoff-quality analysis and automatic repair within the implementation environment.
- For more information you may check at https://www.synopsys.com/implementation-and-signoff/physical-verification.html

3. PrimeWave:
- PrimeWave Design Environment is a comprehensive and flexible environment for simulation setup and analysis of analog, RF, mixed-signal design, custom-digital and memory designs within the Synopsys Custom Design Platform. 
- It delivers a seamless simulation experience around all the engines of Synopsys PrimeSim solution, with comprehensive analysis, improved productivity, and ease of use. 
- PrimeWave Design Environment also offers powerful Tcl-based scripting capability enabling easy regressions across thousands of corners.
- For more information you may check at https://www.synopsys.com/implementation-and-signoff/ams-simulation/primewave.html

#### Tools to Invoke

The tools that I had invoked for the labs in terminal are listed below:
```
module load primewave
module load hspice
module load cx
module load primesim
module load icv
module load customcompiler
custom_compiler &

The version that I used for invoking all the tools are: 2022.06-SP1
You may invoke it without mentioning the version, as it will take the latest version available. Else, if you wish to invoke a specific version then you may mention it as follows: module load primewave/2022.06-SP1
```


### Understanding of Synopsys EDA Tools for Verification

- If you are looking for steps to install the Synopsys EDA Tools you may check out from the official Website: [Synopsys](https://www.synopsys.com/)
- The details are mentioned below for DRC and LVS. 

#### IC Validator (ICV)

<img width="305" alt="icv" src="https://user-images.githubusercontent.com/83152452/206859524-f6559f20-4d82-4638-ab60-fec910268212.png">

- For details visit the [Link](https://www.synopsys.com/implementation-and-signoff/physical-verification.html)

##### ICV DRC

- Firstly from the link: [https://www.synopsys.com/implementation-and-signoff/resources/videos/drc-three.html], 
  Learn how to run Design Rule Checks (DRC) interactively from IC Validator VUE interface. 
- IC Validator VUE is a flow based graphical tool that guides you through the entire physical verification flow. Within one interface, you can configure and execute a verification run, easily load the results, review a run summary, and debug the design by highlighting errors within most layout editor tools. 
- For technical videos on IC Validator DRC you may check at : 
  https://video.synopsys.com/icvalidator/category/videos/ic-validator-technical-videos:-drc

##### ICV LVS

- Layout Versus Schematic (LVS) checking compares the extracted netlist from the layout to the original schematic netlist to determine if they match.
- The comparison check is considered clean if all the devices and nets of the schematic match the devices and the nets of the layout. 
- Optionally, the device properties can also be compared to determine if they match within a certain tolerance. When properties are compared, all the properties must match as well to achieve a clean comparison.

LVS Flow:
<img width="589" alt="lvs flow" src="https://user-images.githubusercontent.com/83152452/206898082-0e63c33e-28c4-44b3-a8d3-8e5ddf5b080b.png">

- Two main processes make up the LVS flow. The first process in the flow is extraction, in which the layers within the layout database are analyzed and all the devices and nets are extracted. The second process in the flow is compare, in which the actual comparison of devices and nets occurs.
- The LVS runset contains a series of function calls that control both extraction and netlist comparison.

Common LVS issues:
LVS errors can be classified into two main categories:

1. Extraction Errors

- Text short and open
- Device extraction error
- Missing device terminal
- Extra device terminal
- Unused text
- Duplicate structure placement

2. Compare Errors

- Unmatched nets in the layout/schematic
- Unmatched devices in the layout/schematic
- Property errors
- Port swap errors

- Content source [Link](https://www.synopsys.com/glossary/what-is-layout-versus-schematic-checking.html)
- For technical videos you may check at: https://video.synopsys.com/icvalidator/category/videos/ic-validator-technical-videos:-lvs



## Designing of an Inverter

### CMOS Inverter Working

- The term “CMOS” stands for “complementary-symmetry metal–oxide–semiconductor” which is pronounced as “see mos”. CMOS is a type of MOSFET, where its fabrication process uses complementary & symmetrical P-type & N-type MOSFET pairs for logic functions. 
- The main CMOS devices characteristics are consumption of low static power & high noise immunity. The inverter is accepted universally as the basic logic gate while performing a Boolean operation on a single i/p variable. 
- A basic inverter circuit is used to accomplish a logic variable by complementing from A to A’. So, a CMOS inverter is a very simple circuit, designed with two opposite-polarity MOSFETs within a complementary way.
- The logic element like an inverter reverses the applied input signal. In digital logic circuits, binary arithmetic & switching or logic function’s mathematical manipulation are best performed through the symbols 0 & 1. If the input logic is zero (0) then the output will be high (1) whereas, if the input logic is one (1), then the output will be low (0).

#### Inverter Schematic Diagram

![CMOS-Inverter-Symbol-Truth-Table](https://user-images.githubusercontent.com/83152452/206916149-65b30392-3e6f-4b56-b0b8-fdf89a436890.jpg)

- CMOS inverter definition is a device that is used to generate logic functions is known as CMOS inverter and is the essential component in all integrated circuits. 
- A CMOS inverter is a FET (field effect transistor), composed of a metal gate that lies on top of oxygen’s insulating layer on top of a semiconductor. These inverters are used in most electronic devices which are accountable for generating data n small circuits.


### Library Characterization

- Cell library characterization is a process of analyzing a circuit using static and dynamic methods to generate models suitable for chip implementation flows.
- Knowing the logical function of a cell is not sufficient to build functional electrical circuits. More aspects need to be considered; for example, the speed of a single cell will influence the speed of the full circuit, just as the power used by a single cell can influence the total power. 
- Further, the speed as well as the power might be influenced by the output load. Standard-cell characterization aims at collecting this sort of information.
- Library characterization is a process of simulating a standard cell using analog simulators to extract input load, speed, and power data in a way that the downstream tools can process it all. 
- This can be done via a specific analog simulator whose output is used to generate the characterization data, or by using a library characterization tool.

#### How Does Library Characterization Work?

- For digital library characterization, some of the views are usually provided by the design while others must be derived. For instance, the logic function of a NAND gate could be defined as "!(A & B)." In principle, all other views can be derived from this equation using some additional assumptions and constraints, depending on the technology and design goals. 
- The netlist of combinatorial cells can be generated algorithmically. Starting from the netlist, a layout will be drawn which can be used to extract parasitic capacitances and resistances within the cell. The resulting netlist of transistors and parasitics can be further used to get an abstract description of the cell’s timing, power, and noise behavior.
- One way to derive timing and power numbers is through simulation of the transistor netlist. However, one simulation is not enough because the cell’s behavior strongly depends on other factors, such as process, voltage, and temperature (PVT), input wave form, and output load. 
- A common approach is to look at the best and worst PVT conditions. Effectively, this allows you to predict lower and upper bounds of the cell behavior, which are important to ensure the overall functionality of the design. 
- Further, cells must be characterized over a reasonably large range of input edge rates and output load. For example, a cell could be characterized on a two-dimensional grid with variable input edge rate and output load (non-linear delay model). If the grid points are chosen correctly, then the behavior between grid points can be approximated by linear interpolation.
- Cell library characterization typically takes cell design extracted as SPICE circuit and SPICE technology models. The characterization tool analyzes this information to:

1. Acquire the functionality of the cell
2. Generate stimulus to produce the characterization decks
3. Simulate the decks using a circuit simulator
4. Gather the simulation output
5. Write this data into standard models, like Liberty™, Verilog, or IBIS

- For more details: Source [Link](https://www.synopsys.com/glossary/what-is-library-characterization.html)

### Library and Cell View creation

1. Invoke Custom Compiler (CC) from your terminal ``` module load custom_compiler & ``` and your window should look something like this:

<img width="955" alt="Step-1" src="https://user-images.githubusercontent.com/83152452/206899185-f38e9bc7-b022-4f6e-ad79-de716f6b72a5.png">

2. Go to ``` File --> New --> Library ``` as shown below:

<img width="400" alt="Step-2 - Library creation" src="https://user-images.githubusercontent.com/83152452/206899190-036accdc-26b1-4b1c-8dab-ba88f9f1c0aa.png">

3. A dialog box appears as shown below. Enter the Name and select the Technology Node, as in my case it is ```reference40nm``` and then Click OK.

<img width="344" alt="Step-3 - Library creation - name" src="https://user-images.githubusercontent.com/83152452/206899194-fa5f829b-3735-4f9f-a880-4b03d1e1105a.png">

4. After which a New Library gets created with the name that you gave. Then for creating a new cell view, Go to ``` File --> New --> CellView ``` as shown below:

<img width="401" alt="step-4 - cellview creation" src="https://user-images.githubusercontent.com/83152452/206899197-44d81ca5-2106-438f-ae4a-10bd6006bad1.png">

5. CellView dialog box appears. Choose the options as shown below and then Click OK.

<img width="442" alt="step-5 - cellview creation - dialogbox" src="https://user-images.githubusercontent.com/83152452/206899200-c66d8c75-39ae-4968-822f-cbbe9b8c759b.png">

6. Schematic Window will open as shown below:

<img width="956" alt="step-6 - schematic window appearance" src="https://user-images.githubusercontent.com/83152452/206899203-709f2dde-79c8-46c0-8724-330ed2d5da40.png">

### Schematic Creation

1. Click on Instance as shown in the figure or press I on screen

<img width="182" alt="step-7- instance" src="https://user-images.githubusercontent.com/83152452/206899654-bc62b1cc-9813-4633-b4a7-a34cde3aefd3.png">

2. In the Library section, select the Technology node that you used to create the Library and select p_4t, n_4t devices from the options available one by one. In the Parameters section, enter the vlues as shown in the fig below.

![Inkedstep-8-schematic - 1](https://user-images.githubusercontent.com/83152452/206903587-2b4fbd3f-de33-4860-8f14-d7c550f85a1c.jpg)

<img width="294" alt="p_4t" src="https://user-images.githubusercontent.com/83152452/206903583-bacb66b2-4a77-49d9-aa00-a3dc3aad79c8.png">

<img width="272" alt="n_4t" src="https://user-images.githubusercontent.com/83152452/206903585-b7fe90df-c5c7-4dbc-894f-54c642d6da6a.png">

3. Place them on the window one by one.

4. Select Add Pin as shown in the below figure and place it on the screen.

<img width="128" alt="step-8-schematic - 4" src="https://user-images.githubusercontent.com/83152452/206899675-b0b0bb4a-9a03-4f39-bcc6-aa24602447d3.png">

In the Name section type ``` Vin, VDD, VSS, Vout ``` by selecting the Type to ``` Input, Input, Input, Output ``` one after the other and place them on the schematic editor window.

![Inkedstep-8-schematic - 5](https://user-images.githubusercontent.com/83152452/206903588-0ef87ff7-1b42-4a63-b67b-9c12b4a37c0a.jpg)

5. Add wire by the clicing the option shown below:

<img width="94" alt="step-8-schematic - 6" src="https://user-images.githubusercontent.com/83152452/206899689-a400f8b1-5ea6-449b-b378-862938fb957c.png">

6. And, join all the circuit elements as shown below:

<img width="210" alt="schematic" src="https://user-images.githubusercontent.com/83152452/206903779-69039027-c094-40e5-bb52-e7e0eb28100c.png">


### Symbol Creation

For creating the Inverter symbol follow the steps as shown below:

1. Go to ``` Design --> New CellView --> From CellView ``` 

<img width="467" alt="step-9-symbol creation-1" src="https://user-images.githubusercontent.com/83152452/206903863-58facc04-1159-43ef-b0c5-7a0742f4fb4d.png">

2. Dialog box appears to be as shown below. Click OK.

<img width="470" alt="step-9-symbol creation-2" src="https://user-images.githubusercontent.com/83152452/206900902-9721d9fe-14d8-4e54-a773-9f237b3d3212.png">

3. Inverter symbol will be created as shown below:

<img width="922" alt="step-9-symbol creation-3" src="https://user-images.githubusercontent.com/83152452/206900905-97938889-62e8-45f0-bcb0-780f0a984c41.png">

4. You may delete the other options present in the snapshot above and make it to look like this as shown below:

<img width="954" alt="step-9-symbol creation-4" src="https://user-images.githubusercontent.com/83152452/206900907-10e6a01e-8e22-4863-b5aa-4c6f93167bbc.png">


## Analysis of an Inverter

The characteristics of the CMOS inverter are discussed below.

### Inverter Static Characteristics or VTC

- The quality of the inverter can be measured frequently by using the VTC or voltage transfer curve, which is plotted between input voltage (Vin) and output voltage (Vo). From the following static characteristics, the parameters of devices like gain, operating logic levels & noise tolerance, and noise can be obtained.

![CMOS-Inverter-Symbol-Truth-Table](https://user-images.githubusercontent.com/83152452/206916457-79d0c60e-13aa-4b1c-bc63-60e9f7aff4d3.jpg)

- The VTC or voltage transfer curve looks like an inverted step-function that specifies accurate switching in between ON & OFF however in real devices, a gradual transition region exists. The voltage transfer curve specifies that for less input voltage Vin, the circuit generates high voltage Vout, whereas, for high input, it generates 0 volts.
- The transition region slope is a measure of quality – steep slopes yield exact switching. The tolerance toward noise can be calculated by evaluating the smallest input to the highest output for every region of ON or OFF operation.

### Inverter Dynamic Characteristics

- The CMOS inverter dynamic characteristics are shown below. So, some of the following formal definitions of different parameters are discussed below. Here, all the percentage (%) values are the steady-state values.

![Voltage-Transfer-Curve](https://user-images.githubusercontent.com/83152452/206916459-78fd0485-a537-470c-9dfa-5ca076bc122c.jpg)

- Rise Time or tr: Rise time is the time used to increase the signal from 10% to 90%.
- Fall Time or tf: Fall time is the time used to drop the signal from 90% to 10%
- Edge Rate or trf : It is (tr + tf )/2.
- The propagation delay from high to low or tpHL: The time used to drop from VOH – 50%.
- The propagation delay from low to high or tpLH: The time used to increase from 50%- VOL.
- Propagation Delay or tp: It is (tpHL + tpLH)/2.
- Contamination Delay or tcd: It is the smallest time from the 50% input crossing to the 50% output crossing.


Follow the steps below in order to get the DC Analysis of an Inverter.

### Creating TestBench

1. Create a new CellView and name it as testbench (inverter_tb) as shown in the below figure:

<img width="442" alt="step-9-symbol creation-5" src="https://user-images.githubusercontent.com/83152452/206904048-0daf27e4-8fe5-49c8-8992-4c8c6350796e.png">

2. Add Instance of an Inverter that you created earlier in the exercise by either clicking on the Instance shortcut on the left side bar or simply press I by following the steps shown below:

<img width="358" alt="step-9-symbol creation-6" src="https://user-images.githubusercontent.com/83152452/206904051-37544327-5142-4a71-913b-b7c60b51191a.png">

3. Add Vdc (Voltage supply) by selecting the options shown below:

<img width="359" alt="step-9-symbol creation-7" src="https://user-images.githubusercontent.com/83152452/206904053-26374252-b471-427b-a834-9fbcc670e38a.png">

4. Add gnd (Ground) by selecting the options shown below:

<img width="355" alt="step-9-symbol creation-8" src="https://user-images.githubusercontent.com/83152452/206904058-02894510-6ad1-4773-85c4-810af9d4a221.png">

5. Connect them in the following way:

<img width="941" alt="step-9-symbol creation-10-dc analysis" src="https://user-images.githubusercontent.com/83152452/206904064-4fbe8f9e-2698-479d-bfa6-bc2135a543eb.png">

### Simulation on PrimeWave

1. Select ``` Tools --> PrimeWave ```

<img width="230" alt="step-10-primewave-1" src="https://user-images.githubusercontent.com/83152452/206904736-706277c4-ecd6-4d4a-960e-d8fa6d8c932d.png">

2. ``` Setup --> Model Files --> Include the hspice path from the directory --> Click to add and add the .lib file --> Under Section, Select TT --> Click OK ```

<img width="571" alt="step-10-primewave-2-setup-modelfiles" src="https://user-images.githubusercontent.com/83152452/206904739-6e8f7cd7-b5c7-4eb2-8145-636aea531a1a.png">

3. ``` Setup --> Analyses --> Analysis Type: dc --> Source Name: Select the Vdc pin that connects to Vin (In my case, it is V8) --> Give Start, Stop and Step size values as shown below --> In the bottom left corner, Click on Enable --> Click OK ```

<img width="511" alt="step-10-primewave-3-setup-analyser-dc" src="https://user-images.githubusercontent.com/83152452/206904743-d695fc92-2600-4f25-a8d6-f2b65d1b273d.png">

4. ``` Simulation --> Options --> Under Simulation Engine: PrimeSim HSPICE (Rest options, leave default) --> Click OK ```

<img width="311" alt="step-10-primewave-3-simulation-options" src="https://user-images.githubusercontent.com/83152452/206904750-13c3fcb6-8096-428a-8825-2947fadb32ae.png">

5. ``` Outputs --> Add from Design ```

<img width="239" alt="step-10-primewave-4-add from design" src="https://user-images.githubusercontent.com/83152452/206904776-b6e9b0fa-0df7-41d9-8dcd-8fbfa87e6629.png">

6. Click on the Input and Output Net (Vin and Vout) as shown below:

<img width="910" alt="step-10-primewave-4-add from design-schematic" src="https://user-images.githubusercontent.com/83152452/206904780-ff94b434-8887-441d-a423-85a927ed84a4.png">

7. Your inverter_tb window should now look something like this:

<img width="945" alt="step-10-primewave-5- final look" src="https://user-images.githubusercontent.com/83152452/206904784-ecf55857-1692-4cea-988f-74bf110f993e.png">

8. ``` Simulation --> Netlist and Run ```

<img width="206" alt="step-11-simulation-netlistandrun" src="https://user-images.githubusercontent.com/83152452/206904790-aa70ea43-d791-4738-9517-2d38646a109e.png">

#### DC Analysis

- Analyze DC Characteristics of CMOS Gates by studying an Inverter.

<img width="164" alt="p1" src="https://user-images.githubusercontent.com/83152452/206918784-85237de0-c421-4ee8-a68a-eaa11e910c86.png">

<img width="192" alt="p2" src="https://user-images.githubusercontent.com/83152452/206918783-ba3cf39f-1e7a-494c-bfde-4fb7f136a0af.png">

• DC Analysis:
– DC value of a signal in static conditions

• DC Analysis of CMOS Inverter:
– Vin, input voltage
– Vout, output voltage
– single power supply, VDD
– Ground reference
– find Vout = f(Vin)

• Voltage Transfer Characteristic (VTC):
– plot of Vout as a function of Vin
– vary Vin from 0 to VDD (and in reverse!)
– find Vout at each value of Vin


9. DC Analysis of an Inverter:

<img width="950" alt="step-11-waveform-1-dc" src="https://user-images.githubusercontent.com/83152452/206904793-9e6c8e9b-42d9-486d-9efd-32edfe4ffbc1.png">


### Netlist and log Display

#### Synopsys Design Constraints

- Timing closure is the big whale for most P&R designers. You get it done, and then you can wash your hands off all those annoying designers and get to work cleaning up and beautifying your layout. 
- So I thought of starting my P&R cheat sheet with an introduction to timing closure, and what best to address than the mighty SDC constraints, the bane of all our existence. You get the SDC right, you win half your battle. But it is not so simple. 
- SDC you get from the synthesis tool might have very well served its purpose there, but to get it do a pretty good job in layout requires some time spent on understanding and iterating the design.

1. Clock Statements
2. False path Specifications
3. Specifying operating conditions
4. Specifying Ideal Networks – No optimizations required
5. set_input_delay
6. set_max_delay
7. set_driving_cell
8. set_load

- There are countless other commands in SDC format, but these are the most common. Try to simplify your SDC as much as possible, and you can pin point the errors and issues easier.
- For more details visit the [Link](https://vlsi.pro/synopsys-design-constraints-sdc/)


#### Netlist run

- A netlist is a textfile containing the description of components connection. Component id , what is its value or name and the nodes its pins are connected.
- Spice netlist is an input file where user writes them and use it for simulation. Also used at the vlsi design where at the end of design phase the netslist is generated from layout and used for verification.
- Netlist perse is used in the PCB design for layout and routing of tracks.
- Netlist can be defined as the piece of code, which describe a circuit in textual form for interpretation by computer. Any simulation software uses some kind of textual description which is converted to machine understandable language.

- You can view the netlist file by simply going to ``` Simulation --> Netlist --> Display ``` 

<img width="380" alt="step-12-netlist display-1" src="https://user-images.githubusercontent.com/83152452/206906907-a8a22f4c-14c5-42ce-b25b-f8e3f893cb36.png">

- Below snapshot shows you the Netlist file:

<img width="935" alt="step-12-netlist display-2" src="https://user-images.githubusercontent.com/83152452/206906910-c90730c9-621f-4620-bb81-38153a6d27ff.png">

#### Log

- To view the log file ``` Simulation --> Display log ```

<img width="205" alt="step-13-display log file" src="https://user-images.githubusercontent.com/83152452/206906914-56a6bc4e-94f3-458e-904e-333a280b189f.png">

- You can explore and try to understand the other files generated as well. Below snapshot shows the hspice.st0 file:

<img width="929" alt="step-13-display log file-2" src="https://user-images.githubusercontent.com/83152452/206906918-5bfb17c5-1fe2-4933-af83-e7e77eb5ef13.png">


## Layout Creation

Basic Design Rules:

<img width="953" alt="basic design rules" src="https://user-images.githubusercontent.com/83152452/206907552-fd9fea07-c829-4d6d-a9c6-52f7a5084f6e.png">

### Schematic Driven Layout (SDL)

- The schematic-driven layout (SDL) flow in CC helps create an optimized layout that is DRC/LVS-correct in less time—without sacrificing layout area. Both 
netlist and schematic views are included with the CC layout editor for an intuitive SDL working environment, which includes:
```
- Schematic view and design browser
- Advanced layout editor, including rule-driven layout, flight-line guidance and short detection
- Realize, place, route, and edit a physical layout that is DRC- and LVS-correct
```

#### Execution

1. ``` Tools --> Check SDL Option ```

<img width="229" alt="step-14-SDL-1" src="https://user-images.githubusercontent.com/83152452/206907605-a3ab1fbf-33dc-4f93-b76e-321e1c67632f.png">

2. Physical Target Dialog box appears. Click Ok by checking with the details.

<img width="263" alt="step-14-SDL-click ok-2" src="https://user-images.githubusercontent.com/83152452/206907630-71a085cc-8999-4199-9c80-d8a278cda8fb.png">

3. ``` SDL --> Generate Layout ```

<img width="265" alt="step-14-SDL-3" src="https://user-images.githubusercontent.com/83152452/206907642-25bd997a-7e20-47c9-b3a1-68461c0a8d2f.png">

4. After the previous step, A dialog box appears wherein you can leave the options default or you may check with the options by trail and error to see which one works good for you and then Click OK --> You will now be able to see what is displayed on the snapshot below. Press ```Shift+F``` in order to view the circuit elements.

<img width="946" alt="step-14-SDL-window-4" src="https://user-images.githubusercontent.com/83152452/206907649-b0143071-7c13-44d1-9ed9-63030c2ea879.png">

5. Arrange the pmos, nmos and the pins as shown below or you may arrange it differently to check or experiment.

<img width="689" alt="step-14-SDL-window-5" src="https://user-images.githubusercontent.com/83152452/206907651-6756558a-0753-43aa-805d-33b3be50f8e5.png">

### Routing

- Routing is the process of creating physical connections based on logical connectivity. Signal pins are connected by routing metal interconnects. Routed metal paths must meet timing, clock skew, max trans/cap requirements and also physical DRC requirements.
- In grid based routing system each metal layer has its own tracks and preferred routing direction which are defined in a unified cell in the standard cell library.
- There are four steps of routing operations:
1. Global routing
2. Track assignment
3. Detail routing
4. Search and repair

- Global Route assigns nets to specific metal layers and global routing cells. Global route tries to avoid congested global cells while minimizing detours. Global route also avoids pre-routed P/G, placement blockages and routing blockages.
- Track Assignment (TA) assigns each net to a specific track and actual metal traces are laid down by it. It tries to make long, straight traces to avoid the number of vias. DRC is not followed in TA stage. TA operates on the entire design at once.
- Detail Routing tries to fix all DRC violations after track assignment using a fixed size small area known as “SBox”. Detail route traverses the whole design box by box until entire routing pass is complete.
- Search and Repair fixes remaining DRC violations through multiple iterative loops using progressively larger SBox sizes.

#### Execution

1. Click on ``` Tools --> Routing ``` from the window

<img width="167" alt="step-15-routing-1" src="https://user-images.githubusercontent.com/83152452/206907983-34e3733b-ce52-4ef5-8435-05b5c23e6df4.png">

2. Once you enable Routing option, On the Toolbar you will now be able to see the Routing option. Go to ``` Routing --> Auto Route ```. This option will let you to do the routing automatically. If you wish to do the Routing manually you may try by using the metal layers available.

<img width="185" alt="step-15-routing-2" src="https://user-images.githubusercontent.com/83152452/206907989-1acfca04-f1ce-4681-bc39-66f4314a3a5e.png">

3. Click on ALL option from the Nets category. Click on tick option in green color and then check the option.

<img width="842" alt="step-15-routing-4" src="https://user-images.githubusercontent.com/83152452/206908001-9ab7cb3a-f513-4859-a9c2-54e8789fdbc9.png">

<img width="993" alt="step-15-routing-3" src="https://user-images.githubusercontent.com/83152452/206907995-c7a14471-ddc5-4236-9d9b-9d0401d3a461.png">

4. Your Layout will look something like this as shown below. If you got it, then that is amazing. Let's now go to DRC.

<img width="676" alt="step-15-routing-5" src="https://user-images.githubusercontent.com/83152452/206908004-2ef66f78-acfc-49cf-8a24-b33edb38c847.png">



## Physical Verification-SignOff Checks

### DRC

- Design Rule Check (DRC) determines whether the layout of a chip satisfies a series of recommended parameters called design rules. Design rules are set of parameters provided by semiconductor manufacturers to the designers, in order to verify the correctness of a mask set. 
- It varies based on semiconductor manufacturing process. These rule set describes certain restrictions in geometry and connectivity to ensure that the design has sufficient margin to take care of any variability in manufacturing process.

- Input files required for DRC
	• GDS
	• drc rule deck file
- Output of DRC
	• Reports

- Typical DRC rules are :

1. Interior:

Fig1: Distance of interior facing edge for a single layer.
Fig2: Distance of interior facing edge of two layer.							

![1](https://user-images.githubusercontent.com/83152452/206917639-93e74436-b5b5-4186-8a72-c859432faf24.jpg)

![2](https://user-images.githubusercontent.com/83152452/206917643-37988bf9-4851-4d73-8b25-9b3a35868921.jpg)

2. Exterior:

Fig3: Distance of exterior facing edge of two layer								

![3](https://user-images.githubusercontent.com/83152452/206917647-9d00fd50-0fa9-449f-9ae8-aed06eb044b3.jpg)

![4](https://user-images.githubusercontent.com/83152452/206917651-8938adfd-c29b-40cc-8cab-b6e7bbf7dfc6.jpg)

![5](https://user-images.githubusercontent.com/83152452/206917657-b800068b-948e-47ec-befe-8cf8860f4361.jpg)
								
3. Enclosure:
								
Fig4: Distance between inside edge to outside edge.                                                                          

![6](https://user-images.githubusercontent.com/83152452/206917661-526ef30d-3af1-4a4e-8683-ca5a4d3644f7.jpg)

![7](https://user-images.githubusercontent.com/83152452/206917665-73d833e4-9fbf-4146-a157-802bbc81fc9d.jpg)

4. Extension:								

Fig4: Distance between inside edge to outside edge.								

![8](https://user-images.githubusercontent.com/83152452/206917666-d67b974e-1afe-4d99-954f-0d766e91ddd6.jpg)


<img width="499" alt="9" src="https://user-images.githubusercontent.com/83152452/206917671-29387072-ba34-4890-b544-99cb9e24b85c.png">

<img width="505" alt="10" src="https://user-images.githubusercontent.com/83152452/206917672-0629ea06-e61b-4a92-8e6f-4eda929519c7.png">

<img width="506" alt="11" src="https://user-images.githubusercontent.com/83152452/206917675-2138591a-8bc3-4f40-825d-1ae4783f40ed.png">


#### Execution

1. Go to ``` Verification --> DRC --> Setup and Run ``` and then the Dialog box will appear.

<img width="335" alt="step-16-DRC-1" src="https://user-images.githubusercontent.com/83152452/206908765-5c87a019-a362-44da-8934-7374b8921654.png">

2. DRC Setup box will appear. Keep the options default but if in case any options are not selected, then select them accordingly.

<img width="506" alt="drc -1" src="https://user-images.githubusercontent.com/83152452/206909148-b67ddd0c-eba1-4e5e-8e6d-6827f7594f85.png">

Check for Layer map file from Custom options tab as shown below if it is not selected by default.

<img width="506" alt="drc-2" src="https://user-images.githubusercontent.com/83152452/206909150-770ddc3d-5e05-4611-86b5-f3928899a84d.png">

3. If no errors then VUE (Visualization User Environment) window will appear saying the layout is DRC clean.

<img width="646" alt="step-16-DRC-2" src="https://user-images.githubusercontent.com/83152452/206908774-56df4f3f-d172-4592-8720-2e7c610b9dac.png">

4. In the output directory many files are generated. The snapshot below shows the file from the text editor window which shows the DRC Clean status.

<img width="953" alt="step-16-DRC-3" src="https://user-images.githubusercontent.com/83152452/206908784-31c9af76-674c-4226-afaa-38f407b1b3ea.png">

5. Now let's try to generate some errors to see how VUE window displays if any error occurs. I have tried to keep the nmos-pmos spacing to <0.08

<img width="649" alt="step-16-DRC-4-errors-1" src="https://user-images.githubusercontent.com/83152452/206908797-68d3cd9b-25e2-4cec-acbd-5d918b2add6b.png">

6. Now once again try to run DRC, VUE window will generate the errors as shown below:

<img width="958" alt="step-16-DRC-4-errors-2" src="https://user-images.githubusercontent.com/83152452/206908803-9b3a9a68-2eab-4977-ba88-5a4f685bdc13.png">

7. You may check the errors description and try to correct them by looking into the DRC Errors tab in VUE. You can double click on the errors in the error list and check for where the error has occurred.

<img width="431" alt="step-16-DRC-4-errors-3" src="https://user-images.githubusercontent.com/83152452/206908811-ec121466-074a-443a-98c5-5db5ad5d130f.png">

```
NOTE: The output generated files are listed below,

- dff.LAYOUT_ERRORS  - Short summary on the error status whether is it CLEAN or errors occurred.
- dff.RESULTS  - ICV Execution (Pathss), Results Summary (Viloations, rules check), Assign Layer Statistics (Utilized layer or unoccupied), Run Configuration (Processing details –CPU, RAM), Performance Statistics (Runtime seconds, memory usage).
- dff.vue - We can configure and execute a verification run. It easily loads the results and reviews a run summary. Debugs the design by highlighting errors.
- icv.log and stdout.drc.log - Log information
- prefs.xml - It contains the details such as cell name, version, library name, path, run dir, etc. 
- run_details - It is a directory that contains all the rules, tech information that is needed to run the DRC.
- run_icv.sh - Contains the path related information.
- stdout.icvVue.log - Contains the color related information of the layout (cells, Blocks, vertical & horizontal routing).
```


### LVS

DRC only verifies that the given layout satisfies the design rules provided by the fabrication unit. It does not ensure the functionality of layout. Because of this, idea of LVS is orginated. This blog focuses on how LVS works and what all are the common issues faced in LVS.

Input files required for LVS
    
	• .v – netlist of the design
	• GDS – layout database of the design
	• LVS rule deck

Outputs for LVS

	• reports


#### LVS Flow

<img width="589" alt="lvs flow" src="https://user-images.githubusercontent.com/83152452/206909633-7e604d47-cb05-4c69-b0f1-66d7c62326d4.png">

- .v and GDS should be of same stage :

LVS rule deck is a set of code written in Standard Verification Rule Format (SVRF) or TCL Verification Format (TVF). It guides the tool to extract the devices and the connectivity of IC’s. It contains the layer definition to identify the layers used in layout file and to match it with the location of layer in GDS. It also contains device structure definitions.

- LVS check involves three steps :

	1. Extraction: The tool takes GDSII file containing all the layers and uses polygon based approach to determine the components like transistors, diodes, capacitors and resistors and also connectivity information between devices presented in the layout by their layers of construction. All the device layers, terminals of the devices, size of devices, nets, vias and the locations of pins are defined and given an unique identification.
	
	2. Reduction: All the defined information is extracted in the form of netlist.
	
	3. Comparison: The extracted layout netlist is then compared to the netlist of the same stage using the LVS rule deck. In this stage the number of instances, nets and ports are compared. All the mismatches such as shorts and opens, pin mismatch etc.. are reported. The tools also checks topology and size mismatch.

##### Commonly faced LVS issues

- LVS check includes following comparisons:

	• Number of devices in schematic and its layout
	• Type of devices in schematic and its layout
	• Number of nets in schematic and its layout

- Typical errors which can occur during LVS checks are:

	1. Shorts: Shorts are formed, if two or more wires which should not be connected together are connected.
	2. Opens: Opens are formed, if the wires or components which should be connected together are left floating or partially connected.
	3. Component mismatch: Component mismatch can happen, if components of different types are used (e.g, LVT cells instead of HVT cells).
	4. Missing components: Component missing can happen, if an expected component is left out from the layout.
	5. Parameter mismatch: All components has it’s own properties, LVS tool is configured to compare these properties with some tolerance. If this tolerance is not met, then it will give parameter mismatch.


##### IR Drop Analysis

- IR Drop can be defined as the voltage drop in metal wires constituting power grids before it reaches the vdd pins of the cells. IR drop occurs when there are cells with high current requirement or high switching regions. IR drop causes voltage drop which in-turn causes the delaying of the cells causing setup and hold violations. Hold violations cannot be fixed once the chip is fabricated.
There are two types of IR drop analysis namely:
Static IR drop analysis :
	• Calculates the average voltage drop of entire design assuming current drawn across is constant.
	• As average current is calculated this analysis depends on time period. This analysis is good for signoff checks in older technology.

- Dynamic IR drop analysis :
	• Depends on switching activity of the logic.
	• Is vector dependent .
	• Less dependent on clock period as depends on instantaneous current.
	• Analysis of peak current demand and highly localized cells.

- Methods to reduce IR drop :
	• Robust power mesh– Initial power grid is made based on static IR analysis due to late availability of switching activity. If there is IR drop due to some of the clustered cells then adding a strip will make the power mesh more robust.
   • De-cap– These are decoupling capacitors which are spread across the high switching region to maintain the voltage.
	• Spacing– If clock cells are clustered and causing IR drop, then by spacing them apart near to different power rails will reduce the IR drop. While    shifting the cell to next power rail, it should be made sure that the power rail is not driving many cells, because adding another cell may give IR drop.
	• Reducing load– Cells driving more load will be drawing more current. Hence reducing load will reduce IR drop.
	• Downsizing– Cells of smaller size will draw less current. But the transition of cells should not become worse.
	• The number of power switches can be increased to reduce IR drop
	• It should be made sure that all the power pins of macros are properly connected to the power rails.


#### Execution

1. Go to ``` Verification --> LVS --> Setup and Run ``` and then the Dialog will appear.

<img width="335" alt="step-17-LVS-1" src="https://user-images.githubusercontent.com/83152452/206909643-ed3f50e9-0731-4b05-8779-e27496dfa0be.png">

2. LVS Setup box will appear. Keep the options default but if in case any options are not selected, then select them accordingly.

<img width="629" alt="lvs-1" src="https://user-images.githubusercontent.com/83152452/206909651-ec3a88a1-14ba-4980-9422-008ecaf0b4e4.png">

Check for Layer map file from Custom options tab as shown below if it is not selected by default.

<img width="628" alt="lvs-2" src="https://user-images.githubusercontent.com/83152452/206909656-86889aa9-ddf1-468a-b971-c93f80072815.png">

3. If no errors then VUE (Visualization User Environment) window will appear saying the layout is LVS clean.

<img width="646" alt="step-17-LVS-2" src="https://user-images.githubusercontent.com/83152452/206909667-8b357b33-dbca-4cff-8cde-be541ab5e6f5.png">

4. In the output directory many files are generated. The snapshot below shows the file from the text editor window which shows the LVS Clean status.

<img width="950" alt="step-17-LVS-3" src="https://user-images.githubusercontent.com/83152452/206909672-f427b3a2-4c1b-4fd9-aa15-1dd94fd009d4.png">

5. Now let's try to generate some errors to see how VUE window displays if any error occurs. Here, I have tried to change the W value of pmos which doesn't match with the schematic W value of pmos. Hence, It much generate LVS errors.

<img width="803" alt="step-17-LVS-4-error-1" src="https://user-images.githubusercontent.com/83152452/206909680-e22fbb10-e75f-40de-95c2-870955665017.png">

6. Now once again try to run LVS, VUE window will generate the errors as shown below:

<img width="955" alt="step-17-LVS-4-error-2" src="https://user-images.githubusercontent.com/83152452/206909681-ddb268fb-ec09-4b54-b34c-72d4bc877bf7.png">

7. You may check the errors description and try to correct them by looking into the LVS Errors tab in VUE. You can double click on the errors in the error list and check for where the error has occurred. In this case, It clearly shows that there is a mismatch error.

<img width="430" alt="step-17-LVS-4-error-3" src="https://user-images.githubusercontent.com/83152452/206909687-4db9adf4-c467-485c-9ac6-e06dc9344d99.png">

``` 
NOTE : When you edit any properties in Layout editor and save it that doesn't match with the Schematic editor. You can observe that there is a red mark on M1 in Design Navigator. So, you can look into that.

<img width="953" alt="step-17-LVS-4-error-4-grey color indication" src="https://user-images.githubusercontent.com/83152452/206909688-31d1e8ff-b125-4cdc-87c9-57037672ca3d.png">

```


### XOR

- Compares and reports differences among polygons, edges, and/or text in two layout databases
- Comparison methods: XOR (default), NOT, or OUTSIDE
- Supported layout formats: OpenAccess (default), GDSII, OASIS
- Checking Area:
     Design – complete layout
     Viewport – selected viewport in layout
     Region – allows user to enter bbox coordinates from layout

#### Execution

1. You can go to ``` Verification --> LVL --> Setup and Run ``` from the Layout editor window. Here XOR operation is implemented on similar cells.

<img width="239" alt="step-18-XOR-1-similar files" src="https://user-images.githubusercontent.com/83152452/206910330-cca8daa9-0444-4316-93fe-f83699586de4.png">

2. As you can see that there were no dissimilarities found in these two cells as they are the same, this was expected.

<img width="958" alt="step-18-XOR-2-similar files" src="https://user-images.githubusercontent.com/83152452/206910335-dd1a4e03-7f25-4235-bb2a-99c47817a3eb.png">

3. Once again run the LVL operation. Here XOR operation is implemented on dissimilar cells.

<img width="239" alt="step-18-XOR-3-dissimilar files" src="https://user-images.githubusercontent.com/83152452/206910351-a4da60d4-44c8-4ff7-ab3b-74c0ee3e700d.png">

4. As you can see that there are many violations found in these two cells as they are not the same, this was also expected.

<img width="959" alt="step-18-XOR-4-dissimilar files" src="https://user-images.githubusercontent.com/83152452/206910357-9160ef18-30a6-4e83-a6d0-9f24bce92b39.png">



## Acknowledgements

- [Synopsys](https://www.synopsys.com/)
- [Kunal Ghosh](https://github.com/kunalg123), Co-founder, VSD Corp. Pvt. Ltd.



## Author

- [A Devipriya](https://github.com/Devipriya1921), B.E (Electronics and Communication Engineering), Bangalore - adevipriya1900@gmail.com


## References

- [Synopsys](https://www.synopsys.com/)
- [Emad-H](https://github.com/Emad-H/Physical-Verification-using-SKY130#fundamentals-of-design-rule-checking)
- VLSI Design Cycle - GeeksforGeeks
- ASIC Physical Design Flow - VLSIFacts
- CMOS Inverter : Circuit, Working, Characteristics & Its Applications (elprocus.com)














