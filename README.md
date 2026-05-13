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

The Tomachie score is low, 60 out of 100 but that is mostly due to less attention to volume manufacturing issues and DFT 
practices.  The biggest design issue is that the design intent is not properly captured where the schematic shows SFP+ 
connector shells going to logic GND.  and they should be seperated to CHassis GND and then connected with zero ohm resistor
or inductor/cap network.    The layout is probably managing the cage shields properly but leaving direction out of the
schematic and using side channels to communicate design intent is not good practice.
There are a few flaws such as impedance path gaps, which can affect layout or automated layou so controlled impedance
gaps can be a problem depending on who is doing the layout.  There are a few places (see report) where the AC coupling of caps
are marginal for PCIe.
