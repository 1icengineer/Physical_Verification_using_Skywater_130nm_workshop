# Physical_Verification_using_Skywater_130nm_workshop
Physical Verification using Skywater 130nm technology - VSD IAT workshop

![image](https://user-images.githubusercontent.com/93275755/139152828-c4addf35-8f3e-4f93-bb3a-0ccf4a310996.png)

This workshop was offered by https://www.vlsisystemdesign.com/vsd-iat/ and held 20211026-30
------------

## Course Curriculum
**Day 1: Introduction to Sky130**

    Introduction to Skywater PDK
    Opensource EDA Tools
    Understanding Skywater PDK
    Opensource Tools And Flows

**Day 2: DRC/LVS Theory**

    Understanding GDS Format
    Extraction Commands, Styles and Options In Magic
    Advanced Extraction Options In Magic
    GDS Reading Option In Magic
    GDS Writing, Input, Output Styles and Output Issues
    DRC Rules In Magic
    Extraction Rules And Errors In Magic
    LVS Setup For Netgen
    Verification By XOR

**Day 3: Frontend & Backend DRC**

    Introduction To Basic Silicon Manufacturing Process
    Backend Metal Layer Rules
    Local Interconnect Rules
    Front-End Rules, Transistors Implants, ID and Boundary Layers, Wells And Same Net Rules
    Deep N-Well And High Voltage Rules
    Device Rules
    Miscellaneous Rules Latch-up Antenna Stress Rules

**Day 4: PNR and Physical Verification**

    The OpenLANE Flow
    RTL2GDS For Demo Design
    Interactive OpenLANE Run
    Interactive OpenLANE Run - Final Steps
    Techniques To Avoid Common DRC Errors
    Techniques To Manually Fix Violations

**Day 5: Running LVS**

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

![image](https://user-images.githubusercontent.com/93275755/139271371-32fe3285-9f40-44ed-a3f3-755e9f6a182c.png)


----------

### Key Skywater PDK repositories and components:
1. Documentation: https://skywater-pdk--136.org.readthedocs.build/
2. PDK Library and files: https://github.com/google/skywater-pdk
3. Community on slack: https://join.skywater.tools/


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

**OpenSource tools supported by open_pdks:**
* Magic VLSI - Layout Editor
* Klayout - GDS Editor
* openlane - synthesis, p&r based on openroad
* xschem - schematic editor
* netgen - lvs
* ngspice - berkeley spice derivative
* iverilog, irsim, qflow, xcircuit
 
 ----
**Library setup and structure**
 
*Digital Standard Cells*
 
![image](https://user-images.githubusercontent.com/93275755/139273576-bcffc3bb-4ea8-4241-a8fe-5dcddd216b16.png)

*Primitive Devices / Analog*

![image](https://user-images.githubusercontent.com/93275755/139273803-3ba695de-39d9-4cfb-b357-503b84f81fad.png)

*I/O Cells*

![image](https://user-images.githubusercontent.com/93275755/139273917-9c3ebcd6-5f7c-4fc0-a46e-fc0ac3223e0e.png)

*3rd Party Cells*

![image](https://user-images.githubusercontent.com/93275755/139274200-ea55410a-d26d-467d-a5a5-fd4393545e41.png)


 
-------------------

**/usr/share/PDK - home of Skywater PDKs**

project files and directories of your own projects:

![image](https://user-images.githubusercontent.com/93275755/139274616-963b9a28-4eee-4830-9f59-d00de6a8b1fb.png)

Creaate your own subdirectories for the different design parts or views.
Link back to the init-files like 
`.magicrc` pointing to `sky130A.magicrc` 
Alternatively, create local copies to change default settings while leaving the PDK files unchanged.

-------------

### Skywater PDK + Layers

SKY130 is a 180/130 hybrid with many devices having a 150nm feature size

**Process Cross Section**

higher level of  detail shown here than included in PDK. 
Some layers are derived from gds streamed out to foundry.
![image](https://user-images.githubusercontent.com/93275755/139154485-91db7f5f-cc31-46c8-bb72-4fd4bfeb2c72.png)

**Backend Layers**

contained in PDK cover contact, M1..M5:
![image](https://user-images.githubusercontent.com/93275755/139154643-97487a30-2aa1-48e2-a855-f0518a3c13f6.png)

**Front emd layers**

Note the diffusion and tap specialty here:
Diffusion (n/p) are heavily doped regions for eg source/drain region forming, 
while tap (n/p) are less heavily doped contacts to well regions 

![image](https://user-images.githubusercontent.com/93275755/139154786-7c78fd8c-2870-49f8-939c-824697335751.png)
![image](https://user-images.githubusercontent.com/93275755/139154933-9b52611f-f2d2-48bb-8385-76bf5d258b63.png)

**MIMCAPs**

single/double mimcap can be placed between M5-M4 and M4-M3. Stacking selectable.

**RDL Layer / Redistribution Layer**

This is from a 3rd party wafer-level processing company offering a 4um Cu layer for WLCSP packages:

![image](https://user-images.githubusercontent.com/93275755/139155251-0ebec190-59b0-4bd9-81a8-8477e81b3230.png)

**2nd RDL layer for UBM and bump placement:**

![image](https://user-images.githubusercontent.com/93275755/139277365-1fa97ef6-c067-48cb-a090-e400949ad4ed.png)


### Devices

**MOSFETs**

nMOS and pMOS devices with thin/thick oxide availble. 
ThinOx for VGSmax=2V, ThickOx for VGSmax=5V. 
Designrules call for 1.8/3.3V 

**BJTs**

* vertical npn with high gain made of n-well & deep-n-well, p-well, n-diffusion -> fully isolated devices. 
* pnp with low gain made of p-diffusion, n-well, substrate -> collector always tied to ground 

**polysilicon resistors**

* p+ with 1kOhms/square
* p- with 2kOhms/square

**diffusion resistors**

p and n diffusion resistor types available. Need well contacts around them for isolation. Large footprint

**implanted resistor**

pwell resistor

**Device widths**

discrete device contact widths are being used in the PDK: 0.35 / 0.69 / 1.41 / 2.85 / 5.73um width

**Device configurations**

MOSFETs come in predefined coonfigurations for number of source/gate/drain fingers.
No free choice of eg gate fingers allowed for simplification and model accuracy.

### Libraries

**sky130_fd_sc_hd**  
most commonly used lib 
130nm foundry-supplied (fd) standard cell (sc), high density (hd) variant

cell example: **sky130_fd_sc_hd__nor2_2** - final digit "2" designates fan-out level

*Note*  
Almost no user-documentation available for standard cell library elements, only selected ones.
Symbol libs for manual placement from 3rd parties getting in pulled during install/setup

**sky130_fd_io**   
IO library - complex cells derived from cypress legacy designs
use caravel reference design to understand how to use and connect these.

**sky130_ef_io**  
overlay cells needed to configure IO cells properly for power-clamps etc

**sky130_fd_pr**  
Primitive devices following best-practices in layout and modeling, some obsolete like the MOM-cap device which should be replaced by MIM-caps if voltage complies. 

**SRAM and NVRAM**  
not included in PDK for now.
SRAM is convered by the *openRAM-project* might be merged with PDK in near future.
NVRAM is incomplete as only a SONOS device is available so far, aux circuits like charge pump and sense/erase are missing. Will not appear soon.

----

### Open Source Tools and Flows
The free alternative to the bid 3 in commercial EDA.

A simple manual design flow is shown here based on free opensource tools:

* xschem - schematic editor 
* magic - Layout Editor, one of the oldest around, but well-kept and updated
* netgen -LVS checking
* ngspice for simulation of schematic and extracted netlists

xschem and magic both generate spice netlists from schematic and extraction to run LVS checks. 
DRC checks are performed on-the-fly in magic.

----------------

## Lab 1

Note all labs run in the VSD sandbox, but all used tools can be set up on any recent linux system.
Howtos available on github for installing the tools and pdk on your own linux.

### Survival Skills needed in the opensource world
>1. always pull updates and compile from the source yourself. repeat frequently.
>2. run tools from command line. get used to some level of programming/scripting in python etc

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

* `netgen -batch source test.tcl`  
![image](https://user-images.githubusercontent.com/93275755/139242251-7449768d-f7a8-4861-9793-15a3559e8822.png)

* `xschem --tcl test.tcl -q`  
throws an error on the test.tcl since it does not understand `quit` which is part of the tcl extension in magic  

![image](https://user-images.githubusercontent.com/93275755/139242751-7155e991-7465-4236-9ce4-ab11a28a6d08.png)

* `ngspice -b`  
note that ngspice does not understand tcl  

---

### Lab: PV_D1SK2_L2 - Creating Sky130 Device Layout In Magic

First create a local project directory structure with links to the pdk init files for the apps needed:

<pre><font color="#8AE234"><b>thorsten@icdesign-workshop</b></font>:<font color="#729FCF"><b>~/course</b></font>$ mkdir inverter
<font color="#8AE234"><b>thorsten@icdesign-workshop</b></font>:<font color="#729FCF"><b>~/course</b></font>$ cd inverter/
<font color="#8AE234"><b>thorsten@icdesign-workshop</b></font>:<font color="#729FCF"><b>~/course/inverter</b></font>$ mkdir xschem
<font color="#8AE234"><b>thorsten@icdesign-workshop</b></font>:<font color="#729FCF"><b>~/course/inverter</b></font>$ mkdir mag
<font color="#8AE234"><b>thorsten@icdesign-workshop</b></font>:<font color="#729FCF"><b>~/course/inverter</b></font>$ mkdir netgen
<font color="#8AE234"><b>thorsten@icdesign-workshop</b></font>:<font color="#729FCF"><b>~/course/inverter</b></font>$ cd xschem/
<font color="#8AE234"><b>thorsten@icdesign-workshop</b></font>:<font color="#729FCF"><b>~/course/inverter/xschem</b></font>$ ln -s /usr/share/pdk/sky130A/libs.tech/xschem/xschemrc 
<font color="#8AE234"><b>thorsten@icdesign-workshop</b></font>:<font color="#729FCF"><b>~/course/inverter/xschem</b></font>$ ln -s /usr/share/pdk/sky130A/libs.tech/ngspice/spinit .spiceinit
<font color="#8AE234"><b>thorsten@icdesign-workshop</b></font>:<font color="#729FCF"><b>~/course/inverter/xschem</b></font>$ cd ../mag/
<font color="#8AE234"><b>thorsten@icdesign-workshop</b></font>:<font color="#729FCF"><b>~/course/inverter/mag</b></font>$ ln -s /usr/share/pdk/sky130A/libs.tech/magic/sky130A.magicrc .magicrc
<font color="#8AE234"><b>thorsten@icdesign-workshop</b></font>:<font color="#729FCF"><b>~/course/inverter/mag</b></font>$ cd ../netgen/
<font color="#8AE234"><b>thorsten@icdesign-workshop</b></font>:<font color="#729FCF"><b>~/course/inverter/netgen</b></font>$ ln -s /usr/share/pdk/sky130A/libs.tech/netgen/sky130A_setup.tcl setup.tcl
</pre>


![image](https://user-images.githubusercontent.com/93275755/139288164-507550ca-d3eb-45ed-b4f7-7208f9e2b3c2.png)

---

Switch to the xschem directory and launch xschem to verify it loads pdk devices:  

![image](https://user-images.githubusercontent.com/93275755/139289383-f3f413b4-f8ef-498e-8b82-bc0542ce1188.png)

This is one of the example designs / testbenches from the pdk. Select first via LMB click and then hit "e" to enter and crtrl-e to return from here:    

![image](https://user-images.githubusercontent.com/93275755/139292023-8d94046a-fbd4-44c4-82ee-3c37cf18cac8.png)


BTW: a number of established hotkeys from virtuoso are working here as well... Q etc ;-)


Now checking the `magic` setup and pdk plugins:  

![image](https://user-images.githubusercontent.com/93275755/139297992-c67888ec-6fd3-4bc6-805f-8ff9b1097968.png)

Note that the pdk context is now loaded and process-related info like layers are now shown and selectable.

For enhanced graphics, try `magic -d XR` for cairo-based graphics or`magic -d OGL` for openGL

----

**Basic editing in magic**

You should see a white box and the crosshaired cursor.   
Shift+Z to zoom out, LMB click to relocate the box and RMB click to resize.  
Hover your cursor over one of the colour tabs on the right (metals etc) and either press your middle mouse button or P to "paint"  
Place the box over a painted structure, hover over the respective colour tab (layer) and press E to erase the area within the white box with the corresponding colour.  
The box concept allows for fairly complex shapes to be drawn and assembled:

![image](https://user-images.githubusercontent.com/93275755/139315995-6b424cb8-4e14-4755-9089-15d509753214.png)

![image](https://user-images.githubusercontent.com/93275755/139316072-eb37dda9-3146-4532-a163-d995f0053a7d.png)

More information on magic editing at http://opencircuitdesign.com/magic/tutorials/tut1.html



**Placing a device in magic**

click on Devices1 and place a MOSFET with these sample properties:

![image](https://user-images.githubusercontent.com/93275755/139312518-a9a791f7-cc83-4f4c-b1cd-8b2366f0b156.png)

----

### Lab: PV_D1SK2_L3 - Creating Simple Schematic In Xschem

Go back to ../xschem and run `xschem` 
Then select File - New Schematic and press the "insert" key on your keyboard:  

![image](https://user-images.githubusercontent.com/93275755/139317657-5c6da789-aceb-46c5-85a4-059d2973687c.png)

Place nmos, pmos and pins onto schematic, using insert key and select the following libraries
1) for nmos pmos:  
![image](https://user-images.githubusercontent.com/93275755/139323106-2a1eac8d-f30b-4ced-bff0-a53e4e0464d2.png)
![image](https://user-images.githubusercontent.com/93275755/139323157-ba7b8f2d-333a-47c3-ae81-6033b3022f32.png)


2) for the pins (ipin, opin, iopin):  
![image](https://user-images.githubusercontent.com/93275755/139323021-650b8c8e-c53b-4cba-8a27-56aa15a4d85b.png)


The place real labels on the pins by selecting each one and then press Q for properties:

![image](https://user-images.githubusercontent.com/93275755/139322581-798618e0-a980-4d79-9b72-aaadb7425367.png)

In a similar fashion, select and bring up the props via Q for the mosfets, adjusting them to their final configs:

nmos properties after modification:  
![image](https://user-images.githubusercontent.com/93275755/139337702-5d3f9e2a-60cf-4ad6-8c67-957530ab1950.png)

pmos:  
L=0-18u / W=3 / nf=3 /bulk=vdd  
![image](https://user-images.githubusercontent.com/93275755/139338045-37f520bd-cff3-4525-9412-74aaa35c06d6.png)

## PV_D1SK2_L4 - Creating Symbol And Exporting Schematic In Xschem

Next we need is to create a symbol from our schematic - go to `Symbol - Make Symbol from Schematic`

![image](https://user-images.githubusercontent.com/93275755/139338881-5bdc2f91-8311-488c-b2a4-5d45f6b7f801.png)

Then we need to create a testbench schematic. Open a new empty sheet in xschem:  

Use the insert key and select the inverter symbol we just created:  
![image](https://user-images.githubusercontent.com/93275755/139339679-0677b9a7-e8a4-448d-b60c-c6eb0fd94892.png)

Then, place voltage sources, gnd and opin symbols for probes. 
Parameterize voltage sources suitable for tran simulation:  

![image](https://user-images.githubusercontent.com/93275755/139394227-c82912ee-fe01-4d96-9418-e026f5249690.png)

Now we need to enter simulation statements line `.tran` and similar to the schematic.
This is done by adding adding a symbol `code_shown.sym`

Parameterize the code_shown symbols to carry the following statements:
> name=s1 only_toplevel=false value=".lib /usr/share/pdk/sky130A/libs.tech/ngspice/sky130.lib.spice tt"  
and  
> name=s2 only_toplevel=false value=".control  
>tran 1n 1u  
>plot V(vin) V(vout)
>.endc"

Use the respective labels you have placed earlier on.

![image](https://user-images.githubusercontent.com/93275755/139397086-0d6b0b99-8836-4632-bf7d-58e9d1745f12.png)

Then save the file under a suitable name and run the simulation specified.

---

Now click the button `Netlist` and then `Simulation` to launch the spice simulation:

![image](https://user-images.githubusercontent.com/93275755/139399823-bc7f8604-d761-4593-a322-6552d459f553.png)

Since the first simulation result showed a kind of floating output, I checked the netlist for debugging:  

![image](https://user-images.githubusercontent.com/93275755/139403625-b7a70903-5af6-4d50-b458-5b70cc4db409.png)

Note that the supply connections on the inverter schematic are not properly recognized when wired in north/south direction:  

![image](https://user-images.githubusercontent.com/93275755/139403833-a3d0f47c-2700-40fd-ae91-8021b2208f94.png)

So, in order for these to be correctly netlisted we need to draw them this way:

![image](https://user-images.githubusercontent.com/93275755/139404091-ba18597c-d2f9-4edc-b654-1ded118f057e.png)

And subsequently the netlist shows the correct supplies on our subcircuit:

![image](https://user-images.githubusercontent.com/93275755/139405393-3fa16407-b59b-4873-b086-cabd12b06a7d.png)

With that, we obtain the expected results from our simulation:

![image](https://user-images.githubusercontent.com/93275755/139406281-929542ce-6e19-4a33-bd85-1b39cfb2f4af.png)


Now that we have verified proper circuit functionality, we can proceed to netlist export for layout in magic.  
For that, check the option `LVS netlist: toplevel is a .subckt` under `Simulation`:

![image](https://user-images.githubusercontent.com/93275755/139407696-e9bd29e4-bf36-4a8f-b1e6-42dc23488bd5.png)

With that, we can generate the subcircuit netlist to read into magic next. Here is the subckt netlist being generated:

![image](https://user-images.githubusercontent.com/93275755/139411098-38b6c6b5-4067-4b10-8435-80b16bef8b25.png)


----

## PV_D1SK2_L5 - Importing Schematic To Layout And Inverter Layout Steps

Now we head back to magic for importing the netlist and editing the layout:  
> cd ../mag
> magic -d XR

Then import the inverter spice netlist via `File - Import SPICE`  

![image](https://user-images.githubusercontent.com/93275755/139415172-35d431c4-6a2b-4e92-8a47-4f79b8b2b10d.png)

resulting in this inital setting:  

![image](https://user-images.githubusercontent.com/93275755/139413705-caf4e581-8b36-45f8-82f0-257246ed8408.png)

Rearrange this layout with key "i", "s", "m" (select instance, shape, move) and go to pmos properties:

![image](https://user-images.githubusercontent.com/93275755/139423419-2426f5f5-882d-4dbc-92df-a0bca32ccbfa.png)

Set Top Guardring coverage to 100% for metal on top of the ring, Source via coverage to +40, Drain coverage to -40:

![image](https://user-images.githubusercontent.com/93275755/139423884-94f539d0-7484-4139-b90f-ac79508dbc4c.png)


Next we need to establish connectivity all on metal1 between the devices and pins/pads in this layout, so we arrive at a complete layout:

![image](https://user-images.githubusercontent.com/93275755/139454532-4f6d274d-19d6-4960-b3f3-32cfaf1d0d86.png)


## PV_D1SK2_L6 - Final DRC/LVS Checks And Post Layout Simulations

Now we can extract our layout using these commands in the magic command window:
>extract do local
>extract all

![image](https://user-images.githubusercontent.com/93275755/139454154-b4d60c6c-2dff-4f25-b35b-29e69aded988.png)

This generates an extracted dataset in magic which needs to be converted to a spice netlist for postlayout simulation:
>ext2spice lvs
>ext2spice

Ths results in an extracted spice netlist containing all extracted layout devices:

![image](https://user-images.githubusercontent.com/93275755/139455266-bc3ebefe-32cc-4938-8f18-f08836851eaa.png)

Next we will run LVS to ensure our layout is correct, by comparing the extracted spice netlist from magic with the one for the schematic from  xschem:

in the terminal we enter:
> cd ../netgen
> netgen -batch lvs "../mag/inverter.spice inverter" "../xschem/inverter.spice inverter" 

This runs netgen on the two netlists supplied, referencing the inverter subcircuits therein.  
Result:  
![image](https://user-images.githubusercontent.com/93275755/139457150-539f757b-240d-4600-90f5-009fb7b2a36e.png)
![image](https://user-images.githubusercontent.com/93275755/139457200-f4bd9d24-3498-4bdf-8814-b3f1046ffae5.png)

So these two netlists match, indicating our layout is correct.

Next we will extract layout parasitcs to be included in a post-layout simulation:

go back magic and open the inverter layout, then enter the follwoing commands for magic
>extract do local
>extract all
>ext2spice lvs
>ext2spice cthres 0
>ext2spice

With tha we obtain a netlist with extracted caps from our layout included:

![image](https://user-images.githubusercontent.com/93275755/139459240-b18007ba-830d-4e61-95aa-a31d8229fc56.png)

We can now build a testbench for this circuit by reusing the one created above for the schematic version:  
In the mag subdir copy over the testbench netlist from the xschem dir:
>cp ../xschem/testbench_inverter.spice .
>vim testbench_inverter.spice

In the testbench we now need to adjust the call of our extracted subcircuit, as the pins and their sequence might have changed.  
Extraction in magic is basically showing all pins in a different order.  

We do this by considering the extracted spice netlist subckt definition
![image](https://user-images.githubusercontent.com/93275755/139462190-edce8ceb-4454-4686-adcf-49b88d9ff8b3.png)

and entering that one to our testbench_inverter.spice subckt call:
![image](https://user-images.githubusercontent.com/93275755/139462473-aa46909b-92d7-449b-a025-37e27ec3d3ac.png)

Plus we added an include statement to ensure the extracted netlist from our local mag subdir is used:
>.include ./inverter.spice

Then we save our testbench spice netlist file run spice on it:
>ngspice testbench_inverter.spice

Result of that run:
![image](https://user-images.githubusercontent.com/93275755/139462875-e71620ef-551e-4d73-b8dc-cd6d46e3a3df.png)

Just for fun, lets run a combined pre/post-layout sim as well by modifying the testbench to call both extracted and schematic versions of the inverter:
![image](https://user-images.githubusercontent.com/93275755/139463846-e0834280-bfd5-430d-81c8-a455886d89af.png)

In this netlist, two calls ae being made:
1) to interver.schem, the schematic version, generating V(out.schematic)
2) to the local inverter from ./inverter.spice, generating V(out.extracted)

Note that both calls have different pin sequences as discussed above.  
Otherwise, they are functionally equivalent, just with/without parasitics.  

After ngspice finishes, we get this output:

![image](https://user-images.githubusercontent.com/93275755/139464980-4b6dd3d5-3818-4b5e-952d-1d2ebd9bda75.png)

There are only very small layout parisitics in this circuit, resulting in very little difference between the blue output (schematic) and the yellow (extracted).

------------------------

## Day2








