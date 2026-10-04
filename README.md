# DIY-21700-Battery-System
(THIS REPO IS DEPRECATED AS OF 2026/10/03, SEE [DIY 21700 Battery System V2](https://github.com/BradleyCanty/DIY-21700-Battery-System-V2))
## Introduction
This is a do-it-yourself battery system intended for use with Unmanned Aerial Vehicles or Unmanned Ground Vehicles.

It consists of...

1) a battery using lithium ion 21700-form-factor cells, with battery configurations spanning from 6s3p to 12s6p
2) a vehicle adapter with place-to-lock/press-to-unlock latching mechanism
3) an off-vehicle charging station

(12s4p battery system configuration shown below)

<p align="center">
  <img src="0_Misc/Readme_Images/readme_12s4p_battery_system_2.jpg" width="100%">
</p>

**This is a complete and working system, with all design files (CAD and PCB design) provided for your use:**\
Multiple battery system configurations have been built and flight tested. Additionally, all CAD models have been 3D printed to ensure they work as intended. Finally, if none of the configurations presented here suit your needs then you can create your own battery configuration, since all CAD files (Solidworks part files and STLs) and PCB design files (KiCad project files) are included here for your use.

**Version 2 will have cell voltage and temperature monitoring safety features (sent as a BATTERY_STATUS message over MAVLink to MAVLink-compatible autopilot software, such as ArduPilot and PX4) so star this repo for updates**

## Purpose
The parametric nature of this system allows designing and building a battery system specific to your vehicle's requirements. As such, **the value add of this system is threefold:**
1) you can use cells of your choice (i.e., can optimize for either power output or energy capacity) in a configuration suitable for your vehicle's operating envelope
2) you can enjoy a significant cost savings (~50% discount at the time of this writing) by building it yourself (see 'Bill of Materials' section for cost info)
3) the battery's latching mechanism presents a common interface for battery swapping (either manually or by robot arm); this is important since battery swapping vastly increases system uptime when compared with on-board charging

## Battery Mounting Choice
This battery system is designed such that the battery itself is top-mounted onto the vehicle its powering (and top-mounted onto its charging station). This mounting method was chosen to simplify battery swapping (either by human hand or by robot gripper); the process to swap the battery is as follows:
1. get within close proximity of the battery
2. translate grippers directly over the battery
3. rotate grippers to be aligned with the battery end plates
4. translate grippers underneath each end plate ledge
5. close grippers such that the end plate buttons are pressed down, thus unlatching the battery from the vehicle adapter
6. lift battery away from the vehicle
7. carry battery to its charging station

A similar but reversed process occurs for taking a fully charged battery off its charging station to the vehicle.

If you intend for such a battery to power your multirotor you might ask: "wouldn't the multirotor be top heavy, thereby adversely affecting stability and control?". The simple answer is no, it wouldn't **if you mount the battery directly above the bulkhead where the motor arms are mounted to the body**. In doing this, the center of gravity moves closer to the thrust plane (i.e. the plane where the rotors spin), which decreases overall inertia and thereby increases control responsiveness. So the overall impact is positive, not adverse. As final proof of functionality, I have personally flight tested a multirotor using a 12s4p battery, with no stability or control issues whatsoever.

(12s4p battery and vehicle adapter used on large quadcopter shown below)

<p align="center">
  <img src="0_Misc/Readme_Images/readme_12s4p_quad_1.jpg" width="48%">
  <img src="0_Misc/Readme_Images/readme_12s4p_quad_2.jpg" width="48%">
</p>

Details on mounting the vehicle adapter to the vehicle can be found in Build Step 7: [Integrate vehicle adapter into vehicle](5_Integration_and_Test_Instructions/1_Vehicle_Adapter_Integration_Instructions/vehicle_adapter_integration_instructions.md)

## Cell Form Factor Choice
Considering the cell form factor, **21700 cells** (i.e. cylindrical cells having 21[mm] height and 70[mm] length) **are used in the battery since they are cheap, easily obtained, and have high gravimetric energy density**. In comparison to other cells types, 18650 cells tend to have lower gravimetric energy density, while 4680 cells, prismatic cells, and pouch cells aren't widely available to the general public (as of late 2026). Concerning battery design, the battery configuration is defined by its number of cells in series (which sets its voltage) and its number of cells in parallel (which sets its charge capacity and its max current draw limit). In the system presented here, the possible number of cells in series ranges from 6 to 12, while the possible number of cells in parallel ranges from 3 to 6. That is, the smallest possible battery configuration is one having 6 cells in series and 3 cells in parallel (18 cells total), while the largest battery configuration is one having 12 cells in series and 6 cells in parallel (72 cells total).

(6s3p and 12s6p battery system configurations shown below)

<p align="center">
  <img src="0_Misc/Readme_Images/readme_6s3p_battery_system_4.jpg" width="48%">
  <img src="0_Misc/Readme_Images/readme_12s6p_battery_system_2.jpg" width="48%">
</p>

As a final note, **the cell-to-pack mass fraction across all battery configurations is found to be around 83%**. This value is important since once you select a specific 21700 cell to use, you can then estimate the battery's total mass and the battery's gravimetric energy density:\
$m_{battery} \approx m_{cell} * M * N / CPMF$ = battery mass [kg]\
where\
$m_{cell}$ = cell mass [kg]\
$M$ = number of cells in series\
$N$ = number of cells in parallel\
$CPMF$ = cell-to-pack mass fraction

$E_{battery}^* = (battery\ nominal\ voltage) * (battery\ charge\ capacity)$\
&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &thinsp;= $(M * V_{cell, nominal})*(N * Q_{cell,expected}) / m_{battery}$\
&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &thinsp;= gravimetric energy density [Wh/kg]\
where\
$V_{cell, nominal}$ = nominal cell voltage = 3.6[V] (for li-ion cell)\
$Q_{cell,expected}$ = expected cell charge capacity for normal vehicle operation (computed using the process outlined in 'Steps for Selecting a Battery Configuration' section found below)

For example, for a battery having 8 cells in series and 5 cells in parallel (i.e. MsNp = 8s5p) using EVE 50PL 21700 cells, we have\
$m_{cell} = 0.066 [kg]$ (real-world value... datasheet gives 0.072 [kg] per cell)\
$M$ = 8\
$N$ = 5\
$CPMF$ = 0.83\
$Q_{cell,expected}$ = 4.5 [Ah]

Then, battery mass and gravimetric energy density are computed to be\
$m_{battery} \approx 0.066 * 8 * 5 / 0.083 =  3.18$ [kg]\
$E_{battery}^* = (8 * 3.6)*(5 * 4.5) / 3.18$ = 203.8 [Wh/kg]

## Safety
Additionally, safety is paramount. A temperature sensor circuit consisting of a microcontroller and busbar voltage sensing is in progress (I had assumed thermistors would work in the cell failure detection role, but apparently the thermal mass of the busbar is too large to detect it in time). I am in the process of designing and prototyping a circuit which detects voltages at each busbar and sends them in MAVLink packets over UART to the flight controller (running either Ardupilot or PX4), with options of alerting the pilot or autolanding the vehicle if a measured busbar voltage is lower than some threshold. **The software development, physical packaging, and testing have posed significant issues which require a non-trivial amount of iteration, time, and money: I am working through these issues, but at some point I need to call a "pencils down" and declare victory, so Version 1 is without this safety feature. However, I assure you that Version 2 will have this safety feature, so stay tuned for that.** Additionally, I am in the process of making a low voltage alarm circuit featuring a buzzer and orange LED lights which buzz and flash upon low voltage detection, which I intend to include in Version 2 of this project.

Concerning the electrical system limitations, the PCBs (the 'Battery Board', 'Vehicle Adapter', and 'Antispark Board') are designed to handle 60 amps of continuous current (assuming a maximum temperature rise of 20°C). **You should consider the 60 amps of continuous current limitation as the primary limiting factor in your selected configuration.** This current limitation is written out on each PCB via silkscreen text. Note that this is max continuous current, <ins>not max short-duration current</ins>: max short-duration current is dictated by the 21700 cell you choose (should be listed in its datasheet) and the number of cells used in parallel. Specifically, its given by:

$I_{max\ total,short\ duration}$ = $I_{max\ cell} * N$\
where\
$N$ = number of cells in parallel

## DIY or Build from a Kit
Building this system consists of many steps (see 'Build Steps' section), and requires specific tools (see 'Required Tools' section). As a consequence, many people may find that doing this on their own is too advanced for them. Therefore, a kit is available to make the the build process easier: in essence, all you need to do upon receiving the kit is
1) Insert your favorite cells (purchased separately) into the fully-prepared 3D printed frames
2) spot weld the busbars to the cell terminals
3) Solder the balance cable wires to the busbars
4) Cut and solder the 8 AWG power wires to the battery PCB
5) fasten the fully-prepared 3D printed parts and pre-populated PCBs together with screws
6) Test the battery by performing a few charge/discharge cycles on your battery charger (the battery charger is not included in the kit)

Then, the tools required for building the battery system from the kit is
1) a suitable spot welder
2) soldering iron
3) metric hex wrenches
4) a suitable battery charger

Kit contents, pricing, and ordering information can be found [here](0_Misc/build_kit_info.md).

Whether you are building from raw materials or building from the kit, the 'Build Steps' section found below contains pictures and descriptions at each step to aid in the build process.

## Battery System Components in Detail
The battery system consists of the following components:
1) the battery
2) the vehicle adapter
3) the charging station

Each is explained in turn

### The Battery
Each component is sized according to the chosen battery configuration. Possible battery configurations range from 6 to 12 cells in series, and 3 to 6 cells in parallel. Spelled out, these configurations are:
* Six in series:
    - Three in parallel (6s3p)
    - Four in parallel (6s4p)
    - Five in parallel (6s5p)
    - Six in parallel (6s6p)
* Eight in series:
    - Three in parallel (8s3p)
    - Four in parallel (8s4p)
    - Five in parallel (8s5p)
    - Six in parallel (8s6p)
* Ten in series:
    - Three in parallel (10s3p)
    - Four in parallel (10s4p)
    - Five in parallel (10s5p)
    - Six in parallel (10s6p)
* Twelve in series:
    - Three in parallel (12s3p)
    - Four in parallel (12s4p)
    - Five in parallel (12s5p)
    - Six in parallel (12s6p)
  
  The battery consists of
  - two frames, which hold the cells between them
    <p align="center">
      <img src="0_Misc/Readme_Images/readme_battery_frames_1.jpg" width="48%">
      <img src="0_Misc/Readme_Images/readme_8s5p_battery_frames.jpg" width="48%">
    </p>
    
  - a top plate, which covers the top frame
    <p align="center">
      <img src="0_Misc/Readme_Images/readme_battery_top_plate_1.jpg" width="48%">
      <img src="0_Misc/Readme_Images/readme_8s5p_battery_top.jpg" width="48%">
    </p>

  - a bottom plate, which connects with the bottom frame and houses the female 12-pin power and balance connectors
    <p align="center">
      <img src="0_Misc/Readme_Images/readme_battery_bottom_plate_1.jpg" width="32%">
      <img src="0_Misc/Readme_Images/readme_8s5p_battery_bottom_plate.jpg" width="32%">
      <img src="0_Misc/Readme_Images/readme_8s5p_battery_bottom.jpg" width="32%">
    </p>
    
  - two end plates, which clamp the frames together and serve as handles containing the button-press-to-unlatch mechanism
    <p align="center">
      <img src="0_Misc/Readme_Images/readme_battery_end_plates_1.jpg" width="48%">
    </p>
  
### The Vehicle Adapter
Connects the battery with the vehicle, both mechanically and electrically. For the mechanical interface, it has two latches which interface with the two button-press-to-unlatch mechanisms on either end of the battery. For the electrical interface, it has a male 12-pin connector.
  <p align="center">
    <img src="0_Misc/Readme_Images/readme_vehicle_adapter.jpg" width="48%">
  </p>

### The Charging Station
Interfaces the battery with a standard RC battery charger via XT60 connector for power and JST-XH connector for balancing. Again, <ins>this is not a charger itself: its an interface between the battery and the charger</ins>.
<p align="center">
  <img src="0_Misc/Readme_Images/readme_charging_station.jpg" width="48%">
  <img src="0_Misc/Readme_Images/readme_charger_annotated.png" width="48%">
</p>

## Steps for Selecting a Battery Configuration
In general,\
**The number of cells in series determines the battery voltage:**\
$battery\ max\ voltage = (cell\ max\ voltage) * (number\ of\ cells\ in\ series)$\
where\
$cell\ max\ voltage$ = 4.1 V (for LiIon cells)

**The number of cells in parallel determines the battery charge capacity:**\
$$battery\ charge\ capacity = (cell\ expected\ charge\ capacity) * (number\ of\ cells\ in\ parallel)$$\
where\
$cell\ expected\ charge\ capacity$ = a function of expected average current draw in cruise (fixed wing) or hover (VTOL)

Specifically, to find the battery's expected charge capacity, check the 21700 cell's datasheet for the plot of Voltage vs Charge Capacity (which contains curves of various discharge rates) and then match your vehicle's expected average current draw divided by the number of cells in parallel to the corresponding discharge rate curve in the plot. Finally, find the cell's capacity corresponding to an "empty" voltage of around 2.8 V, and then multiply it by the number of cells in parallel to get the battery's charge capacity.

For example, suppose you are using Molicel P50B cells in an 8s5p battery configuration on a vehicle expected to draw 1300 Watts of power in nominal operation, then the steps to find the expected charge capacity are as follows: 

1) Compute the average discharge current in nominal operation:\
   $I_{avg}$ = $P_{nominal}$ / $V_{batt,nominal}$ = $P_{nominal}$ / ($V_{cell,nominal}$ * M)\
   where\
   M = number of cells in series = 8\
   $V_{cell,nominal}$ = li-ion cell nominal voltage = 3.6V
   
   Plugging in the values...\
   $I_{avg}$ = 1300 / (3.6 * 8) = 45.1 Amps
   
2) Compute the discharge rate per cell: 45.1 Amps / 5 cells in parallel = 9.03 Amps
3) Find the Voltage vs Charge capacity plot in the Molicel P50B datasheet
4) On the plot, draw a horizontal line at the cutoff voltage (here 2.8V) across the plot, and stop upon reaching the curve corresponding to the cell's discharge rate (here the 10 Amp curve)
5) On the plot, draw a vertical line to the abscissa to obtain the cell's nominal charge capacity
(see plot below)

<p align="center">
  <img src="0_Misc/Readme_Images/readme_discharge_rate_plot_example.png" width="60%">
</p>

6) Finally, compute the battery's nominal charge capacity:\
   $Q_{batt,nominal}$ = $Q_{cell,nominal}$ * M\
   where\
   M = number of cells in parallel = 5

   Plugging in the values...\
   $Q_{batt,nominal}$ = 4500 * 5 = 22500 mAh = 22.5 Ah

Once battery nominal charge capacity is found, you can **compute the vehicle's expected endurance** (i.e. operating time under nominal conditions) using the following equation:\
t = $V_{batt,nominal}$ * $Q_{batt,nominal}$ / P\
where\
$V_{batt,nominal}$ = $V_{cell,nominal}$ * M

Using the values from the previous example, we have...\
t = (3.6 * 8) * 22.5 / 1300 = 0.498 hours = 29.9 minutes

This is all predicated on the accuracy of your predicted power draw, which is a function of your payload mass, battery mass, structure mass, and the type of motors (and, for multirotors, the size of the rotors) used on the vehicle. **So, in summary, selecting a battery configuration is an iterative process.**

For a multirotor, the appropriate battery configuration can be determined using this [multirotor design methodology](0_Misc/multirotor_design_methodology.md).

## Required Tools
* Soldering iron (for general soldering)
* Threaded insert soldering iron tip 
* Hot plate (for SMD soldering)
* Spot welder (for battery bus bar welding)
* 3D printer
* Deburr tool (for removing edge artifacts from 3D prints)
* Wire stripper
* Wire cutter
* Super glue
* Sheet metal shears (for cutting busbars)
* Hand drill or drill press (for cutting wire pass-through holes into busbars)

## Bill of Materials (BOM)
The battery BOM is found [here](3_Bill_of_Materials/battery_bom.md)

The vehicle adapter BOM is found [here](3_Bill_of_Materials/vehicle_adapter_bom.md)

The charging station BOM is found [here](3_Bill_of_Materials/charging_station_bom.md)

## Build Steps
### Things you should know before starting:
* ASA filament is used in all the 3D printed parts since it's ultraviolet resistant, so it can be left outdoors without degrading in strength. However, 3D printing it releases toxic fumes (specifically, printing it releases volatile organic compounds along with ultrafine particles, either of which may be carcinogenic), so use a fan-filter system when printing ASA. Also, ASA is prone to warping and so must be printed in a heated enclosure. Even with a heated enclosure, a brim is often needed on large parts to keep them from peeling away from the print bed. If the peeling at edges occurs at any time during the print, its a failed print so cancel it and reprint it with a 5 mm increase to the brim width. Do this iteratively until the print is successful.
* The printed circuit boards (PCBs) can be manufactured by uploading the zipped gerber files (provided in the repo) to a PCB manufacturer, such as PCBWay or JLCPCB
* The PCB components can be purchased from electronic component suppliers, such as DigiKey or Mouser, but know that you will need to hand-solder the through-hole components and hot plate solder the SMD components
* The build process is very frustrating and time-consuming, especially
    - 3D printing the parts, with ASA prints likely to fail due to warping
    - populating the antispark circuits with SMD components and soldering them using a hot plate
    - cutting wires to length and soldering them
    
    To save time and frustration, pre-made kits for each battery configuration are available for purchase: see 'DIY or Build from a Kit' section above for details on ordering a kit.

### To make the build process as simple as possible, proceed in this order:
  1. **Decide on a battery configuration**
  2. **Order the required PCBs and associated components:**\
     each system needs two antispark PCBs, one charging station PCB (pick the one that matches your configuration), one battery PCB (pick the one that matches your configuration), and one vehicle adapter PCB, all found [here](2_PCB_Files). See the READMEs for BOMs and ordering information.
     
  4. **Build the antispark boards:** instructions found [here](4_Build_Instructions/1_Antispark_Build_Instructions/antispark_build_instructions.md)
  5. **Build the charging station:** instructions found [here](4_Build_Instructions/2_Charging_Station_Build_Instructions/charging_station_build_instructions.md)
  7. **Build the battery:** instructions found [here](4_Build_Instructions/3_Battery_Build_Instructions/battery_build_instructions.md)
  8. **Build the vehicle adapter:** instructions found [here](4_Build_Instructions/4_Vehicle_Adapter_Build_Instructions/vehicle_adapter_build_instructions.md)
  9. **Integrate vehicle adapter into vehicle:** instructions found [here](5_Integration_and_Test_Instructions/1_Vehicle_Adapter_Integration_Instructions/vehicle_adapter_integration_instructions.md)
  10. **Perform vehicle operating envelope testing:** instructions found [here](5_Integration_and_Test_Instructions/2_Vehicle_Envelope_Testing_Instructions/vehicle_envelope_testing_instructions.md)

## TO DO
### V1 TO DO IMMEDIATELY
* Consolidate and simplify the battery sizing methodology

### V1 TO DO LATER
* Take pictures of antispark circuit build and put into instructions
* Complete the 'Perform vehicle operating envelope testing' instructions

### V2 TO DO IMMEDIATELY
* Replace JST-XH balance connectors used in battery interface with machine header pins: female on battery, male on vehicle adapter and charging station
* Implement safety features: measure voltages at each busbar, and measure temperatures at two cells, and report as a BATTERY_STATUS message over MAVLink
* Refactor end plate latching mechanism to have clamp-from-top-to-unlatch, replacing the button-press-to-unlatch. This makes the battery usable on fixed-wing platforms, since battery swapping operations interact with the top surface only, as opposed to V1 where side clasping is required. It will also probably make it easier to automate battery swapping via robot arm, due to the simplified interface.

### V2 TO DO LATER
* Replace balance wires with a BMS
* Extend battery configurations to 24s12p
