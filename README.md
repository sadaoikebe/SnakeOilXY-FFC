# FFC mod for SnakeOil-XY 3-D printer

This is a toolhead PCB suite for SnakeOil-XY 3-D printer.
* It's a combo of a toolhead PCB and a dedicated MCU. MCU is connected to the toolhead by a ribbon cable, which makes connection simpler and also makes toolhead lighter.
* It only needs USB wire and 24V power supply connected to the MCU board.
* It has an ADXL345 accelerometer on-board.
* It has an X endstop on board, which fits SnakeOilXY toolhead dimension.
* This system is compatible only with Klipper.

![block diagram](blockdiagram.drawio.svg)

## Appearance

![wiring](images/wiring.jpeg)

## Toolhead

This toolhead PCB can be attached onto adxl_mount to replace the ADXL345 PCB.

It's a toolhead PCB with an ADXL345 accelerometer on-board, and it has an X endstop which fits SnakeOil-XY toolhead dimension.

![Toolhead](images/ffc-toolhead.jpeg)

### Toolhead board assembly

Tricky steps are:

* The two rows of the connectors are close, so two rows of connectors must be installed facing each other. Please be careful of the polarity of the hotend fan and the part cooling fan connector... (red arrow in the picture)
* Heater cables should be soldered. One of the heater terminals is close to the FFC connector, so unfortunately, screw terminal block (KF128 etc..) wouldn't fit. It's still able to disassemble without solder iron - loosening the bolt on the hotend will do the job. (yellow arrow in the picture)
* Bypass capacitor C2 is in the way of the stepper 4-pin connector. The bottom side of the stepper connector needs to be chamfered a little.
* It has Bltouch(servo) and Zmin(probe) connectors in order to use Z probes. Bltouch, inductive probe, or klicky probe can be used. See below diagram for connection.

![Toolhead](images/toolhead-issues.jpeg)

![bltouch connection](bltouch.drawio.svg)

![induction probe connection](induction_probe.drawio.svg)
![fan connection](fan_connectors.drawio.svg)

### Toolhead board installation

It's designed to mount onto adxl_mount, however the X endstop switch is a bit thicker than the adxl_mount. Print and use spacer in the toolhead folder.

The back side of the stepper connector may touch the M3 bolt underneath the toolhead board. In order to avoid the unwanted contact, I recommend to cut the excess wire on the bottom and apply some capton tape to have better insulation.

![Toolhead](images/toolhead-installation.jpeg)

## MCU Board (revision 2)

For ease of connection, this board is dedicated to this FFC configuration. The MCU board just needs to be connected to the Raspberry Pi via USB and supplied with 24V power. A 1.0mm pitch 26-pin 20624 FFC cable is used.

It only supports UART-connected smart drivers (like TMC2209) for stepper motor driver. SPI or direct connections are not supported.

FFC connector can be soldered to either the front or back side of the board. Considering cooling and wiring, it's better to place it on the back side.

This board has schottky diode on it, so 24V probe can be connected to Zmin pin without frying the MCU.

Power => Blue LED

Heater and Fan MOSFETs => Yellow LED

![MCU board](images/ffc-mcu.jpeg)

### C1 capacity is too small
On the rev2 MCU board, the capacitance of C1 is 10uF, which does not stabilize the hotend temperature sensor reading enough. It would be better to add a larger capacitor in parallel or add a smooth_time setting in the [extruder] section.

## Klipper configuration

It assumes multi-mcu functionality of klipper firmware.

[sample printer.cfg](klipper/sample_printer.cfg)

## BOM

* RP2040-Zero
* 26-pin 350mm (180 model) / 450mm (250 model) 20624 FFC cable
* 2x 26-pin vertical thru-hole FFC receptacle
* 2x WSF3085 (MOSFET Heater)
* 2x PL4009 (MOSFET Fan)
* Stepper driver TMC2209 or other (UART connected "smart" drivers are must)
* 2-pin DC Terminal (KF128) for 24V power supply
* TVS diode (SMAJ28A) , schottky diode (1N5819) 40V
* 2x 8-pin socket (for stepper driver)
* Jumper pins
* Capacitors, resistors
* XH2.54 sockets (3x 2pin, 2x 3pin, 1x 4pin)

## Defects

### r0.1

* The Drain and Source connections of the MOSFETs of the heater were reversed --> I changed the connections.
* TVS diode are reversed --> Thankfully JLCPCB engineer fixed it. Need to rotate next time.
* The clearance between the power supply/heater terminals and other parts are not enough --> Need increase the clearance.

## Heater connection

The heater is assumed to be a 24V 50W, using 4 FFC cable contacts in parallel. Approx. 500mA current flows per contact.
Resistance of the heater wire is 266m?? round trip including four screw terminals of 24V power supply and a 600mm FFC cable - which is measured by four-terminal-pair method.
When a 24V 50W heater is used, there is a loss of about 1W including circuits, cable and contacts. (225m?? for 53cm of lead wire, 157m?? for 14cm of lead wire, so the loss in the circuit and FFC is 133m??. 266m?? round trip. This resistance value includes the resistance of four contacts on the fixture)

![resistance-14cm](images/resistance-14cm.jpeg)
![resistance-53cm](images/resistance-53cm.jpeg)

## DC Motor Circuit

The DC motor control circuit uses MOSFETs for low-side switching. Positive terminal of the fan is always connected to the 24V power supply, and the negative terminal is controlled by the MOSFET. This circuit doesn't have a diode to dissipate counter EMF (usually called as a flyback diode). This is because DC brushless motors generally have a full bridge driver in it, and the counter EMF does not return to the primary circuit. With an oscilloscope we can confirm that no counter EMF returns to the primary circuit. Many existing MCU boards (BTT, Mellow, FYSETC...) don't employ such diodes.
![cch477e](images/cch477e.jpeg)
![waveform](images/24vfan_switch_wave.jpeg)

