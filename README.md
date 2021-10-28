# Physical_Verification_using_Skywater_130nm_workshop
Physical Verification using Skywater 130nm technology - VSD IAT workshop

![image](https://user-images.githubusercontent.com/93275755/139152828-c4addf35-8f3e-4f93-bb3a-0ccf4a310996.png)

This workshop was offered by https://www.vlsisystemdesign.com/vsd-iat/ and held 20211026-30
------------

## Course Curriculum
Day 1: Introduction to Sky130

    Introduction to Skywater PDK
    Opensource EDA Tools
    Understanding Skywater PDK
    Opensource Tools And Flows

Day 2: DRC/LVS Theory

    Understanding GDS Format
    Extraction Commands, Styles and Options In Magic
    Advanced Extraction Options In Magic
    GDS Reading Option In Magic
    GDS Writing, Input, Output Styles and Output Issues
    DRC Rules In Magic
    Extraction Rules And Errors In Magic
    LVS Setup For Netgen
    Verification By XOR

Day 3: Frontend & Backend DRC

    Introduction To Basic Silicon Manufacturing Process
    Backend Metal Layer Rules
    Local Interconnect Rules
    Front-End Rules, Transistors Implants, ID and Boundary Layers, Wells And Same Net Rules
    Deep N-Well And High Voltage Rules
    Device Rules
    Miscellaneous Rules Latch-up Antenna Stress Rules

Day 4: PNR and Physical Verification

    The OpenLANE Flow
    RTL2GDS For Demo Design
    Interactive OpenLANE Run
    Interactive OpenLANE Run - Final Steps
    Techniques To Avoid Common DRC Errors
    Techniques To Manually Fix Violations

Day 5: Running LVS

    Physical Verification Of Extracted Netlist
    How LVS Matching Works
    LVS Netllist Vs Simulation Netlists
    The Netgen Core Matching Algorithm
    Netgen Prematch Analysis, Hierarchical Checking And Flattening
    Pin Checking And Property Checking
    Series Parallel Combining
    Symmetry Breaking
    Interpreting Netgen Results

------------


- TOC
{:toc}

------------
## Day1 - Intro

### Intro on Skywater
The New Age of Open Source Silicon

Tim Edwards explains the Skywater 130nm PDK and Ecosystem, having evolved partly from the MOSIS legacy, only much more useful since Skywater is a real process running in a real fab. 

Skywater fab in Bloomington, Mn is  owned and run by SkyWater Technology publicly traded as SKYT.
Google and Skywater teamed up to provide a free and open source Process Design Kit (PDK), making it possible for the first time, to work off libraries and setups for open source tools build around a real manufacturable process, which can be used to fabricate real designs at SkyWater's facility.

In collaboration with efabless, this foundry also offers free silicon shuttles. Free as in zero dollars paid - best deal in town right now!
![image](https://user-images.githubusercontent.com/93275755/139150767-f6c73ec1-66cf-4cf1-aca0-5045eef7b9af.png)


----------

Key Skywater PDK repositories and components:
1. Documentation: https://skywater-pdk--136.org.readthedocs.build/
2. PDK Library and files: https://github.com/google/skywater-pdk
3. Community on slack: https://join.skywater.tools/

![image](https://user-images.githubusercontent.com/93275755/139152062-4eed8cc3-b385-485f-9e94-e72bfc768654.png)

--------------
### Open Source Tools

https://opencircuitdesign.com/open_pdks

Steps to installing the SKY130 PDK on your machine

1. clone the repo: "git clone https://github.com/RTimothyEdwards/open_pdks"
2. cd open_pdks
3. configure --enable-sky130-pdk
4. make
5. sudo make install

----

 OpenSource tools supported by open_pdks:
 Magic VLSI - Layout Editor
 Klayout - GDS Editor
 openlane - synthesis, p&r based on openroad
 xschem - schematic editor
 netgen - lvs
 ngspice - berkeley spice derivative
 iverilog, irsim, qflow, xcircuit
 
 ----
 Library setup and structure
![image](https://user-images.githubusercontent.com/93275755/139153338-a00b03b4-ffb3-4236-83ba-3379da418eaa.png)

 
-------------------

/usr/share/PDK - home of Skywater PDKs

project files and directories of your own projects:

![image](https://user-images.githubusercontent.com/93275755/139153865-c1deca0d-ba5a-4a49-a75c-b926a496aa18.png)

-------------

### Skywater PDK + Layers

SKY130 is a 180/130 hybrid with many devices having a 150nm feature size

*Process Cross Section*
higher level of  detail shown here than included in PDK. 
Some layers are derived from gds streamed out to foundry.
![image](https://user-images.githubusercontent.com/93275755/139154485-91db7f5f-cc31-46c8-bb72-4fd4bfeb2c72.png)

*Backend Layers*
contained in PDK cover contact, M1..M5:
![image](https://user-images.githubusercontent.com/93275755/139154643-97487a30-2aa1-48e2-a855-f0518a3c13f6.png)

*Front emd layers*
Note the diffusion and tap specialty here:
Diffusion (n/p) are heavily doped regions for eg source/drain region forming, 
while tap (n/p) are less heavily doped contacts to well regions 
![image](https://user-images.githubusercontent.com/93275755/139154786-7c78fd8c-2870-49f8-939c-824697335751.png)
![image](https://user-images.githubusercontent.com/93275755/139154933-9b52611f-f2d2-48bb-8385-76bf5d258b63.png)

*MIMCAPs*
single/double mimcap can be placed between M5-M4 and M4-M3. Stacking selectable.

*RDL Layer / Redistribution Layer* 
This is from a 3rd party wafer-level processing company offering a 4um Cu layer for WLCSP packages:
![image](https://user-images.githubusercontent.com/93275755/139155251-0ebec190-59b0-4bd9-81a8-8477e81b3230.png)

2nd RDL layer for UBM and bump placement:
![image](https://user-images.githubusercontent.com/93275755/139155548-c340a345-e3bb-42d6-9b46-656e12a798f6.png)

### Devices

*MOSFETs*
nMOS and pMOS devices with thin/thick oxide availble. 
ThinOx for VGSmax=2V, ThickOx for VGSmax=5V. 
Designrules call for 1.8/3.3V 

*BJTs*
* vertical npn with high gain made of n-well & deep-n-well, p-well, n-diffusion -> fully isolated devices. 
* pnp with low gain made of p-diffusion, n-well, substrate -> collector always tied to ground 

*polysilicon resistors*
* p+ with 1kOhms/square
* p- with 2kOhms/square

*diffusion resistors*
p and n diffusion resistor types available. Need well contacts around them for isolation. Large footprint

*implanted resistor*
pwell resistor

*Device widths*
discrete device contact widths are being used in the PDK: 0.35 / 0.69 / 1.41 / 2.85 / 5.73um width

*Device configurations*
MOSFETs come in predefined coonfigurations for number of source/gate/drain fingers.
No free choice of eg gate fingers allowed for simplification and model accuracy.

### Libraries
*sky130_fd_sc_hd* most commonly used lib 
130nm foundry-supplied (fd) standard cell (sc), high density (hd) variant

cell example: *sky130_fd_sc_hd__nor2_2* - final 2 designates fan-out

Almost no user-documentation available for standard cell library elements, only selected ones.
Symbol libs for manual placement from 3rd parties getting in pulled during install/setup

*sky130_fd_io* IO library - complex cells derived from cypress legacy designs
use caravel reference design to understand how to use and connect these.

*sky130_ef_io* overlay cells needed to configure IO cells properly for power-clamps etc

*sky130_fd_pr* 
Primitive devices following best-practices in layout and modeling, some obsolete like the MOM-cap device which should be replaced by MIM-caps if voltage complies. 

*SRAM and NVRAM* not included in PDK for now.
SRAM is convered by the *openRAM-project* might be merged with PDK in near future.
NVRAM is incomplete as only a SONOS device is available so far, aux circuits like charge pump and sense/erase are missing. Will not appear soon.

### Open Source Tools and Flows
The free alternative to the bid 3 in commercial EDA.

A simple manual design flow is shown here based on free opensource tools:

* xschem - schematic editor 
* magic - Layout Editor, one of the oldest around, but well-kept and updated
* netgen -LVS checking
* ngspice for simulation of schematic and extracted netlists

xschem and magic both generate spice netlists from schematic and extraction to run LVS checks. 
DRC checks are performed on-the-fly in magic.


## Lab 1

Note all labs run in the VSD sandbox, but all used tools can be set up on any recent linux system.
Howtos available on github for installing the tools and pdk on your own linux.

*Survival Skills needed in the opensource world:*
1. always pull updates and compile from the source yourself
2. run tools from command line, get used to programming/scripting in python etc

### check tool installations

1. run `magic` from the command line. 
this brings up 2 windows:
![image](https://user-images.githubusercontent.com/93275755/139239517-b8f4782f-77a1-4151-914c-d6e06d67d6d2.png)

2. try `netgen`:

![image](https://user-images.githubusercontent.com/93275755/139239685-f7bf57e4-1016-4779-8abd-ca908211ee27.png)

3. next `xschem`:

![image](https://user-images.githubusercontent.com/93275755/139239784-1fb73a0d-3141-4fb3-a634-c5d54f8e30a8.png)
Exit xschem by typing `exit` - not `quit`unlike in netgen and magic which are tcl based

4. then `ngspice`

![image](https://user-images.githubusercontent.com/93275755/139240157-864c79a1-1144-4998-92c3-708a34af4533.png)

5. try the no-console versions

* `magic -noconsole` and 
* `netgen -noconsole` 
will use the terminal as console without starting their own console windows

6.  run magic in script mode:
* `magic -dnull -noconsole`
* `magic -dnull -noconsole test.tcl`

![image](https://user-images.githubusercontent.com/93275755/139241696-06938f4a-65d9-4c7b-90af-3d782c7bb986.png)

7. the other tools running in batch mode

* netgen: `netgen -batch source test.tcl`
![image](https://user-images.githubusercontent.com/93275755/139242251-7449768d-f7a8-4861-9793-15a3559e8822.png)

* xschem  `xschem --tcl test.tcl -q`
throws an error on the test.tcl since it does not understand `quit` which is part of the tcl extension in magic

![image](https://user-images.githubusercontent.com/93275755/139242751-7155e991-7465-4236-9ce4-ab11a28a6d08.png)

* ngspice `ngspice -b`
not that ngspice does not understand tcl

---

### PV_D1SK2_L2 - Creating Sky130 Device Layout In Magic











