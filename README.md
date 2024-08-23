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
