## Install Klipper on CR-10S

[Klipper Documentation](https://www.klipper3d.org/Overview.html)<br>
[Klipper configs at Github](https://github.com/Klipper3d/klipper/tree/master/config)<br>
[My working config with BLTouch](./printer.cfg){target="_blank" rel="stylesheet" type="text/css"}<br>
[Klipper cartesian printer example cfg](./example-cartesian.cfg)<br>
[Generic ramps cfg](./generic-ramps.cfg)<br>
[Klipper Cr10S cfg](./printer-creality-cr10s-2017.cfg)<br>
[Sample lcd cfg](./sample-lcd.cfg)<br>
[Sample aliases cfg](./sample-aliases.cfg) Contains information mapping arduino atmega pins to Klipper pins (arx -> Py)

Used the Klipper Cr10s file as a starting point, 

- substituted the LCD portion from sample-lcd.config,
- added bed screws and bed-levelling sections.
- Added start print and end print gcode sections from sample-macros.cfg in Klipper configs.
- **NOTE: not working** Added heater_fan section based on  [this](https://klipper.discourse.group/t/starting-the-nozzle-fan-when-nozzle-is-heated-above-50-c/10487) and [this](https://www.klipper3d.org/Config_Reference.html#heater_fan). The goal was to have the part cooling fan oinly run when the heater was above a certain temperature.

## TODO
- Probe print region before print
- Clean nozzle before print
- Exclude object section in printer.cfg



## printer.cfg

[Ramps board pinout diagram](./800px-Arduinomega1-4connectors.png)<br>
[Ramps 1.4 wiki page](https://reprap.org/wiki/RAMPS_1.4)<br>
[LCD wiki page](https://reprap.org/wiki/RepRapDiscount_Full_Graphic_Smart_Controller)

[Marlin CR10S config](./Configuration.h)<br>
[Marlin Creality Ramps pins file](./pins_RAMPS_CREALITY.h)<br>
[Marlin Ramps pins file](./pins_RAMPS.h)

[Marlin settings](./settings.txt)

## Videos

[Video](https://www.youtube.com/watch?v=i_541iD5Bj0) showing how to create a printer.cfg from scratch. Good tips about getting certain settings from Marlin.


## Odds and ends
- Load bed mesh before each print - add to gcode: `BED_MESH_PROFILE load=default`