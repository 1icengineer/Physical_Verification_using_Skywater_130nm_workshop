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

### PV_D1SK2_L4 - Creating Symbol And Exporting Schematic In Xschem

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

### PV_D1SK2_L5 - Importing Schematic To Layout And Inverter Layout Steps

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

## Day2 GDS read/write, extraction, DRC, LVS and XOR setup

These lectures cover in more depth what extraction, DRC, LVS are about, plus some nice anecdotes on magic vlsi. 
If you have done layout work in cadence you'll know most of the basics, only certain commands / approaches in magic might be new. 

Heading to the labs for Day2

### PV_D2SK2_L1 - GDS Read

Create a new workspace with mag subdirectory and copy the init file:  
>thorsten@icdesign-workshop:~/course/lab2$ cp /usr/share/pdk/sky130A/libs.tech/magic/sky130A.magicrc ./.magicrc
>thorsten@icdesign-workshop:~/course/lab2$ magic -d XR

in magic run
>cif listall istyle
>cif list istyle    -->to see the active style, which is sky130(vendor)

Now we load a gds cell lib from our pdk distribution:

>gds read /usr/share/pdk/sky130A/libs.ref/sky130_fd_sc_hd/gds/sky130_fd_sc_hd.gds

then get a list of all loaded cells via:
>cellname top
  
![image](https://user-images.githubusercontent.com/93275755/139493571-359d48a8-d15e-461c-8061-7b1226af58c6.png)

or use cell manager to browse cells loaded:

![image](https://user-images.githubusercontent.com/93275755/139493825-a865bef0-a556-47cb-81d0-76aac699d331.png)

![image](https://user-images.githubusercontent.com/93275755/139493843-1ca49dee-5c49-42c8-9685-f66fed0bb7c0.png)

Pick eg the and2_1:  

![image](https://user-images.githubusercontent.com/93275755/139494042-56e9dd33-19c3-46b9-aa19-a182af4af0a0.png)

Labels turned from yellow to blue by activating the correct  vendor style sky130(vendor).  
If a different unfitting style is chosen, all labels will turn yellow, ie they are text.

So, if we are running in magic  
>cif istyle sk130()
>gds read /usr/share/pdk/sky130A/libs.ref/sky130_fd_sc_hd/gds/sky130_fd_sc_hd.gds

then all labels turn yellow.  

![image](https://user-images.githubusercontent.com/93275755/139494893-0b05f978-8f1d-42db-8325-3556cf16d170.png)


while 

>cif istyle sk130(vendor)
>gds read /usr/share/pdk/sky130A/libs.ref/sky130_fd_sc_hd/gds/sky130_fd_sc_hd.gds

turns them blue again, indicating "real" pins:

![image](https://user-images.githubusercontent.com/93275755/139494962-31e6f3be-122c-4612-b0cd-de95ddc59e33.png)

During these switches between istyles the gds lib was re-read everytime after, overwriting existing cells from the lib in memory.  
this can be deactivated by means of:  
>gds noduplicates true

which will disallow magic to re-read a cell already in memory a second time, freezing cells in memory in their respective state. 

----

### PV_D2SK2_L2 - Ports

Now we will take a closer look at labels and ports:  

![image](https://user-images.githubusercontent.com/93275755/139496059-ab63401c-80e0-439a-917f-64aab07320b3.png)

We notice that all port information besides the name is not preserved in gds, eg port use, port class etc.  
LEF is needed for that.

But at least we can browse the list of ports in our gds cell:

![image](https://user-images.githubusercontent.com/93275755/139496752-95454526-8126-41c5-a8f9-54d589206c3a.png)

The order of pins matters for spice, while it does not for gds and magic. 

Check the spice of our lib and seek the and2_1 cell  
>vim /usr/share/pdk/sky130A/libs.ref/sky130_fd_sc_hd/spice/sky130_fd_sc_hd.spice 

![image](https://user-images.githubusercontent.com/93275755/139497601-49cb17a3-a935-48f8-843e-11c335a105bf.png)

All this information is rad in by magic from gds, lef, spice files combined and then processed to maintain proper port/pin order.

now we read in the lef file for our lib:  
>lef read /usr/share/pdk/sky130A/libs.ref/sky130_fd_sc_hd/lef/sky130_fd_sc_hd.lef

This lef information is annotated to our cell in case there is a match and we now have full information on our ports available:  

![image](https://user-images.githubusercontent.com/93275755/139498179-353cf244-5c8e-4ba7-9a45-00fd49a91079.png)

Spice libs cannot be read in directly to establish port order. But we have tcl script available to read this information:  
>readspice /usr/share/pdk/sky130A/libs.ref/sky130_fd_sc_hd/spice/sky130_fd_sc_hd.spice

which will cause the port order to be adjusted in magic for all fitting cells:  

![image](https://user-images.githubusercontent.com/93275755/139498747-fb8061ca-4b17-4817-b03b-47615420a449.png)

Now port order is established in our gds per the spice file and port purposes were read in from the LEF:  

![image](https://user-images.githubusercontent.com/93275755/139499426-5f9ac1a6-c164-44eb-9e4f-b2cca333230f.png)

----

#### PV_D2SK2_L3 - Abstract Views

This time we will take a closer look at cell abstracts from the LEF file.

In a new magic session we are reading in the lef alone, without gds or spice:  
>lef read /usr/share/pdk/sky130A/libs.ref/sky130_fd_sc_hd/lef/sky130_fd_sc_hd.lef

loading our and2_1 cell again we notice the LEF contains a very simplified view of the cell compared to the gds:  

![image](https://user-images.githubusercontent.com/93275755/139500275-3b3f47c9-61fc-4f2a-a686-18dd3442e5e4.png)

LEF contains routing data, labels, port meta-data but not all meta-data liek port sequence.  

![image](https://user-images.githubusercontent.com/93275755/139500545-2136ba09-3d65-47b3-9990-342f9fdd610e.png)

Port order needs to come from spice, as shown lab L2 above.

Creating a new cell, loading the abstract view and writing to gds is possible, but somewhat useless:  

![image](https://user-images.githubusercontent.com/93275755/139501454-0bc75727-60a3-4bb3-b9f1-a6db86eded90.png)

The resulting gds is sort of crippled as many layers are missing, but magic is smart enough to retain the missing information from its pdk reference libs and produce a correct-looking gds. However, port assignments and such are still incorrect (yellow, ie just labels)

----

### PV_D2SK2_L4 - Basic Extraction

now close and reopen a new magic window, then type:  
>load sky130_fd_sc_hd__and2_1

this will find the cell in the pdk reference libs and place it in current layout:  

![image](https://user-images.githubusercontent.com/93275755/139503472-d815c334-2a3b-48f4-a061-b6f06430b4ba.png)

now run in magic:  
>extract all
>ext2spice lvs
>ext2spice

here is the extracted netlist generated:  

![image](https://user-images.githubusercontent.com/93275755/139503715-b4a8a8aa-7542-475f-b392-04b0d86b7619.png)


>ext2spice cthres 0
>ext2spice

will produce a parasitic netlist:  

![image](https://user-images.githubusercontent.com/93275755/139503972-0a71dff9-d0d0-40c1-83e2-4397ef1309e4.png)

level of caps included can be controlled by setting a different threshold

----

Now we try these commands  

>ext2sim labels on
>ext2sim
>extresist tolerance 10
>extresist
>ext2spice lvs
>ext2spice cthres 0.01
>ext2spice extresist on
>ext2spice

we will get a complete spice netlist with rc extracted:  

![image](https://user-images.githubusercontent.com/93275755/139504694-054ceb32-11cb-440c-813e-ac767d701e63.png)


After extending our testbench from Lab1 and modifying subckt calls, we run a comparison of schematic, cap-extract and rc-extract in one simulation:  

----

### PV_D2SK2_L5 - Setup For DRC

Running DRC on the and2_1 gate cell yields the following DRC errors:

![image](https://user-images.githubusercontent.com/93275755/139703815-b56a4c01-81fc-4001-9f39-6f409363ad78.png)

Open the respective cell in magic layout editor via  
>load sky130_fd_sc_hd__and2_1

And now enter  
>drc style

![image](https://user-images.githubusercontent.com/93275755/139704140-747b120c-fc1a-4be4-bce4-864d41c39afb.png)

Partial drc runsets being used in magic at this point is the reason why magic does not flag drc errors, while the external script does.

Switch to full drc ruleset by  
> drc style drc(full)

Then re-issue the DRC run in magic with this full set of tests put the cursor on the cell content in layout window and type  
>:drc check  
The : routes this  command straight to the magic vlsi interpreter 

Notice the redmarks on all DRC erros now found:  
![image](https://user-images.githubusercontent.com/93275755/139704782-afd28260-10c6-4f39-9285-814784343ba3.png)


These can be examined by hover over, pressing S and then run >:drc why

These drc errors on standard cell lib cell can be avoided in this case by assing substrate tap cells.  
Load a new design with the and cell and then a subtap by running

>load test2
>getcell sky130_fd_sc_hd__and2_1
>getcell sky130_fd_sc_hd__vpwrvgnd_1

Now the DRC errors in our gate cell are gone, but new ones came in due to placement violations:  
![image](https://user-images.githubusercontent.com/93275755/139705476-cbb76a52-6def-43b6-8593-1139741cf1df.png)

We correct this by moving the tap cell to align with the gate cell using the arrow keys and we notice the drc error count dropping to zero:  
![image](https://user-images.githubusercontent.com/93275755/139705661-3c9c2cea-8ff4-4729-9e32-136c72bd2a68.png)
You can track the drc live updates to find the correct arrangement while moving the cell using the arrow keys.

----

Not that magic DRC operates fully hierachical, ie it keeps the list of intra-cell drc violations where in this case the subtaps are missing and it distinguishes those from higherlevel drc where the taps are in fact present.  
Verify this by selecting the and2_1 gate using "i" and then  descending with the ">" key.   
Inside the and2_1 cell, the above violations are present, while they are gone on the upper level layout.

----

### PV_D2SK2_L6 - Setup For LVS

Make a new directory for netgen to run LVS:  
> mkdir netgen
> cd netgen
> cp /usr/share/pdk/sky130A/libs.tech/netgen/sky130A_setup.tcl ./setup.tcl

now open the and2_1 cell again in magic after >cd ../mag and then run inside magic:  
>ext2spice lvs
>ext2spice

Now back in the netgen subdir run LVS by:  
>netgen -batch lvs "../mag/sky130_fd_sc_hd__and2_1.spice sky130_fd_sc_hd__and2_1" "/usr/share/pdk/sky130A/libs.ref/sky130_fd_sc_hd/spice/sky130_fd_sc_hd__and2_1  sky130_fd_sc_hd__and2_1"

So in a nutshell you run >netgen -batch lvs "<path.to.magic.spice> <name.of.top.cell>" "<path.to.schematic.spice> <name.of.top.cell>" and compare the two spice netlists created by magic and by xschem

In this case layout and schematic match, which is confirmed by he netgen output:  
![image](https://user-images.githubusercontent.com/93275755/139708148-9f8a486a-1610-466e-9938-0150a6247a9e.png)

----

### PV_D2SK2_L7 - Setup For XOR

We can also run XOR comparisons to find out modifications in a layout that might have left to DRC or LVS violations to get an output just howing the differences between a later version layout and an earlier/reference version:  
![image](https://user-images.githubusercontent.com/93275755/139709099-c1b86f24-2ce0-452e-8543-649307ae2087.png)

In this example some things got accidentally shifted vs the original resulting in stripline plots on the xor result layout.

Generate these by running in magic:  
>load sky130_fd_sc_hd__and2_1
>save changed.layout
>load changed.layout


Now erase something eg by dropping a box on the layout and enter >:erase li

Next flatten the layout and load the unchanged reference cell:  
>flatten -nolabels xor_test
>load sky130_fd_sc_hd__and2_1

And run the XOR test and load the results by:  
>xor -nolabels xor_test
>load xor_test

This last loaded file called >xor_test contains the differences on each layer checked and provides insight into what changed in layout.

--------------

## Day 3 - Front-end and back-end DRC

This content is dealing in very fine detail with how you setup and alter existing DRC decks for the following classes of issues.
* PV_D3SK2_L1 - Lab For Width Rule And Spacing Rule
* PV_D3SK2_L2 - Lab For Wide Spacing Rule And Notch Rule
* PV_D3SK2_L3 - Lab For Via Size, Multiple Vias, Via Overlap and Autogenerate Vias
* PV_D3SK2_L4 - Lab For Minumum Area Rule And Minimum Hole Rule
* PV_D3SK2_L5 - Lab For Wells And Deep N-Well
* PV_D3SK2_L6 - Lab For Derived Layers
* PV_D3SK2_L7 - Lab For Paramterized And PDK Devices
* PV_D3SK2_L8 - Lab For Angle Error And Overlap Rule
* PV_D3SK2_L9 - Lab For Unimplemented Rules
* PV_D3SK2_L10 - Latch-up And Antenna Rules
* PV_D3SK2_L11 - Lab For Density Rules

From my perspective this lecture part mostly relevant for thos who write DRC decks, which is usually done by foundry experts, or a modeling group inside a design-house or similar. 

For circuit designers and layout engineers I would see this as optional, as we usually work with fixed andset DRCrulesets which we are not supposed to change.  
If you  wanted to know how you could fix your layout to be DRC compliant, these labs provide insght.

To install the lab workfiles on your system clone start by cloning the following git repo and then start the exercises:
>git clone https://github.com/RTimothyEdwards/vsd_drc_lab.git
>cd vsd_drc_lab
>./run_magic

Then >load exercise_1 in magic.

In the example opening, place a box around the first item using LMB adn RMB and then run DRC on it:  
![image](https://user-images.githubusercontent.com/93275755/139712523-b975553d-60e9-4e57-9c2e-3eda7bcac98b.png)

Resulting in this error being listed:  
![image](https://user-images.githubusercontent.com/93275755/139712620-76828011-6945-4321-8d95-36f8490ccccb.png)

White dots indicate the missing area/width.

You can hover over any DRC violation highlighted in layout and press "?"

Use the grid and snap to grid for some fixing certain offgridn errors. 

Here in the example we need to widen the metal trace which can be done by:  
1.) using middle mouse button auto-paint:  
    draw box around eg metal to enlarged, then hover over metal and press MM. This will sample the currently used metal and paint the whole enlarged box with it.  
2.) hover over metal to be enlarged, then type 
    >: box width 0.14  
    and hit return and enter in layout
    >: paint m2
    

for shifting shapes around the following commands might be helpful:  
>:move e 0.14um
>:stetch n 0.14um
>:box 

use your respective north/south/east/west directions to optimize or fix shapes.

**Streteching** shapes (like the s key in virtuoso layout) can be accomplished using the numerical keypay arrows under key 8,6,2,4:
1) select the shape to be stretched such that the edge to be moved is fully contained in the white box  
2) press "a" for area select
3) now press nad hold shift while using eg the numpad 4 or 6 keys to move the edge east or west

![image](https://user-images.githubusercontent.com/93275755/139727809-f56674f1-2bb5-4fa5-bd66-fa3e3e635b94.png)
![image](https://user-images.githubusercontent.com/93275755/139727830-9c6b5d02-8681-42a3-9cb6-60067ce7c7bf.png)

alternatively to numpad keys use the command version:
>:strecth e 1.6um

----
Via Arrays:

>cif see MCON 
or (VIA1, VIA2 etc) highlights the contacts/vias drawn automatically in magic with the Skywater 130 pdk to populate the metal area with vias.

Use these command to highlight errors on vias and other structures
>feedback why
>feedback clear 

These commands grow the box to reshape a metal patch or selection of patches symmetrically:  
>: box grow e 0.3um
>: box grow w 0.3um  
then use middle mouse button to select and paint the box area with the metal to be grown.

![image](https://user-images.githubusercontent.com/93275755/139729648-fbedf73e-d470-42ae-a6c5-aa98bd250aa0.png)
![image](https://user-images.githubusercontent.com/93275755/139729617-432de0af-b12c-48fb-be95-d5b3c44f6998.png)

Use the wiring tool to auto-place vias by hitting "space" many times until it says "switching to wiring tool":  
Now move around the mouse button (shown by a hand with pointing finger), use the LMB (left mouse button) to start a new segment.  
Note:  
> shift + LMB will change to metal layer ABOVE the current layer,  
> shift + RMB will change to a layer BELOW the current routing layer.

![image](https://user-images.githubusercontent.com/93275755/139730302-410e2f34-d36d-4f92-931e-b5afbdbf4b1a.png)

Magic will automatically insert a suitable array of vias fitting in the overlap area between the lower and upper metal of the new metal transition.
Note that wiring created this way will always adhere to minimum metal width and coverage rules on all alyers.

----

**Holes in metal** are checked with drc style(full).  
To fix minimum metal hole errors, draw a box manually over the hole and measure its size with the "b" key.

Now grow a larger box and place over/around the hole, measure with b to verify it is large enough and enter:  
>: erase m1  
to enlarge the cutout on e.g. m1


----

**Wells and Taps**

NWELL must be contacted by N-tap contact, adhering to overlap and distance rules which can be fixed as the metal errors above
N-Tap in turn must be contacted by "li" (local interconnect):  
![image](https://user-images.githubusercontent.com/93275755/139734615-fd80b06c-fedd-40c9-8a11-084c6955bf89.png)


Substrate contacts must be filled with contact "p-sub p-contact" and "li":  
![image](https://user-images.githubusercontent.com/93275755/139734536-a07b171e-3fae-476c-ad1d-6deebf0c35c7.png)


Draw automatic DNWELL ring contact assemblies using the menu:  
![image](https://user-images.githubusercontent.com/93275755/139733831-6622ee2a-6f3f-4481-9af3-3875f5b54083.png)  
Draw the white box large enough and sufficiently far away from existing NWELL and DNWELL contacts.


----

Pcells / Parameterized devices

magic will flag pcells without higher level interconnect for minimum metal area violations. 
These will vanish once the device is properly connected.  
Use pop into devices using "s" and ">" to verify this behavior inside and outside cells



-----

**Latchup / Antenn checks**

we need to extract the design to eval the elecrical connectivity to long metal lines and then run antenna check:  

> extract do local
> extract all
> antenncheck

this just provides silent feedback, use >: feedback why to find out what is flagged in your current selection box

Run:  
>antennacheck debug
>antennacheck

to get a real debug report: 

![image](https://user-images.githubusercontent.com/93275755/139738584-7e683f84-7e54-4689-9648-39d1a3a40ca0.png)

When fixing an antenna error, by placing and connecting an antenna diode, be sure clear all feedack and rerun extract and antennacheck

>: feedback clear
>extract all
>antennacheck debug
>antennacheck

Another way to fix antenna violations is to via down to layers existing in the respective metal deposition step eg for M2, via down to M1 and connect antenna-forming  structures on M1 to avoid the error altogether:  

![image](https://user-images.githubusercontent.com/93275755/139739622-46f07bbc-1473-4f61-bc60-746f000c49bd.png)


----

**Density Violations**

Density checks are suitable and intended to run on larger areas and full chips only, not on small local cells.  
Slect toplevel cell using "i" then run in magic:  
>cif cover MET1  
>cif cover MET2  
>cif cover MET3  
etc

resulting in output such this reporting metal density:

![image](https://user-images.githubusercontent.com/93275755/139740122-b2fe05c9-7833-4229-9b8d-9720b01f01e9.png)

to check compliance with actual foundry density rules, run this external python script from the pdk tree:  
>/usr/share/pdk/sky130A/libs.tech/magic/check_density.py exercise_11.gds

NOte that excercise_11.gds needs to be created upfront with  
>gds write exercise_11

We are then getting reults like these reported:  

![image](https://user-images.githubusercontent.com/93275755/139741005-52d9845b-213d-4e27-b7ae-ba0ae65bf1aa.png)


In order to fix these violations automatically in Sky130, we need to run another python script from the PDK:  
>/usr/share/pdk/sky130A/libs.tech/magic/generate_fill.py exercise_11.gds  

This results in a new gds file being generated **after a while** called exercise_11_fill_patten.gds  
which contains the fill needed for the metals to be used in a DRC compliant way for the example above

![image](https://user-images.githubusercontent.com/93275755/139741521-ef326843-6d2e-44e9-a1c5-bd5798686e66.png)

To visualise only MET2 fill patterns issue the command >see m2fill   

This shows the limited smarts of the auto metal fill script sind it tries to fill all metal to the max, 
independent of the respective layer already being compliant upfront or not:  

![image](https://user-images.githubusercontent.com/93275755/139741827-69f9a03a-ca0e-4fdb-8c00-4d4f6130af91.png)


To merge the fill pattern with the orig layout do

> load excercise_11
> see *
> box values 0,0,0,0
> getcell exercise_11_fill_patten

This merges the fillpattern and cell layout as we can verify by showing only m2 and m2fill layers:  
> see m2,m2fill  

![image](https://user-images.githubusercontent.com/93275755/139742194-d3db07dc-a545-4619-b268-8831e1122dbd.png)


>cif cover m2_all

now results in 92% coverage. Over-coverage needs to be fixed by manually removing metal.

--------
























































