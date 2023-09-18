# physical-design-exploration
In this repo I explore opensource EDA tools and PD of ASIC design flow.

# Advanced Physical Design using OpenLANE/Sky130 

## IC Package 

<img width="600" height="400" alt="image" src="https://github.com/yagnavivek/PES_OpenLane_PD/assets/93475824/96055996-9ea4-4f61-b9fb-295efe5b1bbb">

The most critical component of the IC is the ```chip``` because it contains the actual functionality of the device and IO pads helps in exchange of signals with the chip.

## CHIP

![image](https://github.com/yagnavivek/PES_OpenLane_PD/assets/93475824/2d811fbe-7f4d-4dae-8bd5-e1d26c7df813)

A chip usually consists of different components such as macros, IO Pads and pins, Foundary IPs etc.

1. **Macros:** Macros in VLSI design are predefined and reusable blocks of digital circuitry, often standard cells, used to simplify the design process by providing building blocks for complex integrated circuits.

2. **Foundry IPs (Intellectual Property):** Foundry IPs are pre-designed and verified circuit components, such as analog blocks, memory cores, or standard interfaces, licensed from semiconductor foundries for integration into custom chip designs.

3. **IO Pads and IO Pins:** IO pads are the physical interfaces between a chip and the external world, while IO pins are the electrical connections from these pads to the internal circuitry, facilitating input and output communication in integrated circuits.

## Application Specific Integrated Circuit (ASIC)

To develop an ASIC, We mainly require 3 components
- RTL IPs (github.com, opencores.org, librecores.org)
- PDK data (Qflow, OpenRoad, OpenLane)
- EDA Tools [google+skywater](github.com/google/skywater-pdk)

1. **```RTL```** : It is a level of abstraction in digital design and electronics where a system's behavior is described in terms of registers, logic gates, and the flow of data between them.
2. **```PDK```** : Process Design Kit is a collection of files, libraries, and documentation provided by semiconductor foundries to assist designers in creating integrated circuits (ICs) that are compatible with a specific manufacturing process. PDKs include information on design rules, device models, and technology files required for designing and simulating ICs.
3. **```EDA```** : Electronic Design Automation tools are software tools used for designing and testing electronic systems, including ICs and PCBs.

## RTL2GDSII FLow (simplified)

- ```synthesis```
- ```Floorplanning```
- ```Powerplanning```
- ```Placement```
- ```Clock Tree Synthesis```
- ```Routing```
- ```Signoff```

EDA Tools go through all these steps mentioned to obtain the required Layout file that is required for tapeout and The **GOAL** is to produce a clean GDSII with no human intervention which can be used to harden macros and chips (clean : No LVS, DRC, Timing violatons).

## OpenLane

![image](https://github.com/yagnavivek/PES_OpenLane_PD/assets/93475824/68749aa4-dd1a-401e-b7a3-0c74df581e8e)

## OpenLANE Design Stages

OpenLANE flow consists of several stages. By default all flow steps are run in sequence. Each stage may consist of multiple sub-stages. OpenLANE can also be run interactively as shown [here][25].

1. *Synthesis*
    1. `yosys` - Performs RTL synthesis
    2. `abc` - Performs technology mapping
    3. `OpenSTA` - Pefroms static timing analysis on the resulting netlist to generate timing reports
2. *Floorplan and PDN*
    1. `init_fp` - Defines the core area for the macro as well as the rows (used for placement) and the tracks (used for routing)
    2. `ioplacer` - Places the macro input and output ports
    3. `pdn` - Generates the power distribution network
    4. `tapcell` - Inserts welltap and decap cells in the floorplan
3. *Placement*
    1. `RePLace` - Performs global placement
    2. `Resizer` - Performs optional optimizations on the design
    3. `OpenPhySyn` - Performs timing optimizations on the design
    4. `OpenDP` - Perfroms detailed placement to legalize the globally placed components
4. *CTS*
    1. `TritonCTS` - Synthesizes the clock distribution network (the clock tree)
5. *Routing*
    1. `FastRoute` - Performs global routing to generate a guide file for the detailed router
    2. `CU-GR` - Another option for performing global routing.
    3. `TritonRoute` - Performs detailed routing
    4. `SPEF-Extractor` - Performs SPEF extraction
6. *GDSII Generation*
    1. `Magic` - Streams out the final GDSII layout file from the routed def
    2. `Klayout` - Streams out the final GDSII layout file from the routed def as a back-up
7. *Checks*
    1. `Magic` - Performs DRC Checks & Antenna Checks
    2. `Klayout` - Performs DRC Checks
    3. `Netgen` - Performs LVS Checks
    4. `CVC` - Performs Circuit Validity Checks

OpenLane can be operated at 2 different modes ie., Automated flow and Interactive mode.

## To enter the automated flow, use these commands
```
cd OpenLane
make mount
./ flow.tcl -design openlane/<DESIGN_NAME>  -tag <TAG>
```

## To enter the Interactive mode, use these commands 
```
cd OpenLane
make mount
./flow.tcl -interactive 
prep -design <path_to_your_design_folder> -tag <tag> -overwrite //overwrite is optional
```

**Interactive mode** offers us to learn all the steps present in automated flow step by step.
The steps are as follows : 

```
run_synthesis
run_floorplan
run_placement
run_cts
run_routing
write_powered_verilog followed by set_netlist $::env(lvs_result_file_tag).powered.v
run_magic
run_magic_spice_export
run_magic_drc
run_lvs
run_antenna_check
```

# COURSE
## DAY 1 : Inception of opensource-EDA, Opennlane and Skywater130
## Skywater-130 PDK

![A_DAY1_PDKS](https://github.com/yagnavivek/PES_OpenLane_PD/assets/93475824/64ca3a21-4a81-4094-ad18-0079d698d645)

The Skywater PDK files we are working with are described under $PDK_ROOT
1. Skywater-pdk – Contains all the foundry provided PDK related files
2. Open_pdks – Contains scripts that are used to bridge the gap between closed-source and open-source PDK to EDA tool compatibility
3. Sky130A – The open-source compatible PDK files

## Invoking OpenLane

![a_day1_invoke](https://github.com/yagnavivek/PES_OpenLane_PD/assets/93475824/77ca741e-1ec1-4322-a67e-dd6bbcd8a1e6)

flow.tcl is the file that contains the script to run the designs

## Importing package

Different software dependencies are needed to run OpenLANE. To import these into the OpenLANE tool we need to run: ```package require openlane 0.9```

![a_day1_packagge_req](https://github.com/yagnavivek/PES_OpenLane_PD/assets/93475824/0ee50a40-0d0a-4bcd-bcf4-f7a3fc8204e9)

## Designs presnt in openalne and Heirarchy in a Design

![a_day1_designhier](https://github.com/yagnavivek/PES_OpenLane_PD/assets/93475824/306d0c04-e1f4-4936-825a-b9480defde52)

- ```Src folder``` - Contains verilog files and sdc constraint files
- ```Config.tcl files``` - Design specific configuration switches used by OpenLANE

## Config file example content

![a_day1_configtcl](https://github.com/yagnavivek/PES_OpenLane_PD/assets/93475824/fcbdac6f-704e-4bdd-a051-791525392433)

## Prepare the design for the flow 

```prep -design <design_name> -tag <tag>```

![a_day1_prep_design](https://github.com/yagnavivek/PES_OpenLane_PD/assets/93475824/4517d1fe-69d8-4da1-96e3-40debdc548af)

Once the design prep stage is done, it creates a runs directory where all the results will be stored

![a_day1_runs](https://github.com/yagnavivek/PES_OpenLane_PD/assets/93475824/0b7230f2-589a-4ff6-a9e3-0764235c8fce)

## Synthesis

```run_synthesis```

![a_day1_run_synthesis](https://github.com/yagnavivek/PES_OpenLane_PD/assets/93475824/7f2375b4-f15b-4d47-bb2f-44493e586d9c)

****The main task to do at the beginning stage is to find the flop ration ie., (No. of D flip flops / Total number of cells)****

![a_day1_flopratio](https://github.com/yagnavivek/PES_OpenLane_PD/assets/93475824/20e5fb03-2184-493f-892b-be3fdf5df92b)

[BACK TO TOP](https://github.com/yagnavivek/PES_OpenLane_PD#to-enter-the-automated-flow-use-these-commands)
