.. _link_training_issue_reasons:

Common Link Training Issue Reasons
===================================

    - Unable to retain L0, going to recovery 
    - Incorrect Pinouts – Clock, GTs, Reset     
    - Lane is reversed and neither EP or RP can do lane reversal 
    - BAR is too big or wrong type – Host run out of contiguous memory space 
    - Link is disabled by Host – maybe missed enumeration time, driver directed to this, surprise link down, etc. 
    - VM issue – device not assigned to proper IOMMU for VM use 
    - Host is bifurcated – Card doesn’t match Host and vice versa 
    - Link width/speed not supported by Host slot 
    - Power issue on the card 
    - Wrong Vendor / Device ID 
    - Invalid capability pointers or extended config not terminated 
    - Incorrect / bad Tandem image 
    - Timing failures / Wrong constraints 
    - Incorrect IP use case – no credits, stalled/throttled data pipelines, cfg_space not enabled, etc. 
    - Existing Errata with the link partner 
    - Incorrect BIOS revision 
  
.. _link_training_debug_questions:

General Debug Questions
=======================

Regression
----------
  
    - Check whether the issue occurs in previous Vivado versions
    - Check whether other link width configurations show similar behavior
    - Test with a Gen1x1 configuration
    - Test on a different board
    - Test on a different machine

System Configuration
--------------------

    - Indicate what board you are using: is it a Xilinx Development Board or a Customer Board? If it is a Xilinx development board, please provide the board revision ID. Indicate motherboard description:
    - What is the link partner? Is it a switch, or a PC? Who is the manufacturer of that switch or PC? - Which Chipset are you using?
    - Did the failure occur in Gen1, Gen2, and/or Gen3?
    - Did the failure occur as RP (Root Port) and/or EP (Endpoint)?
    - Describe the channel between the FPGA and the link partner, and estimated channel loss at desired link speed. 
    - Is there any passive hardware (Interposer for analyzer, retimer) in the path?
    - How is the Xilinx part connected to the system / link partner (Chip-to-chip on single board, add-in card, backplane, cabling) ?

Regression
----------

    - Did the issue occur in previous Vivado versions too? 
    - Do other link width configurations show similar behavior?
    - Have you tried with Gen1x1 configuration?
    - Do you have a different board that you could try on? If you do, do you see the same issue on that board?
    - Have you tried on a different machine?

Clocking
--------

    - Did the clock lock?
    - What is the clocking architecture? Synchronous or Asynchronous?
    - Is SSC enabled?
    - Have you checked by disabling SSC?
    - What frequency are you using for the reference clock?

Design Implementation
---------------------

    - Were there any implementation (synthesis, routing) errors?
    - Were there any timing errors?
    - Have all of the IP constraints been verified by comparing with the Example Design XDC file? 

Failing Behavior
----------------
  
    - What is the frequency of the error? For example, does it happen immediately or after 1 hour?
    - Can the error be cleared? If cleared, does the error come back?
    - Is this failure observed on multiple parts?
    - Does failure occur immediately after reset?
    - Does failure occur immediately, after first rate change, after multiple rate changes?
    - How long after successful rate change does it fail?
    - Does the issue occur with the Example Design as well or only in your design?

Debug Capability
----------------

    - Was a protocol link analyzer used? If so, provide protocol link analyzer captures with the details of your analysis of the captures.
    - Do you have a high-speed oscilloscope available, to probe clock / power / data lines?
    - Do you have a free-running clock available to the FPGA?  (A clock that is separate from the PCIe reference clock, and not tied to the PCIe reset)
    - Do you have the ability to insert a clean clock in place of the on-board reference clock? 
    - Have you captured an LTSSM graph by enabling the JTAG Debugger feature in the GUI?
    - Have you run Eye Scan by enabling the In-system IBERT feature in the GUI?

SI Debug Info
-------------

    - Has it been confirmed whether Clock Jitter and Power Noise are within the specification?
    - Power integrity measurements
    - REFCLK jitter measurements
    - Channel loss data
    - Eye Scan plots
    - Confirm whether DFE, LPM or AutoRxEq are selected in the core configuration.

.. _link_training_debug_checklist:

General Debug Checklist
=======================

    • Check with the example design that comes with the generation of the IP. 
    • Try Gen1x1 configuration
    • Instead of regenerating the core, tape off the lanes if possible (See: https://www.xilinx.com/support/answers/38988.html)
    • Check with a different host system if possible.
    • Check with a different card if possible.
    • Check Integrated EoU Debug Features:
        • https://forums.xilinx.com/t5/Design-and-Debug-Techniques-Blog/Debugging-PCI-Express-Link-Training-Issues-with-Integrated/ba-p/1097525 
        • https://forums.xilinx.com/t5/Design-and-Debug-Techniques-Blog/Debugging-Versal-ACAP-Integrated-Block-for-PCIe-Express-link/ba-p/1203707
    • Check Board Design Guidelines in GTH/GTY User Guide
        • https://www.xilinx.com/support/documentation/user_guides/ug576-ultrascale-gth-transceivers.pdf 
        • https://www.xilinx.com/support/documentation/user_guides/ug578-ultrascale-gty-transceivers.pdf 
    * Check the link training debug guide in the following links:
        * https://www.xilinx.com/support/answers/56616.html
        * https://www.xilinx.com/support/answers/73361.html
    • Check ltssm status in Vivado ILA; track the ltssm transition by using store_ltssm signal 
        • (See: https://www.xilinx.com/support/answers/71355.html )
        • Run example design simulation for the IP configuration of your design. 
        • Observe ltssm transition in example design simulation. Compare this with the ltssm transition in Vivado ILA. If the expected transition is not happening, identify the erroneous ltssm state transition and consult PCIe specification to understand why such transition is occurring. 
    • Signal Integrity Check:
        • As defined in the specification, it is required to put AC coupling capacitors at the transmitter lanes differential signal pair. The value of AC coupling capacitor is between 75 nF and 200 nF. The user should make sure that the PCI express card has an AC coupling capacitor placed in the close proximity of the transmitter lane. Check if the correct AC capacitor value has been put in place or not. There might be a possibility for a cracked capacitor.
        • Review the reference clock data sheet and/or hardware measurement of reference clock, confirm the phase noise specification is below the phase noise mask of the target GT.
        • Confirm the hardware has taken care of “GT Reference Clock Checklist” specified in the respective GT user guide.
        • Ensure Reference clock hardware schematic is as per the requirement of the IO standard of the clock
        • Review the power supply decoupling capacitors on the board and make sure it is as per the guidelines mentioned in GT user guide
        • Check the power supply noise measurement and confirm that peak-to-peak noise as measured at the input pin is according to the value specified in the corresponding GT user guide. 
        • Confirm that Termination Resistor Calibration Circuit on the board is as per the reference circuit in the corresponding GT user guide and layout guidelines from the user guide are followed.
        • Fulfill the oscillator vendor's requirement regarding power supply, board layout, and noise specification.
        • Provide AC coupling between the oscillator output pins and the dedicated clock input pins.
        • Meet or exceed the reference clock characteristics as specified in the corresponding datasheets of the device used. 
        • Provide a dedicated point-to-point connection between the oscillator and clock input pins.
        • Keep impedance discontinuities on the differential transmission lines to a minimum (impedance discontinuities generate jitter).
    • For Gen3 Link Training Issues:
        • Check by setting 'Enable Auto RxEq' option to 'True' in the IP Configuration GUI if it is available for the device being used.
        • Sometimes the issue may be related to CPLL vs QPLL. The IP Configuration GUI allows to select PLL option for Gen2 mode only. If the link training issue is related to Gen3, check by configuring the IP as Gen2 by selecting QPLL option. 
        • Check by selecting 'Link Partner TX Preset' value to '5'. The default value is 4; this can be selected in the IP configuration GUI. 
        • Check if the Link Status 2 register in the PCIe Configuration Space to see if Link Equalization phases were attempted.
        • Try bypassing Phase2/3
        • Use the PCIe PIPE descrambler module in Xilinx PCIe MAC to check for lane-to-lane skew at Gen3 speed. See: https://forums.xilinx.com/t5/Design-and-Debug-Techniques-Blog/Demystifying-PIPE-interface-packets-using-the-in-built/ba-p/980246
    • If Third party MAC is used, try using the Xilinx example design first to rule out any board or setup issues. The most common issue is the MAC-GT integration issue, please review all connections on mandatory ports as stated in (PG239).
    • Check if it is possible to change TX drive parameters on the host.
    • Check if PERSIST is enabled as a Bitstream setting. This option is not supported for non-tandem designs while using SPI/BPS Flash and has been known to cause link training issues.
    • Ensure that there is no skew between lanes on the board 

.. note::
    
    The list above provides a general checklist for debugging link training issues across all devices.
