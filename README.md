# Skywater-OpenLANE-physical-design

VSD SKy130 Openlane Physical Design is a 5-day workshop where I learnt about the opensource EDA tools involved in the synthesis, physical design and gdsii stages. 
First, I will be briefing about the Openlane flow and the tools involved in it. Later, I will documenting my journey of 5 days in the workshop.

## OpenLane 

OpenLane Flow is an automated RTL2GDSII flow which involves various opensource tools like OpenSTA, Yosys, Netgen, Magic etc. Below is the image illustration the flow along with
the tools involved. The main motto of this Openlane is to automate the RTL2toGDSII flow and producing clean GDSII files. 
![OpenLane Flow - from official github repo](https://github.com/efabless/openlane/raw/master/docs/_static/openlane.flow.1.png "OpenLane Flow - from official github repo")

## Table of Contents

* [Day-1 Inception of open-source EDA, OpenLANE and Sky130 PDK](Day-1-Inception-of-open-source-EDA-OpenLANE-and-Sky130-PDK)
  
  > [**1. Synthesis**](
   
* [**Day-2 [Good floorplan vs bad floorplan and introduction to library cells]**]

  > [**2. Floorplanning**]
 
  > [**3. Placement**]
   
* [**Day-3 [Design library cell using Magic Layout and ngspice characterization]**]
     
  > [**Working with a custom cell design (Here it was an inverter)**]
* [**Day-4 [Pre-layout timing analysis and importance of good clock tree]**]
      
  > [**4. CTS**]
 
* [**Day-5 [Final steps for RTL2GDS using tritonRoute and openSTA]**]
   
  > [**5. Routing**]
  
  > [**6. RC Extraction**]
  
  > [**7. Physical Verification**]
   
  > [**8. GDSII**]
-----------------

## Day-1 [Inception of open-source EDA, OpenLANE and Sky130 PDK]

The understanding of a chip started from the package and went till the macros inside a chip. Later, basic understanding of RISC-V ISA was given followed by a detailed understanding on how softwares interact with hardware and what are the intermediate components that are involved in the process. 

Moving forward, I was introduced to the OpenLane ASIC flow and the tools involved in it. In a nutshell, we get to give the RTL file (usually a .v file) and the PDK, in this case it is the skywater130 PDK, to this flow and the GDSII (clean) will be generated. OpenRoad application is the one where the PnR happens. Though for detailed routing, we need to move out of the OpenRoad application for using the TritonRoute (another tool) which is part of the flow. 

OpenLANE integrated several key open source tools over the execution stages:


| Parameters | Tools |
| --- | :--- |
| RTL Synthesis | yosys |
| abc | Technology mapping |
|Static Timing Analysis | OpenSTA |
|Floor Plan| init_fp, ioPlacer, pdn and tapcell Placement: RePLace (Global), Resizer and OpenPhySyn (Optimizations), and OpenDP (Detailed) |
|Placement| RePLace (Global), Resizer and OpenPhySyn (Optimizations), and OpenDP (Detailed)|
|CTS| TritonCTS |
|Fill Insertion| OpenDP/filler_placement |
|Routing| FastRoute (Global) and TritonRoute (Detailed) |
|SPEF Extraction|  SPEF-Extractor |
|GDSII| Magic and Klayout
|DRC Checks| Magic and Klayout |
|LVS checks| Netgen |
|Antenna Checks| Magic
|Circuit Validity Checker| CVC |

The main commands used in openLANE design flow in interactive mode are:

```
./flow-interactive 
prep -design <design> -tag <tag> -config <config> -init_design_config 
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
run_CVC

```


## Invoking OpenLane and performing initial preparations

![day 1 lab commands](https://user-images.githubusercontent.com/61493308/124503228-b8efc200-dde2-11eb-8535-5f39967a29ad.JPG)
![day 1 lab commands2](https://user-images.githubusercontent.com/61493308/124503653-9f9b4580-dde3-11eb-838d-2db430279cda.JPG)


## **1.Synthesis**

![synth command](https://user-images.githubusercontent.com/61493308/124503946-2a7c4000-dde4-11eb-8d19-474c52604738.JPG)
![ABC tech map](https://user-images.githubusercontent.com/61493308/124504264-bd1cdf00-dde4-11eb-8497-ac63a2c42294.JPG)




 ## Summary of cells in the design after optimization in the synthesis

![day2 statistics](https://user-images.githubusercontent.com/61493308/124504431-12f18700-dde5-11eb-8b98-6b87467de455.JPG)


## Slack Report of min and max paths of the clock
![slack min max paths](https://user-images.githubusercontent.com/61493308/124504494-39172700-dde5-11eb-947c-a2708ef5c2eb.JPG)



---------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Day-2 [Good floorplan vs bad floorplan and introduction to library cells]

A brief understanding on floorplan stage and the factors involved in it like core utilization, aspect ratio etc., was given. Later, I came across the method of loading the prev run file while preparing the openlane. While performing floorplan, I came across the precedency of various config files. Those files and the order of precedence is shown below.
### **2.Floorplanning**
### Default floorplan config file

![floorplan tcl](https://user-images.githubusercontent.com/61493308/124504845-e12cf000-dde5-11eb-9cc7-c3478a2cd43a.JPG)

![floorplan tcl2](https://user-images.githubusercontent.com/61493308/124504855-e5590d80-dde5-11eb-8c76-9ccb9545cd79.JPG)

### config file

![config tcl](https://user-images.githubusercontent.com/61493308/124504675-94e1b000-dde5-11eb-8a67-a8e4e5a4a6e6.JPG)




### Precendency
**Sky130a config file > config file > default floorplan config file**


In floorplan, we fix the core utilization ratio, aspect ratio and provide decap cells, welltap cells. Later, we fix the I/O pins position. The floorplan was done using *run_floorplan* command and employed the **Magic** tool to view the layout by importing the def file generated. 

## Floorplanning in OpenLANE
![day2 floorplan cmnds](https://user-images.githubusercontent.com/61493308/124505162-85169b80-dde6-11eb-885e-f19f748a69d0.JPG)
![floorplan1](https://user-images.githubusercontent.com/61493308/124505213-9c558900-dde6-11eb-8c58-410f79e31172.JPG)


## Magic tool invoking by providing lef, tech library and def file


```
magic -T /home/thanga/../pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def

```
![view floorplan command](https://user-images.githubusercontent.com/61493308/124506444-47ffd880-dde9-11eb-8940-2294037a8b8c.JPG)

![Untitled Workspace](https://user-images.githubusercontent.com/61493308/124505979-513c7580-dde8-11eb-95a0-9b022ab85953.jpg)


## Layout after floorplan stage in Magic tool
![floorplan view1](https://user-images.githubusercontent.com/61493308/124506530-754c8680-dde9-11eb-8b89-88096b7fff6e.JPG)



## Welltap cells arranged in checker board fashion
![pins and decap cells](https://user-images.githubusercontent.com/61493308/124506559-83020c00-dde9-11eb-8de1-e1bca3388e41.JPG)


## Location of Std cells in the floorplan
![standard cell layout](https://user-images.githubusercontent.com/61493308/124507677-e725cf80-ddeb-11eb-9e23-485942dce4ea.JPG)



## **3.Placement**

After performing floorplan, we place the standard cells (which were already present at the bottom left of the design) and observe the DRCs. We do this by giving the command *run_placement* in OpenLANE. Placement in OpenLANE occurs in two stages - Global placement and Detailed placement. In global placement, all cells are placed on the floorplan in a random manner and in detailed placement, the cells positions are legalized. Legalization in the sense, it makes sure that cells are not overlapping. In OpenLANE, placement happens by having a goal (for congestion driven) to reduce the HPWL (Half Parameter Wire Length), hence taking many iterations. We invoke the **Magic** tool to view the layout of the design after placement of std cells. 

### Placement in OpenLANE

```
run_placement

```

![placement](https://user-images.githubusercontent.com/61493308/124507766-1e947c00-ddec-11eb-8702-9f5f5b2b20f9.JPG)


## Invoking Magic tool to view placement.def file

```
magic -T /home/thanga/../pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def

```
![view pacement command](https://user-images.githubusercontent.com/61493308/124507912-6fa47000-ddec-11eb-9be3-c20a09f452b2.JPG)




## Layout of the design after Placement stage in Magic tool

![placement layout](https://user-images.githubusercontent.com/61493308/124510008-949ae200-ddf0-11eb-9b55-3be57cb5cf2c.JPG)


We generally know that there are two types of placements 
* Timing Driven
* Placement Driven

We can set the same in OpenLANE as well. Below is the config file in which the environment variable named *PL_TIME_DRIVEN* is set as **0** which indicates FALSE. Hence, the placement performed above is **Congestion Driven Placement** by default.



**The interesting thing to notice is that the PG routing is not done yet. Usually (in ASIC flow), we perform this during Floorplan stage itself. But in the OpenLANE flow, PG routing happens after the CTS and before Routing.**

------

## Day-3 [Design library cell using Magic Layout and ngspice characterization]

### 16-mask CMOS process
I found this interesting, hence noted it down while imagining each step in the process. 
Select the substrate
~40nm of SiO2
~80nm of Si3N4
~1um of photo-resist
Layouts -> masks are placed on photo-resist layer (acts as protection layer from photo-lithography process) - masks are used here
UV light will remove photo-resist
Remove the mask
etching Si3N4
Remove extra photo-resist  
oxidation growth on other areas by placing in oxidation furnace using a process called LOCOS (local oxidation of silicon
remove  extra Si3N4 using hot phosphoric acid

**N-well and P well formation**
-> Deposit photo-resist layer
-> place the mask according to the layout
-> UV light will remove the unmasked region
-> remove the mask
-> Deposit Boron atoms using Ion implantation process to create a P-well

By following similar process and by deposit phosphorous atoms to form N-well

Now take the complete material and place in high temp furnace to drive-in diffusion. After this process, N-well and P-well will diffuse deep into substrate diffusing half of the substrate). This process is called is twin-tub process.

**Formation of Gate**
-> Photo-resist material is deposited followed by the mask
-> Deposit boron on the p-well so that we achieve required doping concentration on the surface
Similarly, we need to do the process for n-well by using arsenic doping.
Now, we have n-doping and p-doping on the top layer of n-well and p-well. But in this whole process, the chances of damage to the SiO2 layer is inevitable. Hence, we etch this oxide layer using dilute hydrofluoric acid and regrow the oxide layer. All these will effect our threshold voltage.

Now, we grow a thick layer of poly-silicon oxide layer. Again , we deposit the photo-resist material and place mask. We remove the extra photo-resist layer and the masks as well. Now, remove the poly silicon layer outside the photo-resist. 
Remove the photo-resist material on the gate poly-silicon and at the end, we get the required gate.

Now, we have Gate. Next, we need to form the drain. 
Deposit the photo-resist material and place the mask on the m-well part. Remove the photo-lithography material and the mask as well. Dope the phosphorus on the p-well side. By this, we get the N-implant in the p-well. 
We repeat the same steps to deposit the lightly doped P-implant in the N-well. 
This is called as Lightly doped drain (LDD) formation. 
After this, we deposit the Si3N4 and use plasma an-isotropic etching process to remove this and form a side-wall  spacers at the gate. 
Now, we deposit a thin layer of screen oxide to avoid the channeling effect during implantation.

**Formation of source and drain**
-> We follow the similar process of protecting one side of the substrate (N-well) and dope arsenic on the N-well side. By this, we get more doping of N in the P-well but the less doping which is protected by the side-wall spacers is retained. 
-> We do the same process but use Boron for N-well. 
After this, we place this device in a high-temp furnace to let the P+ and N+ penetrate more into their respective wells. 
After this whole process, we get a doping configuration like P+P-N for N-well and N+N-P for P-well (+ indicated more doping, - indicates less doping)

Now, since our gate, source and drain regions are ready, we now build the contacts to control them.
We, first, etch the thin oxide using HF solution. We deposit titanium on wafer surface using sputtering process. Then, we heat the wafer in N2 atmosphere which results in the formation of TiS2 in-place of pure titanium. We also have TiN developed on the wafer which will be used for local communications.
Now, we do photo-lithography (placing photo-resist material and removing the same from unwanted areas using masks). After this we will be left out with the TiN which will be used for interconnects.
## A view of CMOS Inverter 
![cmos middle step](https://user-images.githubusercontent.com/61493308/124510500-944f1680-ddf1-11eb-9091-0b73e97d869f.png)



**Higher level metal formation**
-> since the surface of the wafer (incomplete mosfet in this case) is uneven, we deposit a thick layer of SiO2 which is doped with phosphorous or boron. and level up the surface using a process called chemical mechanical polishing (CMP) technique. 
To develop the higher metal connections, we follow the photo-lithography process and use masks to make space at our required connection positions. 

After this, we deposit a series of materials and do photo-lithography and prepare 3 metal layers. After this, we drill holes to the terminals and get our connections to control our source, gate and drain  regions of the inverter created. If we carefully observe, we totally used 16 masks (the layouts) to build this device. Hence, this complete process is called as “16-mask CMOS process”

## Final view of CMOS Inverter 

![cmos last step](https://user-images.githubusercontent.com/61493308/124510521-9e711500-ddf1-11eb-9090-1e4e2ea69785.png)


## Understanding DRC in MAGIC tool
To understand the MAGIC tool and its DRC engine, we use the inverter design mag file. For that, we clone the repo and run MAGIC tool using the **Skylane130A** tech file and **inverter** mag file.

### Cloning the repo  for the inverter  
![image](https://user-images.githubusercontent.com/61493308/124510849-59011780-ddf2-11eb-90b5-2b323dca6ad0.png)

### Layout of the inverter  

![magic inverter1](https://user-images.githubusercontent.com/61493308/124510906-733af580-ddf2-11eb-843a-2de2132eddb0.JPG)

### Layers menubar 

![layout window](https://user-images.githubusercontent.com/61493308/124510946-8a79e300-ddf2-11eb-894e-a58933ee32ee.JPG)





### Library Characterization

Basically, by library characterization, we mean to identify 4 parameters. Rise transition delay, fall transition delay, rise cell delay and fall cell delay
We consider a basic inverter layout to identify the parameters. After importing the **mag** file and the **skylane130A tech** file, we extract the spice netlist. 

#### Extracting Spice netlist  

```
extract all
ext2spice

```
![grid help](https://user-images.githubusercontent.com/61493308/124543308-a6a57080-de42-11eb-8fa4-6c8cac394da0.JPG)



#### Extracted Spice netlist 

![Generated spice3 file](https://user-images.githubusercontent.com/61493308/124543418-d3f21e80-de42-11eb-8dee-a3e6bfcc79aa.JPG)


After extracting it, we invoke the NGSPice tool to performt the transiant analysis and find the 4 parameters mentioned at the beginning of this section.

## Invoking NGSpice tool   

```
ngspice sky130Ainv.spice

```
![ngspice](https://user-images.githubusercontent.com/61493308/124543815-9641c580-de43-11eb-833c-7e8166b5e668.JPG)


If we observe in the above netlist, there are only components and its interconnects present. To perform the analysis, we need to include libraries for connecting the behavious of components to its libs. We need to add sources and mention the type of analysis.

First, we include the libs file for pmos and nmos. Observe the below lib file in which few parameters are mentioned. But we include the model from this lib. 

### Parameters in lib file   

In the same file, we have 32 models for pmos i.e., from pshort_model.0 to pshort_model.3l
### pshort_model.0   
![pshort](https://user-images.githubusercontent.com/61493308/124544162-3dbef800-de44-11eb-844f-c7f58dbec934.JPG)


### nshort_model.1   
![nshort](https://user-images.githubusercontent.com/61493308/124544209-53ccb880-de44-11eb-8258-c5ba89f5ccf2.JPG)




If you could observe among these models, width varies. When we compare remaining all models, we could observe changes in width as well as length. Hence, choosing appropriate model will decide our simulations.

After we complete including libs and specifying sources followed by type of analysis, we run simulation

### Modified spice netlist
![edited spice3](https://user-images.githubusercontent.com/61493308/124544321-85de1a80-de44-11eb-8af7-d52d259e8442.JPG)

#### Transiant Analysis
To plot transient analysis output, where y - output node and a - input node
```
plot y vs time a
```
![transient analysis](https://user-images.githubusercontent.com/61493308/124544347-9098af80-de44-11eb-9253-d9dfa664634f.JPG)


Now, we calculate the 4 parameters as part of library characterization
* Rise transition delay = Time taken for the output signal to reach from 20% of max value to 80% of max value.
* Fall transition delat = Time taken for the output signal to reach from 80% of max value to 20% of max value.
* Cell rise delay = Time difference between 50% of rising output and 50% of falling output
* Cell fall delay = Time difference between 50% of falling output and 50% of rising output








## Day-4 [Pre-layout timing analysis and importance of good clock tree]

### Developing LEF file for a standard cell design 
Before we move to this step, we need to make sure that the layout follows two main rules as per the requirements of the PnR tool. 
* The ports must stay on the interconnect of the grids
* The width and height of the std cell must be odd multiples of no.of grids




To understand this, we first need to convert the grids size in the MAGIC tool window to the track pitch size. For that, first we observe the values in the tracks.info file under the skylane130 files which is under Skylane130a PDK.

### Tracks info file
![tracks](https://user-images.githubusercontent.com/61493308/124545175-2aad2780-de46-11eb-8116-8063988493fe.JPG)


Here the first value defines the offset followed by the pitch width. We have these values for both X and Y axis. We transform the default grid size in the MAGIC tool to the tracks.info defined values. We use the command *grid x y xorigin yorigin* where x is horizontal pitch value, y is vertical pitch value, xorigin is horizontal offset value and yorigin is vertical offset value. After implementing this command, our grid structure looks like below.

### Modified Grid structure 
![grid](https://user-images.githubusercontent.com/61493308/124545072-fafe1f80-de45-11eb-972d-08cfd11e9361.JPG)
![grid2](https://user-images.githubusercontent.com/61493308/124545085-00f40080-de46-11eb-97ee-f82046035262.JPG)

By observing the above layout, it is clear that the two rules mentioned at the beginning of the section are met. Ports A and Y are on the interconnects of the grid and the size of the cell layout is 3 times of single grid. 

### Converting Std cell layout to LEF file.

Since the requirements are met, next step is to generate the LEF file. To do this, we go through two steps.
* Naming the pins
* Defining the pin attributes and its use

**Naming the Pin :**
To do this, we first select the area of the pin (suppose 'A') and click on *Edit* and *Text*. We now get the text dialogue box. We define the name, size of the text, order of the pin and the metal type. For Pins, we use *locali* as metal type and for power, we use *metal 3*.


### Text Dialogue box for pin "A"
![conveting pins to ports](https://user-images.githubusercontent.com/61493308/124545424-a14a2500-de46-11eb-9d28-09b378464da9.png)


**Defining Pin use and attributes :**
In this step, we define the pin class as *input/output* and pin use as *signal/power/ground*. 
The commands we use to perform these are - 
 *port class [input/output]*
 *port use [signal/power/ground]*
 
 After defining the pins, we extract the LEF file from the layout by using command *lef write*. Below is the lef file generated for the inverter layout.
 ## LEF file for inverter
 ```
 write lef
 ```
 
 ![lef write](https://user-images.githubusercontent.com/61493308/124549622-0b65c880-de4d-11eb-858e-5ddfc9ad1857.JPG)

## Generated LEF file

![lef file 2](https://user-images.githubusercontent.com/61493308/124549749-3c45fd80-de4d-11eb-97e6-9e044138c8ca.JPG)

![lef file3](https://user-images.githubusercontent.com/61493308/124549760-410ab180-de4d-11eb-96fd-abe619e4f99b.JPG)


Now since we generated the LEF file for the inverter, we need to include it with our design and test it. **The main goal for this whole process till now and next, is to learn how one can add a custom cell design to the OpenLANE flow and work with it.**

To proceed, we need to provide paths for the env variables of the OpenLANE using config.tcl file. There are three types of lib files -  fast, slow and typical.
Typical lib is the one used for the synthesis purposes and remaining two are same as the fast.lib and slow.lib files we usually come across in the ASIC flow.
Below are the snapshots of the three lib files


 ### Fast lib file
 ![fast lib](https://user-images.githubusercontent.com/61493308/124550159-e2920300-de4d-11eb-97ac-fb0530b3f2f1.JPG)

![fast lib 2](https://user-images.githubusercontent.com/61493308/124550037-b2e2fb00-de4d-11eb-943b-72bbb68e4c52.JPG)

 ### Slow lib file
 
![slow lib](https://user-images.githubusercontent.com/61493308/124550175-e9207a80-de4d-11eb-9c82-0ac7e2e034a6.JPG)


Now, we perform synthesis and check whether the custom cell got mapped by abc mapping or not. 

```
run_synthesis
```
![synth command](https://user-images.githubusercontent.com/61493308/124586288-e20c6300-de73-11eb-8b28-90ac9698f522.JPG)



Below is the timing after the synthesis.

![day 1 slack](https://user-images.githubusercontent.com/61493308/124586235-d456dd80-de73-11eb-82d7-f5c219f88de6.JPG)




As we observed, the slack is too high and we need to reduce it. 

After this, we need to merge our custom cell lef file and perform the synthesis, followed by floorplanning and placement. The reason we are proceeding to the placement despite having negative slack violations, is to check whether our custom cell got placed along with the picorv32a design or not.

First,we need to modify config.tcl file in the src folder

## Modified config file

![modified config](https://user-images.githubusercontent.com/61493308/124586762-7a0a4c80-de74-11eb-901b-28e951361653.JPG)

Now we again run the commands
```
 package require openLANE 0.9
 prep -design picorv32a -tag 03-07_16-04 -overwrite
 set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
 add_lefs -src $lefs
 run_synthesis
 run_floorplan
 run_placement

```

To include extra lef files 

```
   add_lefs -src $lefs
   
 ```
![124370761-71f5b580-dc98-11eb-80c4-7440b39768f8 (1)](https://user-images.githubusercontent.com/61493308/124614995-7259a080-de92-11eb-8164-594cb1b422e5.png)

## Perform synthesis
``
run_synthesis

``
![124370832-0233fa80-dc99-11eb-849b-6288f0294e3d](https://user-images.githubusercontent.com/61493308/124615099-8c937e80-de92-11eb-815f-f091d430183c.png)




### Fixing of Violations
Inorder to avoid negative slack as it is not ideal to our design, we optimize the slack values and maintain a positive value.Inorder to do this we set various environmental variables and check if the slack is changing or not as given in the following image.We can do the modifications in the README.md file present in the configuration directory

These are the changes which has to been done to the environment variables and now the flow is run again


![124363548-751d8100-dc59-11eb-9305-6fd64261e7e1](https://user-images.githubusercontent.com/61493308/124612088-ab444600-de8f-11eb-898a-f33afd5a1142.png)

![124377746-2f999c00-dccb-11eb-8514-5d1dd1a86078](https://user-images.githubusercontent.com/61493308/124612197-c44cf700-de8f-11eb-977e-2015ad2e13ca.png)

![124377866-f281d980-dccb-11eb-9411-b929459be206](https://user-images.githubusercontent.com/61493308/124612264-cfa02280-de8f-11eb-8338-8fd4994c6f49.png)





If we could observe in the main lef file, we could see the custom cell lef details in it.
![lef file 2](https://user-images.githubusercontent.com/61493308/124587840-b8543b80-de75-11eb-80b8-b0c56da85fde.JPG)




### **4.CTS**

The main concern in generation of clock tree is the clock skew, difference in arrival times of the clock for sequential elements across the design.To ensure timing constraints CTS will add buffers throughout the clock tree which will modify our netlist. This will generate new def file.

![OCV-clocks](https://user-images.githubusercontent.com/61493308/124596811-54834000-de80-11eb-8d1d-22ffb1a439d9.jpg)



After performing CTS, we need to check for our slacks again. This is what we call as "Post-CTS timing analysis". Here we check the hold slack as well because it was not having any significance in previous stages due to absence of clocks. Before that, we need to make some preparations such as creating a db file in the *openROAD* application etc. 





We all know that in CTS to adjust slew values, CTS engine (in ASIC Flow) uses special buffers called "Clock Buffers". Same happens in the OpenLANE flow. The CTS engine chooses between 4 types of clock buffers present in the list as shown below. Usally, CTS engine chooses the clock buffers from left to right and checks for slew values. We can force the CTS engine to choose required the clock buffer by removing other buffers from the list. This is want I did in the workshop. I modified the clock buffer env variable list and performed CTS again. Wtih this we get a more improved slack value. 


----

## Day-5 [Final steps for RTL2GDS using tritonRoute and openSTA]


### Power Distribution Network
Power planning is a step which typically is done with floorplanning in which power grid network is created to distribute power to each part of the design equally. In openLANE flow it is done before routing.

![img16](https://user-images.githubusercontent.com/61493308/124599901-d0cb5280-de83-11eb-954a-6f8ff5ad7d8f.png)

command for PDN
```
gen_pdn

```
![124378326-5ad1ba80-dcce-11eb-9a2c-87d2c2b3ae39](https://user-images.githubusercontent.com/61493308/124600975-f0af4600-de84-11eb-96db-edabea0db9da.png)

![124378337-67561300-dcce-11eb-8a07-caa6efe97ce2](https://user-images.githubusercontent.com/61493308/124601079-0c1a5100-de85-11eb-9f55-5f4149d55c7f.png)









### Routing

![Untitled Workspace (1)](https://user-images.githubusercontent.com/61493308/124609858-a1b9de80-de8d-11eb-95b2-0f3163babd8d.jpg)






When we compare to ASIC flow, we recollect that there will be two steps in the routing stage
1. Global routing 
 Global routing: Here the routing region is divided into rectangular gridcells,It forms routing guides for the further routing .It basically     forms a rough path of connection.

2. Detailed routing
Detailed routing: The detailed route determines the vias and segments accordingly with the global route solution.This ensures that routing happens within the routing guides.

### command for routing
 ```
 run_routing
 
 ```
 ![124382086-30d6c300-dce3-11eb-8e02-6b507bd76371](https://user-images.githubusercontent.com/61493308/124601784-dde94100-de85-11eb-9e4d-45cf1f0f514d.png)

 ![124382459-2c130e80-dce5-11eb-8d3c-d1aa3a1e696d](https://user-images.githubusercontent.com/61493308/124602018-11c46680-de86-11eb-97ac-941e4d88c299.png)
 
 ### To view routing layout in magic
 
 ```
 $ magic -T /home/thanga/Desktop/work/tools/openlane_working_dir/pdks/sky130libs.tech lef read ../../tmp/merged.lef def read picorv32a.def

```

![image](https://user-images.githubusercontent.com/61493308/124614565-fe1efd00-de91-11eb-9772-68954b5711dd.png)



### **6.RC Extraction**

This step is to extract the SPEF file from the routing database. Unfortunately, we do not have a SPEF Extractor embedded in the OpenLANE flow. Hence we use the SPEF Extractor tool seperately which is mostly of running python scripts whose inputs are lef (the physical library) file and the def (the routed design) file. 

### **7.Physical Verification**

After all the steps performed till now, we need to do the physical verification. We have two tools embedded in the OpenLANE flow - MAGIC for DRC and NETGEN for LVS. 

### **8.GDSII**

After getting a clean DRC design, we are ready to tap out (send to the shuttle for fabrication). To extract the GDSII file, we use MAGIC. 





## Acknowledgments

1. [Kunal Ghosh](https://github.com/kunalg123) - Co-founder (VSD Corp. Pvt. Ltd)
2. [Nickson Jose](https://github.com/nickson-jose/) - Teaching Assistant - One who guided through out the OpenLANE flow
3. [Tim Edwards](https://github.com/RTimothyEdwards)
