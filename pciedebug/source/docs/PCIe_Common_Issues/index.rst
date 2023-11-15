.. _common_issues:

==================
PCIe Common Issues
==================
.. _enumeration_no_pcie_device:

Enumeration shows no PCIe device (lspci)
----------------------------------------
    - Check using ILA if the cfg_ltssm_state signal shows an L0 state ('h10). 
    - If in the L0 state, check if it consistently stays in the L0 state or is going through recovery state continuously. 
    - Repeatedly going through the recovery state indicates a link integrity issue.
        - If it is stable in L0 state, check if PCIe Config Request TLP's are exchanged and that each Completion TLP is returned. 
        - This is part of the PCI enumeration process and must be done within 100ms of the Power Good indication. 
        - Try doing a warm reboot and if the device is now detected after a warm reboot, this requirement was violated. 
        - Certain server systems might not do another PCIe discovery after a PCIe slot/device failure, requiring a Cold Boot (power cycle) to recover.
        - If the cfg_ltssm_state signal shows state 00 indefinitely,ensure that cfg_link_training_enable input pin is driven to 1'b1. 
        - Check using AXI JTAG if the GT reset FSM has completed and is back to 00 state. If so, check if the phy_status_rst pin is connected to the PCIe reset_done pin.

.. _missing_dma_data:

Missing DMA read data for certain read requests
-----------------------------------------------
    - Cross check the read/write request size against the max payload size register in the config space of the endpoint, as there is a chance of the host machine not scanning the config register correctly.
        - The issue was see when programming the device on board first and then placing the board in the PCIe slot and rescanning by the host PCIe bus. 
        - Host bridge did not enumerate the EP config registers properly.

.. _missing_payload:

Missing payload in TLP
----------------------
    Make sure the control signals (tready, tvalid, tlast) are correctly driven from the user application.

.. _unsupported_requests_timeout:

Unsupported Requests/Completion Timeout
---------------------------------------
    Use the command "lspci -vvv -d [Vendor ID]:[Device ID]" and check to ensure that the BAR exists:
    
    The list should have something similar to the following (or more than one if you have multiple BARs):
    
    Region 0: Memory at <address>.
    
    Make sure that this line does not have words such as disabled or virtual.
    
    .. note::
        Virtual means that you have lost the BAR information at the Endpoint card, but that it was previously visible. This can be caused by an unexpected link down.
        
        Disabled means that the Memory Enable bit at the PCIe Command register is not set. Set them through your driver or using the setpci utility.
        
    
    If the BAR does not show up at all, but lspci does indicate that your device is detected, then your system might be running out of memory during memory allocation.
    This can be caused by large PCIe BARs in your design. Use the pci=realloc directive in the Kernel to re-map your MMIO or use 64-bit BAR instead of 32-bit BAR.
    Typically this is caused by Missing BAR information or the Command Register (Memory Enable bit) not being set.

Receiver Overflow
-----------------
    - Check whether the lspci AER RxOF+ is set
    - Ensure that the system sets the relaxed ordering bit in the PCIe config space. If not, it might cause an overflow
    - Check if sufficient credits have been assigned
