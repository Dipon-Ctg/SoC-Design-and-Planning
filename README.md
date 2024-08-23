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

## Lab

### Lab 1: Getting Familiar with Openlane EDA Tools
Useful Linux Command: 
```
pwd: It displays the present working directory and its path.
cd: Using this command we can move in both ways in the directory tree.
ls: It lists all the sub-directories and files present in the current directory.
mkdir: Using this command, we can create a new directory.
rmdir: Using his command, we can delete an existing directory.
cp: For copying the file.
gedit: Open file with text editor (gedit).
help: Using this command we can know the working of any command.
clear: This command clears the terminal.
```
**Libs file:**
Lib file is a short form of Liberty Timing file. LIB file is an ASCII representation of timing and power parameters associated with cells inside the standard cell library of a particular technology node. Lib file is basically a timing model file that contains cell delay, cell transition time, setup and hold time requirement of the cell. So in the lib file, we will find the timing and electrical characteristics of a cell or macros. Lib file is generated and provided to the ASIC designer by a standard cell library vendor or founder if the foundry provides a standard cell library. 

**Lef file:**
Library exchange format (LEF) is also termed as physical library because this file contains physical abstract information of layers and standard cells/macros. It is implemented in ASCII format, so it can be readable by humans. These physical libraries are generated by library vendors and foundries. Physical libraries are of two types:
  - i) Technology LEF
  - ii) Standard cell/macro LEF

**Design Preparation Step:**
To enter bash while being in the openalne directory use the command:
``` 
docker
```
for the step-by-step openlane flow execution enter the following command
```
./flow.tcl -interactive
```
Now, we are in the Openlane. To input the required packages, use the following command:
``` 
% package require openlane
```
As there are various pre-built designs in the design’s subdirectory, we are choosing the "picorv32a.v" design on which we will implement the RTL to GDS flow. To carry out the synthesis on this design, we first need to set it up using the below command:
```
prep -design picorv32a
```
After preparation is complete, we can see a new directory with the latest date is created within the ```runs``` folder in ```picorv32a``` directory.

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/environment.png)

```
/home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs
```

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/directory_in_run.png)

To perform synthesis on the design, use the following command:
```
run_synthesis
```
**Results (Synthesis):**

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/syn/1.png)

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/syn/4.png)

From the synthesis we get-
```Number of D-Ff: 1613``` 
```Number of cells: 14876```
Now, we can calculate the FF (Flip Flop) ratio and FF percentage from the data according to the formula: 
  - Flip Flop Ratio (Number of D-FF) / (Total number of Cells). For FF percentage FF ratio * 100.

We get-
```FF ratio in our design is – 0.1084``` 
```And FF percentage is – 10.84%``` 

For more information, look at the results directory in synthesis.

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/syn/syn.png)

### Lab 2: Steps of run floorplan and placement using OpenLANE

To achieve an error-free floorplanning process, designers need to be aware of specific factors, referred to as switches, that might have a big influence on the floorplan. Aspect ratio and usage factor, for example, are two crucial choices. Before beginning the floorplanning phase, designers must confirm that these criteria meet the project requirements. 
The file ```README.md``` in the openlane configuration displays the different variables of the design flow. The path is shown below:

```
/home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/configuration
```
![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/syn/2.png)

Below image shows different types of switches in floorplan stage after running floorplan.

```
run_floorplan
```
![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/floorplan/7.png)

The image below illustrates various types of switches involved in the floorplanning phase and their description.

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/floorplan/2.png)

The default value of these variables is set in the floorplan.tcl file of openlane configuration. The file path is the same as for ```README.md``` and is shown below:

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/floorplan/3.png)

In the figure above several floorplan variables are set by default. One important variable to mention here is ```FP_IO_MODE``` below:
```
set ::env(FP_IO_MODE) 1; # 0 matching mode - 1 random equidistant mode
```
which is set as '1' when IO pins are random equidistant.

**Floorplan results**
After floorplan execution we go to the runs folder of picorv32a and open the latest date file name. Here one can check the implemented floorplan variables from the logs. 
The file name ```ioPlacer.log``` will give metal layers number for verical and horizontal IO pins. The path to the file is below:

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/floorplan/8.png)

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/floorplan/9.png)

Recall that the floorplan will have ```FP_IO_VMETAL 5``` if ```FP_IO_VMETAL``` was set to 4, and the floorplan will have ```FP_IO_HMETAL 4``` if ```FP_IO_HMETAL``` was set to 3.

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/floorplan/10.png)

**Layout view in Magic:**

When the floorplaning is completed, to view the results go to the path as shown below:

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/floorplan/11.png)

open the design exchange file ```(.def):``` These results are useful. For example: we can see the die area:

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/floorplan/12.png)

Now, to open this ```".def"``` file in magic, use the following command:
```
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def
```

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/floorplan/magic/1.png)
Design Alignment Instructions:

  - Centering the design:\
  ```1. Press s to select the entire design```\
  ```2. Press v to vertically align it to the middle of the screen.```
  - Zooming In on a specific area:\
  ```1. Left click and drag to select the desired region.```\
  ```2. Right-click to bring up the context menu.```\
  ```3. Press Z to zoom in on the selected area.```
  - Getting Details of a Cell:\
  ```1. Move your cursor to the cell of interest.```\
  ```2. Press S to select the cell.```\
  ```3. In the tkcon window, enter the command "what" to display cell details.```

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/floorplan/magic/2.png)

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/floorplan/magic/3.png)

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/floorplan/magic/4.png)

**Placement**
To initiate the placement process, use the following command:
```
run_placement
```
During placement execution the reduction of half parameter wire length is the main focus. The placement is stop when the overflow is converged After the Placement is done. To view the results Go to the following location:
```
/home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/10-08_13-18/results/placement
```
And then we can see ```'picorv32a.placement.def'``` file. To open it using MAGIC use the following command:
```
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def
```

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/placement/5.png)

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/placement/6.png)

### Lab 3: Inverter Characterization using Sky130 Model Files
**Make some changes while being in the flow:**
Open lane allows you to make some changes during the flow, without interrupting the complete flow.
Variables in the floorplan, such as IO mode and core utilization, can be altered. As an illustration, to modify the layout's IO pin alignment, we must first confirm the pins presence of the pins. Navigate to the directory shown in the following image:
```
/home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/10-08_13-18/results/floorplan
```
Then use the command to open the ```‘.def'``` file in magic:
```
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def
```

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/floorplan/magic/3.png)

Pins are spaced at random, as may be observed. IO Placer (an open-source EDA tool) supports four techniques. Now, open the floorplan and navigate to the following directory if you want it to switch to a different IO pin floorplan.tcl data:

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/floorplan_IP_Pin/1.png)

From here we can see the switching variable ```FP_IO_MODE = 1```, hence pins are randomly equidistant. Now, we run the following command and change the IO placer settings:
```
set ::env(FP_IO_MODE) 2
```

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/floorplan_IP_Pin/2.png)

Now, we can check the change in the IO placer strategy: We can see that ```.def``` file has been updated from the time stamps and date:

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/floorplan/11.png)

Now, open it on magic

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/floorplan_IP_Pin/3.png)

Clone ```‘vsdstdcelldesign’``` repo from git:
The repository ```"vsdstdcelldesign"``` contains the .mag file for the inverter and spice models for sky130 nmos/pmos transistors.
Git repo link:
```
git clone https://github.com/nickson-jose/vsdstdcelldesign.git
```
Now, open the sky130_inv.mag file in magic:
```
magic -T sky130A.tech sky130_inv.mag &
```

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/floorplan_IP_Pin/4.png)

**Sky130 INVERTER Basic Layout:**
The source, drain, gate, VDD, and ground terminals of the inverter opened in the magic tool are shown in Figure below. When polysilicon crosses the ndiffusion region it is termed as 'NMOS' and when it crosses the pdiffusion region it is termed as 'PMOS', the same is verified in the image below:

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/Sky130_INV.png)

Create STD cell layout:
Details to create a std cell layout are here- [GitHub](https://github.com/nickson-jose/vsdstdcelldesign?tab=readme-ov-file)

Extract the spice netlist in Magic-
In the tckon window, use the following command:
```
extract all
ext2spice cthresh 0 rthresh 0
ext2spice
```

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/inv/4.png)

Now let's open the extracted SPICE file of sky130A inverter:

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/inv/5_spice.png)

**Final Spice Deck:**
Make the following changes in the ```'sky130_inv.spic'``` file:

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/inv/6_ext.png)

Now to simulate in ngspice, use the following command while in the 'vsdstdcelldesign' directory:
```
ngspice sky130_inv.spice
```

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/Tech_File/2.png)

Now, to open the plot use plot y vs time a in the ngspice terminal

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/Tech_File/3.png)


![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/Tech_File/4.png)

**Characterization of inverter:**
  -	Rise Time: The time for the output waveforms to transition from 20% to 80% of its maximum value.
```From plot points: (x0 = 2.18192ns, y0 = 0.66049) to (x0 = 2.24571ns, y0 = 2.64018). Calculated Rise Time = 0.0634 ns```
  -	Fall Time: The time for the output waveform to transition from 80% to 20% of its maximum value.
```From plot points: (x0 = 4.0525ns, y0 = 2.63976) to (x0 = 4.09516ns, y0 = 0.659249). Calculated Fall Time = 0.0422 ns```
  -	Propagation Delay (Cell Rise Delay): The time for the output to transition 50% in response to a 50% change in the input.
```From plot points: Input (x0 = 2.15018ns, y0 = 1.65018) to Output (x0 = 2.21088ns, y0 = 1.65). Calculated Propagation Delay = 0.064 ns```
  -	Cell Fall Delay: The delay for the output to transition 50% due to a 50% change at the input.
```From plot points: (x0 = 4.04997ns, y0 = 1.65) to (x0 = 4.07748ns, y0 = 1.65). Calculated Cell Fall Delay = 0.0277 ns.```

**DRC Rules:**
The details about the MAGIC tool and its DRC rules can be seen [Magic](http://opencircuitdesign.com/magic/).

We use the following command to download the Lab files used in this tutorial. The current location should be the home directory:
```
sudo wget http://opencircuitdesign.com/open_pdks/archive/drc_tests.tgz
```
Once downloaded the zip file we extracted.

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/drc/1.png)

Introduction to Magic & Steps to load sky130 tech-rules:
Run the ```command magic -d XR``` to open the Magic tool.
Now open the ```met3.mag``` file in magic.

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/drc/2.png)

Fixing the error (Poly.9)

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/drc/3.png)

Using the ```"box"``` command, measure the distance between the poly resistor and poly when you zoom in on the "Incorrect poly.p" layout. The measurement indicates a 0.210 µm spacing, which is less than the 0.480 µm "poly.9" DRC rule. However, there is no DRC mistake. Now let's fix this issue

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/drc/6.png)

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/drc/7.png)

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/drc/4.png)

Go to the ```drc_tests``` directory and open the ```Sky130a.tech``` file. Look up "poly.9" and make the adjustments depicted in the following pictures. Once the changes are made, save the file.

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/drc/8.png)

Reload the ```sky130A.tech``` file and re-run the DRC check by running the following commands in tkcon.

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/drc/9.png)

### Lab 4: Pre-layout timing analysis & importance of good clock tree

Commands to open the custom inverter layout
```
cd Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign
magic -T sky130A.tech sky130_inv.mag &
```
Command for tkcon window:
```
help grid
grid 0.46um 0.34um 0.23um 0.17um
```

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/timing/1.png)

The whole li1 layer is on the grid layer. The locations of the input and output ports are where the vertical and horizontal tracks converge. This fulfills the first requirement.

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/timing/con1.png)

Additionally, we can observe that the standard cell's width equals the three grid/track boxes. As previously stated, the height and breadth of the standard cell should both be odd multiples of the vertical track pitch and the horizontal track pitch. This fulfills the second requirement.

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/timing/con2.png)

Open the complete layout after saving it with a unique name.
```
save sky130_cinv.mag
```
![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/timing/2.1.png)

Generate lef from the layout by command.
```
lef write
Let’s open the LEF file
```

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/timing/4.1.png)

Next, we plug in the LEF file in the ```picorv32a``` design.
**Introduction to timing libs and steps to include new cells in the synthesis:**
The Synthesis, Placement, and Route phases are now repeated. Our unique cell is added to the picorv32a Openlane Design Flow for this purpose. We ensure that the PWD we have is
```
/home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign. From here, we copy the .lef file using the following commands:
```
```
cp sky130_vsdinv.lef /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src
```
For the synthesis step, we need the std cell library files. Therefore we copy the .lib files from the directory vsdstdcelldesign/libs using the following commands:
```
cp sky130_fd_sc_hd__* /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src
```
Every standard cell's characterisation data (cell power, cell increase, cell transition, etc.) is contained in the```.lib``` file. Different speeds, temperatures, and voltage values are described for the files ```sky130_fd_sc_hd__typical.lib```, ```sky130_fd_sc_hd_fast.lib```, and ```sky130_fd_sc_hd__slow.lib```. The nmos/pmos transistors in ```sky130_fd_sc_hd__typical.lib``` are described at a temperature of 25°C and a voltage of 1.8 volts, meaning they are neither fast nor slow. Our customized cell will be added to all of the typical, slow, and fast libraries.

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/timing/fast_lib1.png)

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/timing/fast_lib2.png)

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/timing/slow_lib1.png)

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/timing/slow_lib2.png)

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/timing/typical_lib1.png)

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/timing/typical_lib2.png)

Now, we need to modify the ```config.tcl``` file: Go to the picorv32a directory and open the file using gedit and we make the following modifications:

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/timing/5.png)

Now, launch the docker following the standard procedures as indicated, and include some commands:
```
./flow.tcl -interactive
package require openlane 0.9
#to continue the work in the already made directory in the runs folder
prep -design picorv32a -tag  18-08_15-37 -overwrite
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs
```

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/overwrite.png)

Now run synthesis by command
```
run_synthesis
```

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/timing/6.png)

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/timing/6.1.png)

It is evident from the preceding image that synthesis was effective. 1554 instances of our cinverter are utilized in total. Additionally, we can observe that the ```overall negative slack is -711.59``` and the ```worst slack is -23.89```. ```147712.918``` is the chip area.
At this point, we can attempt a timing-driven synthesis to reduce the slack. To improve the delay, we must trade off the chip area for this.
We opened the README.md from the directory and saw the variable ```‘SYNTH STRATEGY’``` ```‘SYNTH BUFFERING’``` ```‘SYNTH SIZING’``` ```‘SYNTH DRIVING CELL’```. Change the variable to reduce the slack.

```
# Now once again we have to prep the design to update the variables
prep -design picorv32a -tag 18-08_15-37  -overwrite
# Addiitional commands to include newly added lef to openlane flow merged.lef
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs
# Command to display the current value of variable SYNTH_STRATEGY
echo $::env(SYNTH_STRATEGY)
# Command to set a new value for SYNTH_STRATEGY
set ::env(SYNTH_STRATEGY) "DELAY 3"
# Command to display the current value of variable SYNTH_BUFFERING to check whether it's enabled
echo $::env(SYNTH_BUFFERING)
# Command to display the current value of variable SYNTH_SIZING
echo $::env(SYNTH_SIZING)
# Command to set a new value for SYNTH_SIZING
set ::env(SYNTH_SIZING) 1
# Command to display the current value of variable SYNTH_DRIVING_CELL to check whether it's the proper cell or not
echo $::env(SYNTH_DRIVING_CELL)
# Now that the design is prepped and ready, we can run synthesis using the following command
run_synthesis
```

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/timing/7.1.png)

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/timing/8.png)

Now that the synthesis phase is over, we use the following command to finish the floorplan:
```
init_floorplan
place_io
tap_decap_or
```
![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/timing/9.png)

After finishing the floorplan stage, we run placement
```
run_placement
```

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/timing/10.png)

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/timing/11.png)

Now we can see our slack problem is solved. 
Now, to verify if the standard cell we generated was incorporated into the design or not by magic
```
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &
```

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/timing/12.png)

**Timing analysis with ideal clocks using openSTA:**
Let's create an STA conf file (pre_sta.config) first in the directory ```/home/vsduser/Desktop/work/tools/openlane_working_dir/openlane```. In this file, we provide the fast and slow corner lib file, an old Verilog file, and an SDC file. After STA a new Verilog file is created. The file is shown below:

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/timing2/2.png)

```my_sdc``` in the directory ```sky130_cinv.lef```
```
/home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src
```

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/timing2/1.png)

now run the static timing analysis using the following command
```
sta pre_sta.config
```

![image](https://github.com/Dipon-Ctg/SoC-Design-and-Planning/blob/main/reference/image/Lab/timing2/3.png)

Slack does not equal what we obtained during the synthesis stage, as can be seen. Still, we move forward with the CTS and routing.
**CTS:**
TritonCTS is the EDA tool that generates CTS. This tool so far generates the CTS for typical corner of std cells. 







