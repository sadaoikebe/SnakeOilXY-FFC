# FFC mod for SnakeOilXY 3-D printer

These boards are for SnakeOil-XY 3-D printer. It makes connection to the toolhead simpler.
It consists of two PCBs: MCU board and tool head board. This system is compatible only with Klipper.

The MCU board needs to be connected to the Raspberry Pi via USB and supplied with 24V power, and the TMC2209 driver needs to be inserted.
A 1.0mm pitch 26-pin 20624 FFC cable is used.

![block diagram](blockdiagram.drawio.svg)

In the future, I may make it possible to connect via CAN bus so that it can work with RRF, etc.

* Printer.cfg

It assumes multi-mcu functionality of klipper firmware. Printer.cfg needs to be modified like this:

[Printer.cfg modification](klipper/printer_cfg_modification.txt)

![boards](boards.jpeg)
![toolhead](toolhead-installed.jpeg)

* Connection of the heater

The heater is assumed to be a 24V 50W, using 4 FFC cable contacts in parallel. Approx. 500mA current flows per contact.
Resistance of the heater wire was 266mΩ round trip - with a 600mm cable - which is measured by four-terminal-pair method of Agilent E4980A. 
When a 24V 50W heater is used, there is a loss of about 1W including circuits, cable and contacts. (225mΩ for 53cm of lead wire, 157mΩ for 14cm of lead wire, so the loss in the circuit and FFC is 133mΩ. 266mΩ round trip. This resistance value includes the resistance of four contacts on the fixture side.)

![resistance-14cm](resistance-14cm.jpeg)
![resistance-53cm](resistance-53cm.jpeg)

* Defects

In r0.1, the Drain and Source connections of the MOSFETs of the heater were reversed, so I changed the connections.
Also, the clearance between the power supply/heater terminals and other parts are not enough. It needs to be changed to increase the clearance.
