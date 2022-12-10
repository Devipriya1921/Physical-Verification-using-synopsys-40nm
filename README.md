# Physical Verification (PV) Using Synopsys 40nm

# Contents

1. Overview
   - VLSI Design Flow chart
   - Before steps to Physical Verification 
        - PHYSICAL DESIGN STEPS
2. Physical Verification Concepts
   - DRC (Design Rule Check)
   - LVS (Layout vs Schematic)
   - ARC (Antenna Rule Checking)
   - ERC (Electrical Rule Checking)
3. EDA Tools
   - Overview
   - IC Validator












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

1. FLOOR-PLANNING: The first step in physical design is floor planning. In this process, the structures are identified which should be placed together meeting 
the performance and timing requirements. The desire to have everything close to everything else is followed. But based on the area and hierarchy, a suitable 
floor plan is decided. Floor planning is defined as taking account of macros used in the design, memory, other IP cores and their placement needs, the routing 
possibilities and also the area of the entire design.  Area and Speed are two factors which can be trade off against one another. Optimizing the design for 
minimum area  allows the design to use fewer resource and thus increasing the speed of the system.

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



## Physical Verification Concepts

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



## EDA Tools 

### Overview 

Custom Compiler
IC Validator
PrimeWave


### Installation of Synopsys EDA Tools

If you are looking for steps to install the Synopsys EDA Tools you may check out from the official Website: [Synopsys](https://www.synopsys.com/) or from 
the Google source: [Here](https://teamvlsi.com/2021/06/installation-of-synopsys-eda-tools.html)


### IC Validator

<img width="305" alt="icv" src="https://user-images.githubusercontent.com/83152452/206859524-f6559f20-4d82-4638-ab60-fec910268212.png">




























