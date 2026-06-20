# IP6525T-USB-charger-hacking

## What is this?
This repo contains some notes and a reversed schematic about some low voltage DC USB charger modules for phones bought on Aliexpress some time ago. It might be useful to somebody working with these modules.

## Licence and disclaimer
The content of this repo is provided under CC BY-NC-SA 4.0 and WITHOUT ANY WARRANTY!  
Copyright (c) 2026 by kittennbfive.

## The modules
They were sold as "QC3.0 A pple Hua wei MTK Sam sung DIY Fast Charger DC 5-32V 24W Step-down Module" (with the extra whitespaces) and are based on the [IP6525T](https://www.injoinic.com/api//static/uploads/20250529/20250529105517_6837cc959634d.pdf) that is a "18W output step-down converter with fast charge protocols" from Injoinic Technology.

## Versions
It looks like there are (at least) 2 versions of the same module:
1) heatsink fixed with adhesive tape, marking "V1872" on backside of PCB, heatshrink around the inductor
2) heatsink glued on with silicone, marking "HW-366A/B" on topside of PCB, no heatshrink

The components look identical (although i did not check the values, especially of the MLCC and the inductor), except for the marking and manufacturer logo of the diode (inside the red circle on schematic below) near the USB-A connector:
1) marking "BE", logo might be Vishay??
![picture of diode for version 1](/images/diode_v1.jpg)
2) marking "BV" - no picture at this point

## Pictures
![picture 1 of modules](/images/modules1.jpg) ![picture 2 of modules](/images/modules2.jpg) ![picture 3 of modules](/images/modules3.jpg) ![picture 4 of modules](/images/modules4.jpg)

## Schematic 
I reversed the version 1 (where the heatsink is fixed with adhesive tape).
![reversed schematic](/images/schematic_v1.png)

## Odd stuff
### The diode
There is a (unidentified, anybody?) diode that i talked about earlier used as a freewheel-diode on the output side, however the datasheet does not show one in the typical application schematic. I guess the (needed) diode is integrated inside the IC but the person who designed those modules did not know this or wanted to be extra safe? 

### The MOSFET
As you can see on the reversed schematic the modules do contain some circuitry based around a 4410 N-Channel MOSFET. I would guess this is an extra safety to cut power if the polyfuse triggers?

## Hacking
### Adding output current measurement
I wanted to be able to measure the output current, so i had to somehow add a shunt resistor just before the USB-A connector. This was really tricky because there is no trace you can cut directly. I finally cut the 5V pin of the USB connector, bent it outside and added a 0603 10mΩ shunt between the bent out pin and the capacitor on the board. Don't forget the wires to your measurement device! The value of the shunt is a tradeoff between voltage drop and precision as there is no possibility to make a proper 4 way connection for the measurement here.  
  
I am afraid my camera did a really poor job with the picture, but i only have this one:
![added shunt resistor](/images/shunt.jpg)
Because of the small value of the shunt i used a dedicated high side current measurement IC, the INA210. It is supplied with 5V in my setup and does multiply the small voltage from the shunt by 200. This means that the measurement range (with Vcc=5V, the INA210 can go much higher but the output will be feed into an ADC later) is 0-2,5A which is fine for me.

### Removing the MOSFET circuitry
As i don't really see the point of it and it increases losses due to the internal resistance of the MOSFET i just removed all this circuitry. Obviously you have to solder the GND wire of the supply voltage where the drain of the MOSFET was. I also removed the polyfuse as my power supply has a protection, otherwise you should keep it.
![picture of modded board](/images/finished.jpg)

## Measurements
I did some measurements with the following setup:
- INA210 fed with 5V from a lab supply
- module fed with 12V from a lab supply (voltage assumed constant which is reasonnable, module input current reading assumed perfectly accurate)
- electronic load on the module output, constant current (current reading/setting assumed perfectly accurate)
- digital multimeter on the module output and the output of the INA210

### Data
|output current (A)|output voltage (V)|module efficiency (%)|losses (W)|output error INA210 (%)|
|---|-----|----|----|---|
|0,0|5,154|0,00|0,01|-/-|
|0,1|4,965|88,0|0,07|1,6|
|0,2|4,951|91,7|0,09|1,2|
|0,3|4,936|92,8|0,12|1,4|
|0,4|4,923|92,7|0,15|1,2|
|0,5|4,911|93,0|0,18|1,1|
|0,6|4,906|93,3|0,21|6,6|
|0,7|4,902|93,1|0,25|1,1|
|0,8|4,901|93,1|0,29|1,0|
|0,9|4,896|92,7|0,35|1,0|
|1,0|4,894|92,5|0,40|1,0|
|1,1|4,894|92,3|0,45|1,1|
|1,2|4,894|92,0|0,51|1,0|
|1,3|4,892|91,7|0,58|1,1|
|1,4|4,892|91,3|0,65|1,1|
|1,5|4,890|91,0|0,73|1,1|
|1,6|4,890|90,7|0,80|1,1|
|1,7|4,888|90,3|0,89|1,2|
|1,8|4,886|89,9|0,99|1,1|
|1,9|4,885|89,4|1,10|1,2|
|2,0|4,884|89,2|1,19|1,2|
|2,1|4,883|88,6|1,31|1,3|
|2,2|4,882|88,4|1,42|1,3|
|2,3|4,880|87,9|1,54|1,3|
|2,4|4,879|87,4|1,68|1,4|
|2,5|4,877|87,1|1,81|0,0|

*Note: The high output error for 0,6A output current is probably due to a typo in the (manually) recorded values.*

### Analysis
#### Current measurement
I have to say i am quite impressed, the overall error is less than 2%! And because of the small shunt value the voltage drop on the output is negligeable (25mV max).

#### Efficency
It's not bad either, especially considering that i measured the output voltage on the end of a (short and thick, custom made) USB cable, so the voltage drop from the connector is included in the values above.
