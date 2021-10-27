# Physical_Verification_using_Skywater_130nm_workshop
Physical Verification using Skywater 130nm technology - VSD IAT workshop

![image](https://user-images.githubusercontent.com/93275755/139152828-c4addf35-8f3e-4f93-bb3a-0ccf4a310996.png)

This workshop was offered by https://www.vlsisystemdesign.com/vsd-iat/ and held 20211026-31
------------
#TOC

------------
# Day1 - Intro

## Intro on Skywater
The New Age of Open Source Silicon

Tim Edwards explains the Skywater 130nm PDK and Ecosystem, having evolved partly from the MOSIS legacy, only much more useful. 

Skywater run by Google also offers free silicon shuttles. Free as in zero dollars paid - best deal in town right now!
![image](https://user-images.githubusercontent.com/93275755/139150767-f6c73ec1-66cf-4cf1-aca0-5045eef7b9af.png)

![image](https://user-images.githubusercontent.com/93275755/139151254-c1b29cde-56e4-41d6-a290-c5f083b7431d.png)

----------

Key Ingredients to the Skywater PDK:
1. Documentation
2. PDK Library and files
3. Community

![image](https://user-images.githubusercontent.com/93275755/139152062-4eed8cc3-b385-485f-9e94-e72bfc768654.png)

--------------
## Open Source Tools

https://opencircuitdesign.com/open_pdks

Steps to installing the SKY130 PDK on your machine

1. clone the repo: "git clone https://github.com/RTimothyEdwards/open_pdks"
2. cd open_pdks
3. configure --enable-sky130-pdk
4. make
5. sudo make install

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

