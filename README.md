This repository provides an updated set of KiCad schematics of the CERN VME White Rabbit VME board.
a 73 page hierarchical design, the fault coverage and boundary-scan capabilities are discusse dand reported,
It now has test points inserted.
Sections from the tomachie report are included to help understand the design overview,
how 1) the power and regulators work, 2) HSSI, 3) LSSI, 4) Memory, 5) ESD and EMC compliance.
A few areas to observe is the TEN line on the DDR4 memories is tied to GND and they both should go to a 
single test point with pull-down, or since there is unused FPGA GPIO, the TEN pins should be brought to the 
FPGA I/O and used with a pull-down to GND.  When boundary-scan interconnect is desired to the DDR4, then
the FPGA can put the DDR into test mode and perform interconnect tests at relatively slow speeds.  Without it,
boundary-scan can turn-off on-chip DDR4 DLL/PLLs and slow speed write and read to the DDR4, just it is more timing
dependent and diagnostics when control lines are stuck is harder than when using TEN.

The report score is low, 60 out of 100 but that is mostly due to less attention to volume manufacturing issues and DFT 
practices, and a few other issues in schematic quality.
The biggest design issue is that the design intent is not properly captured where the schematic shows SFP+ 
connector shells going to logic GND.  and they should be seperated to Chassis GND and then connected with zero ohm resistor
or inductor/cap network.    The layout is probably managing the cage shields properly but leaving the intent out of the
schematic and using side channels to communicate design intent is not good practice.
There are a few flaws such as impedance path gaps, which can affect layout or automated layou so controlled impedance
gaps can be a problem depending on who is doing the layout.  There are a few places (see report) where the AC coupling of caps
are marginal for PCIe.

The full PCB Design Review is here:
https://tomachie.com/r/324131b6-a699-45a8-ac61-7f713f6410b7/vme-wren_report.html?GH

A representitive description is here below:     The VME-WREN is a VME64x-format FPGA processing board built around a Zynq UltraScale+ MPSoC (IC14, XCZU4CG-1SFVC784E). The design is a hierarchical schematic spanning 73 sheets with 1580 components, 8220 pins, and 1337 nets. The board receives power from the VME backplane via P12V and P5V_VME rails and generates all local supply voltages on-board. It provides extensive I/O buffering for VME bus transactions, high-speed serial links via SFP+ and Mini-SAS HD connectors, a PS-side DDR4 memory subsystem, dual QSPI flash for configuration storage, USB debug access, precision clocking, and a DAC output channel. The board is intended for data acquisition, processing, or I/O control applications in a VME crate environment.


Central Processing Element

IC14 is an XCZU4CG-1SFVC784E, a Zynq UltraScale+ MPSoC CG variant in the 784-FCBGA package. GTH transceivers in the SFVC784 package support data rates up to 12.5 Gb/s. The XCZU4CG in the SFVC784 package provides 4 PS-GTR transceivers and 4 GTH transceivers. The device uses PL I/O banks 43, 44, 45, 46, 64, 65, and 66, plus HD banks, and the PS MIO pins are extensively allocated across PS banks for DDR4, QSPI, UART, I2C, JTAG, and GPIO functions. The PS and the PL are on separate power domains, enabling users to power down the PL for power management if required.


Power Supply Architecture

The board employs eight power-conversion devices to generate the required voltage rails from the backplane P12V and P5V_VME inputs.

IC40 (LMZ31704RVQT) is a 4 A power module combining a DC/DC converter with power MOSFETs, a shielded inductor, and passives into a low-profile QFN package. IC40 generates the P3V3 rail (600 pins) and the filtered P3V3_CLK rail (40 pins, via inductors L13 and L14). P3V3 is the dominant rail on the board, powering the majority of the I/O buffer logic.

IC32 (TPS62125DSG) is a 3 V to 17 V, 300 mA step-down converter that produces the 5VREG rail (20 pins) via inductor L1. This intermediate rail feeds downstream regulators.

IC30 and IC31 are both IRPS5401MTRPBF flexible power management units, each delivering up to 5 output voltages to processors, FPGAs, and other multi-rail power systems. The IRPS5401 is a digitally configurable flexible power management unit with an I2C/PMBus interface, supporting up to 5 rails with 4 independent switching regulators and one linear regulator. IC30 generates P1V8 (201 pins, via PHASE_C through L11) and VCC_PSPLL (8 pins, via VO_LDO). IC31 generates MGT_0V85 (6 pins, via VO_LDO), MGT_0V9 (10 pins, via PHASE_D through L18), MGT_1V2 (12 pins, via PHASE_A through L15), MGT_1V8 (16 pins, via PHASE_B through L16), and P1V2 (85 pins, via PHASE_C through L17). Both IC30 and IC31 are accessible on I2C at addresses 0x14 and 0x15 respectively, via connector J3.

IC29 (TDA21535AUMA1) contains an improved high-speed MOSFET driver optimized to drive a pair of co-packaged high-side and low-side OptiMOS MOSFETs at frequencies up to 1.5 MHz. IC29 generates the P0V85 rail (63 pins) via inductor L9. This rail supplies VCCINT and related core voltages for IC14.

IC22 (TPS74801DRCT) is a 0.8 V to 3.6 V, 1.5 A low-dropout linear regulator producing the P2V5 rail (12 pins). This rail serves the precision voltage reference and DAC subsystem.

IC24 (TPS51200DRCT) is a DDR termination regulator generating the VTT_DDR4-PS rail (37 pins) at half the DDR4 VDDQ voltage, providing the mid-point termination voltage for the PS-side DDR4 interface.

IC12 (REF5030AID) is a 3 V precision voltage reference providing a stable reference for the DAC subsystem (IC10, DAC8562SDSC).

See the link for the PCB Design Review as there are a couple of power-supply marginal choices such as bootstrap cap C270 is marginal, on the lower end of the requirement at 220nf and LMZ31704 delivers 4A maximum and quiescent current of FPGA and logic comes in around 3A.  The main report was generated by Tomachie — try it on your own KiCad project at https://tomachie.com 
