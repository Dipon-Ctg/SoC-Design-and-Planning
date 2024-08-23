# SoC-Design-and-Planning

## Table of Contents

- [About the project](#about_the_project)
  - [Built with](#built_with)
- [Getting Started](#getting_strated)
  - [Prerequisites](#prerequisites)
  - [Installation](#installation)
- [Roadmap](#roadmap)
- [Theory](#theory)
  - [Introduction to IC Design, Components and Terminology](#Introduction_to_IC_Design,_Components_and_Terminology)
  - [Good Floorplan Vs Bad Floorplan and Introduction to Library cells and placement](#Good_Floorplan_Vs_Bad_Floorplan_and_Introduction_to_Library_cells_and_palcement)
  - [Routing](#routing)
- [Lab](#lab)
  - [Lab 1: Getting Familiar with Openlane EDA tools](#Lab_1_Getting_Familiar_with_Openlane_EDA_tools)
  - [Lab 2: Steps of run floorplan and placement using OpenLANE](#Lab_2_Steps_of_run_floorplan_and_placement_using_OpenLANE)
  - [Lab 3: Inverter Characterization using Sky130 Model Files](#Lab_3_Inverter_Characterization_using_Sky130_Model_Files)
  - [Lab 4: Pre-layout timing analysis & importance of good clock tree](#Lab_4_Pre-layout_timing_analysis_&_importance_of_good_clock_tree)
  - [Lab 5: Final Steps to generate GDSII](#Lab_5_Final_Steps_to_generate_GDSII)
- [Acknowledgements](#acknowledgements)
- [Author](#author)


## About the project
Welcome to the workshop for OpenLane! We will explore the process of creating an Application Specific Integrated Circuit (ASIC) using the OpenLane ASIC flow, starting from the Register Transfer Level (RTL) and ending with the Graphical Data System (GDS) file. The process consists of multiple essential phases, commencing with an RTL file and concluding with a GDS file

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Ref/openlane.flow.1.png)


### Built with
Any ASIC implementation revolves around Place and Route (PnR), and Openlane flow includes several essential open-source tools that carry out each of the PnR steps. The steps and corresponding tools that Openlane calls for the specified features are listed below:
- Synthesis
  - Generating gate-level netlist (yosys).
  - Performing cell mapping (abc).
  - Performing pre-layout STA (OpenSTA).
- Floorplanning
  - Defining the core area for the macro as well as the cell sites and the tracks (init_fp).
  - Placing the macro input and output ports (ioplacer).
  - Generating the power distribution network (pdn).
- Placement
  - Performing global placement (RePLace).
  - Performing detailed placement to legalize the globally placed components (OpenDP).
- Clock Tree Synthesis (CTS)
  - Synthesizing the clock tree (TritonCTS).
- Routing
  - Performing global routing to generate a guide file for the detailed router (FastRoute).
  - Performing detailed routing (TritonRoute)
- GDSII Generation/layout
  - Streaming out the final GDSII layout file from the router def (Magic).
##  Getting Started

### Prerequisites
The session expands upon an Ubuntu (only) system and Openlane. Installing a virtual machine on Windows and running the "vsdworkshop" image file are the user's tasks; on Ubuntu, installing "VirtualBox" and following the lab instruction file's instructions are required. Students who register are given access to their accounts and all necessary resources.

### Installation
When the 'vsdworkshop' operating system is installed, all required files and tools are also installed. Follow the link if you wish to install it manually: [GitHub](https://github.com/nickson-jose/openlane_build_script)

## Roadmap
A thorough design path in integrated circuit (IC) development, Register Transfer Language (RTL) to Graphic Data System II (GDSII) converts a high-level hardware description into a physical layout suitable for foundry manufacturing. Starting with the RTL design, when the circuit's functionality is programmed into the hardware description languages using Verilog or VHDL, the RTL to GDSII process consists of many phases. The next step in the process is called synthesis, which turns this RTL code into a gate-level netlist. The physical design process begins with netlist creation and proceeds to floorplanning, placement, clock-tree synthesis (CTS), and routing. Signoff checks, such as Design Rule Checking (DRC), Layout Versus Schematic (LVS) checks, and Static Timing Analysis (STA), are performed following placement and routing.

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Ref/complete%20flow.png)

### Introduction to IC Design, Components and Terminology
The process of connecting circuit elements to carry out a specified intended function is known as integrated circuit (IC) design. ICs are found on almost every electrical gadget you use. Since Gordon Moore noted in 1965 (Moore's Law (opens in a new tab)) that the number of transistors in an integrated circuit (IC) doubles every two years, a lot has happened.  There are two main domains of IC design—digital and analog—and most real-world ICs are a mix of the two.
IC Design Flow:
There are many steps involved in the IC design flow are-
  -	Architecture design
  -	Logic design
  -	Physical design
  -	Physical verification
  -	Final step- The Signoff

**Logic design:** To achieve the necessary functionality of the IC, macro-level building pieces are constructed and linked here. Usually, pre-existing components like sensors, processors, and memory are utilized. Circuit elements with high-level functional descriptions are broken down into the necessary low-level circuit parts. Software known as logic synthesis automates this procedure. To confirm that the design works, the set of devices is simulated. A digital logic simulator or an analog circuit simulator will be employed, contingent on the necessary level of modeling detail.

**Physical Design:** This stage involves creating a layout for the linked forms of the necessary circuit components. To ascertain the position, form, and dimensions of the modules within a chip, a floorplan is initially created. Analyzing the chip area, latency, congestion, etc. helps determine where to put the components and prevent problems that might limit speed and performance.
PnR, the central component of the ASIC design flow, is made up of many phases. The steps and corresponding tools that OpenLANE uses are listed below:
  -	Synthesis: creates a gate-level netlist, maps cells, and sets up pre-layout STA.RTL design, library (standard cells and macros), and restrictions (design objectives, anticipated temporal behavior, and surroundings) are the inputs.
  ![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Ref/synthesis.png)
  - Floorplanning: Major decisions are made at this stage, such as how to divide the system into blocks and subsystems, arrange the blocks on the chip, and assign memory, macros, standard cells, and other resources. Power planning and IO cell layout occur during floorplanning.
  ![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Ref/floorplan.png)
  -	Placement: The design’s stdcell placement is determined by the placement phases. This stage involves estimating the wire length, then placing the wires while taking that estimate into account. Placement consists of three steps of flow for completing the whole placement stage. The typical flow of placement is - Global placement, Legalization, and Detailed placement.
    ![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Ref/placement.png)
  -	Clock-Tree synthesis (CTS): This stage involves wiring the clock network and implementing the clock tree netlist, which includes buffers. This stage aims to reduce power dissipation and skew as much as possible. There are several different ways to disseminate the clock around the network, here are a few examples: Ad hoc, Grids, H-tree, Hybrid etc.
      ![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Ref/H-tree.png)
  -	Routing: Except for the clock and power supply, routing designs the wire arrangement for every net. There are two types of routing: Details Routing and Global routing. In the planning step known as global routing, the whole routing region is divided into rectangular tiles to construct a routing plan for a specific net. Each pre-assigned global tiles, where the real wires and vias are generated, has their actual routing determined by the detailed router.
    ![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Ref/routing.png)
   	![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Ref/routing2.png)

### Good_Floorplan_Vs_Bad_Floorplan_and_Introduction_to_Library_cells_and_palcement
**Chip Floorplanning Considerations:**
Utilization Factor and Aspect Ratio: The Ratio of Utilization and Aspect Ratio we must first determine the height and width of the core and die regions to calculate the Utilization Factor and Aspect Ratio.
  -	The core of a chip is where all the logic cells and other parts are located. That's where a chip's logic is found.
  -	The die, which surrounds the core region, is where I/O-related components are placed.
  ![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/floorplan_IP_Pin/Screenshot%202024-08-22%20113921.png)

The size of the core region will be decided by the design's netlist, which considers the quantity of parts required to carry out the design logic. Thus, the dimensions of the core region determine the height and breadth of the die area, as seen in the above picture. Take a netlist with two logic gates and two flip-flops, for instance.
  ![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/floorplan_IP_Pin/ff2.png)

If every component has a surface area of one square unit. Since there are 4 components in our netlist, a minimum of 4 square units will be needed for the core area.

  ![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/floorplan_IP_Pin/ff3.png)
  - **Utilization Factor:** The Utilization Factor is the ratio of the core area that the netlist occupies to the overall area of the core. The Utilization Factor should be less than 1 in an efficient floor layout since if it hits 1, there won't be any more room for logic to be added, which results in a poor floor plan.
    - Utilization Factor = (Area occupied by netlist / Total core area)
  - **Aspect Ratio:** The height-to-width ratio of the core is known as the aspect ratio. The core is said to be square-shaped when the aspect ratio is 1. The core will be shaped like a rectangle if the aspect ratio is not 1.
    - Aspect Ratio = (Height of the core / Width of the core)
    
    ![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/floorplan_IP_Pin/ff4.png)

    - In this case, when calculated
      - Utilization factor = (4 sq units) / (4 sq units) = 1
      - Aspect Ratio = (2 units) / (2 units) = 1 //The core is in a square shape.
        
    ![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/floorplan_IP_Pin/ff5.png)

    - In this case, when calculated
      - Utilization factor = (4 squints) / (8 squints) = 0.5
      - Aspect Ratio = (2 units)/(4 units) = 0.5 / The core is in a rectangular shape.

**Decoupling Capacitor:**
There is an input transition area in IC where the nMOS and pMOS are conducted simultaneously. For that moment, there will be a significant short circuit current. A significant current will be needed if many of these cells are arranged in a row and switch simultaneously. This high current need, also known as voltage droop or ground bounce, has the potential to lower the VDD or raise the ground voltage. Decoupling capacitors or decap cells are positioned next to these power-hungry components to solve this problem. Currently, the decoupling capacitors rapidly discharge to provide these components with the required power when switching activities take place. The capacitors replenish when the switching is turned off, guaranteeing a steady and dependable power supply to important circuit components. Consequently, decap cells serve as charge reservoirs to maintain and improve the power delivery network. To preserve steady operation and avoid performance problems brought on by varying power supply circumstances, decaps are crucial in circuit design.

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/floorplan_IP_Pin/decaps.png)

**Power Planning/ Pre-Placement:**
It takes more than just decoupling capacitors to control how electricity is distributed across different blocks. Limitations associated with de-cap cells include greater chip size and leakage power. Electricity planning is therefore utilized to supply electricity efficiently. Two effects are possible in regions of the chip where there is a lot of switching activity: ground bounce and voltage drop. Voltage Drop: A large power demand occurs when several cells flip from 0 to 1 at the same time. These cells need a decrease in input voltage owing to inadequate power if the power comes from a single source. When the voltage level drops below the noise margin, this problem becomes more serious.
A ground bounce occurs when several cells simultaneously flip from 1 to 0, dumping power to the ground pin at the same moment. As a result, the ground voltage rises momentarily rather than staying at zero. This causes "ground bounce," a phenomenon. When the voltage level is higher than the noise margin, a problem occurs.

  ![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/floorplan_IP_Pin/pp2.png)
    
  ![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/floorplan_IP_Pin/pp.png)

**Library binding and placements:**
Every element in the netlist, such as the AND, OR, and FF gates, has a corresponding physical dimension that is either square or rectangular, meaning it has width and height. The library has these parts in the form of boxes. For these boxes or components, the library includes temporal (delay) information in addition to dimensions. Information regarding when the component's output is modified is also contained in the library. Furthermore, the same component exists in several versions, each with unique characteristics (size/delay).

**Placement:**
Placement is the process of placing of all standard cells that are present in the netlist by the tool into the core area. The tool also optimizes the design while placing. Trail routing also can be done by the tool. The main goal of placement is – 1. to timing, area, and power optimization. 2. Minimize congestion and congestion hotspots 3. Minimum cell density, pin density.
  - Placement is done by three steps: 1. *Global Placement* 2. *Legalization* 3. *Details Placement*.

### Routing

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Ref/Route.png)

The green region in the associated picture represents the picorv32a design. The blue pads serve as the ground connection, while the red pads are for power.
Power for the rectangular close-loop rings comes from the pads. Power straps are the vertical lines that attach to the rings. Vertical straps hold the power and ground rails of the standard cells. Standard cells need to be higher than the rail pitch to guarantee correct power and ground connection. The power flows from the outside to the pads, pads to rings, rings to strap/stripe, and strap/stripe to stdcell rows as depicted in this figure.
Like placement, routing occurs in three steps. However, the TritonRoute tools slow down the Global and Detail routing stages.
  - Global Route- The global route generates a routing guide to rapidly construct a high-level routing solution. The routing guide is made up of boxes that represent cell pins.
  - Detail Route- The global route's output, including the routing guidelines, is used by the detailed route. TritonRoute is used to perform detailed routes. To determine which route point has the best connectivity overall, algorithms are used.
    ![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Ref/route3.png)

