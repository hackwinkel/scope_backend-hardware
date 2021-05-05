# 100Ms/s dual channel 16 bitdigital oscilloscope and function generator back-end with ethernet 


This repository contains the schematics and PCB design files.

* Main features *
- >=100MHz sample rate
- 2 channels, 16 bits A/D
- 4 channels, 14 bits D/A
- 256 Mbyte ddr3 ram
- Gigabit Ethernet

* General description *
Please reference the hierarchical schematic diaram provided:

Main (contains various digital power supplies and references to subsheets)
-- FPGA (contains the FPGA with its power connections, JTAG and signal definitions)
-- DACS (contains the D / A convertors with analogue power supplies and output buffers)
-- ADCS (contains the A/D convertors with analogue power supplies and input buffers)
-- ddr3 (contains memory with terminations)
-- GNET (contains Gbit ethernet PHY with magnetics and support components)

Power supplies
All power supplies are standard LDO regulators, stepping down the voltages from a main 5V connections. To limit dissipation in each LDO, multiple 3.3V, 2.5V, 1.5V and 1.2V subnets are used, and the lower voltages are stepped down via an intermediary 3.3V stage.
The only exception is an optional switched mode power supply module providing +9V/-9V for analogue input/output buffers. This module is optional if the analogue front-end is used, as this also provides these voltages.

FPGA
This project uses a SPARTAN 6 Field Programmable Logic Array (XC6SLX16 FPLA) at its heart. This FPLA device digitallt connects all components, and allows this project to be flexibly reconfigured for various purposes. Core timing is provided by a 100MHz integrated crystal oscillator and a 125MHz clock drived from the ethernet PHY 

DACS
Digital-to-Analogue conversion is performed by 4 DAC904 165 Msample/s 14 bit D/A convertors. Each of these D/A convertors has its own 3.3V power supply. The DAC904 produces a differential output signal, which is converted and buffered to a single ended signal using an OPA695 operational amplifier.

ADCS
Analogue-to-Digital conversion is performed by 2 LTC2208 130 Msample/s 16 bit A/D convertors. Each of these A/D convertors has its own 3.3V power supply. For each A/D convertor an AD8138 diferential amplifier provides the requires a buffered differential analogue drive signal. These AD8138 are by default configured to convert a single ended signal to a differential signal.

ddr3
A single MT41K128M16 ddr3 RAM chip provides 128M x 16 memory. This has to be a 1.5V compatible device as the SPARTAN 6 FPGA does not support 1.35V memory. Single ended, single direction lines (address and some control signals) are unterminated as this apparently "just works"-albeit probably not at maximum speed. Bidirectional signals are dynamically terminated on the chip. Simple resistive termination is provided on some differential clocking signals. A reference voltage is generated using a resistive divider. All traces on the PCB are length-matched to withn 0.5mm

GNET
A KSZ9031 Gigabit ethernet PHY with Reduced Gigabit Media Independent Interface (RGMII) provides a gigabit ethernet connection through a gigabit magjack. The local 25Mhz crystal oscillator is converted to a 125MHz clock signal, which is also connected to the FPGA as an alternate timing source


*This project is currently under construction*
Most notably, VHDL/Verilog code to program the FPGA is required and an analogue front-end.


IMPORTANT NOTE FOR PCB FABRICATION

Various PCB houses have various design constraints. JLCPCB has some awkward design rule, requiring a larger space between via-pads and adjacent traces of different nets than between two traces of different nets. KiCAD only allows you to spacify *one* spacing for both. In order to accomodate this, the PCB has been designed using 0.508mm vias with 0.2mm holes. Prior to exporting to gerbers, these via pads need to be made smaller in order to compy with these design rules:

Pcbnew -> Setup -> Design rules, change "Via Dia" to 0.45mm
Pcbnew -> Edit -> Edit All Tracks and Vias -> Set all vias (no tracks) to their Netclass value, and OK

EXPORT GERBERS, DO NOT SAVE THE CHANGED PCB DESIGN FILES as next time you change anything the smaller vias will lead to planes and traces coming too close to the via.


