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

General Debug Questions and Checklist
=====================================

Regression
----------
  
    - Verify whether the issue occurs in earlier versions of Vivado.
    - Check if other link width configurations exhibit similar behavior.
    - Test using a Gen1x1 configuration.
    - Test on a different hardware board.
    - Test with a different link partner device.

Clocking
--------

    - Check if the clock is locking.
    - Verify whether the issue occurs only with the asynchronous clocking architecture.
    - Test by disabling SSC (Spread Spectrum Clocking).

Design Implementation
---------------------

    - Check for any implementation errors (synthesis or routing).
    - Check for any timing errors.
    - Verify that all IP constraints have been validated against the Example Design XDC file.

Failing Behavior
----------------
  
    - Note the frequency of the error (e.g., occurs immediately or after a period such as 1 hour).
    - Check whether the error can be cleared, and if so, whether it reappears afterward.
    - Verify if the failure is observed across multiple parts.
    - Check if the failure occurs immediately after reset.
    - Observe whether the failure occurs immediately, after the first rate change, or after multiple rate changes.
    - Measure the time between a successful rate change and the occurrence of failure.
    - Check whether the issue occurs with the Example Design or only with custom design.

Debug Capability
----------------

    - Check using a protocol link analyzer.
    - Probe the clock, power, and data lines with a high-speed oscilloscope.
    - Check whether you can insert a clean clock in place of the on-board reference clock.
    - Capture and review the LTSSM graph by enabling the JTAG Debugger or PCIe Link Debug feature (depending on the device used) in the GUI.

SI Debug Info
-------------

    - Confirm that clock jitter and power noise are within specification.
    - Perform and review power integrity measurements.
    - Perform and review REFCLK jitter measurements.
    - Collect and analyze channel loss data.
    - Capture and review Eye Scan plots.
    - Confirm whether DFE, LPM, or AutoRxEq is selected in the core configuration.

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
