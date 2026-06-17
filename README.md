# IP6525T-USB-charger-hacking

## What is this?
This repo contains some notes and a reversed schematic about some low voltage DC USB charger modules for phones bought on Aliexpress some time ago. It might be useful to somebody working with these modules.

## Licence and disclaimer
The content of this repo is provided under CC BY-NC-SA 4.0 and WITHOUT ANY WARRANTY!

## The modules
They were sold as "QC3.0 A pple Hua wei MTK Sam sung DIY Fast Charger DC 5-32V 24W Step-down Module" (with the extra whitespaces) and are based on the [IP6525T](https://www.injoinic.com/api//static/uploads/20250529/20250529105517_6837cc959634d.pdf) that is a "18W output step-down converter with fast charge protocols" from Injoinic Technology.

## Versions
It looks like there are (at least) 2 versions of the same module:
1)heatsink fixed with adhesive tape, marking "V1872" on backside of PCB, heatshrink around the inductor
2)heatsink glued on with silicone, marking "HW-366A/B" on topside of PCB, no heatshrink

The components look identical (although i did not check the values, especially of the MLCC and the inductor), except for the marking and manufacturer logo of the diode (inside the red circle on schematic below) near the USB-A connector:
1) marking "BE", logo might be Vishay??
![picture of diode for version 1](/images/diode_v1.jpg)

3) marking "BV" - no picture at this point

## Pictures
![picture 1 of modules](/images/modules1.jpg) ![picture 2 of modules](/images/modules2.jpg) ![picture 3 of modules](/images/modules3.jpg) ![picture 4 of modules](/images/modules4.jpg)

## Schematic 
I reversed the version 1 (where the heatsink is fixed with adhesive tape).
![reversed schematic](/images/schematic_v1.png)

## Odd stuff
### The diode
There is a (unidentified) diode that i talked about earlier used as a freewheel-diode on the output side, however the datasheet does not show one in the typical application schematic. I guess the (needed) diode is integrated inside the IC but the person who designed those modules did not know this or wanted to be extra safe? 

### The MOSFET
As you can see on the reversed schematic the modules do contain some circuitry based around a 4410 N-Channel MOSFET. I would guess this is an extra safety to cut power if the polyfuse triggers?

## Hacking
### Adding a shunt for output current measurement
I wanted to be able to measure the output current, so i had to somehow add a shunt resistor just before the USB-A connector. This was really tricky because there is no trace you can cut directly. I finally cut the 5V pin of the USB connector, bent it outside and added a 0603 10mΩ shunt between the bent out pin and the capacitor on the board. Don't forget the wires to your measurement device! The value of the shunt is a tradeoff between voltage drop and precision as there is no possibility to make a proper 4 way connection for the measurement here.  
I am afraid my camera did a really poor job with the picture, but i only have this one:
![added shunt resistor](/images/shunt.jpg)

### Removing the MOSFET circuitry
As i don't really see the point of it and it increases losses due to the internal resistance of the MOSFET i just removed all this circuitry. Obviously you have to solder the GND wire of the supply voltage where the drain of the MOSFET was. I also removed the polyfuse as my power supply has a protection, otherwise you should keep it.
![picture of modded board](/images/finished.jpg)

## Measurements
*to be done later...*
