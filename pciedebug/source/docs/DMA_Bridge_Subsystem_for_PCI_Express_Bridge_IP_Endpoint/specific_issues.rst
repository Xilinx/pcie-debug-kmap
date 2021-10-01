.. _dma_bridge_ip_specific_issues:

Specific Issues 
================

* Data trasfer issue
    - Probe the M_AXI and S_AXI interfaces (depending on the direction of the data flow you are debugging) and see if you can trigger / catch the request you have made.
    - If the packet shows up in here, check the address field (ARADDR for Read Request or AWADDR for Write Request) to ensure that the address is in the expected AXI Address (after translation) for this request.
    - AXI Translation vector is at C_PCIEBAR2AXIBAR_# parameter and C_PCIEBAR2AXIBAR_# parameter. AXI BAR settings are at: C_AXIBAR_# parameter
    - If the M_AXI and S_AXI interfaces do not show anything but this is not the first AXI packet prior to the failure, then check previous AXI transactions and make sure that they are completed.
        - For Writes you must see a corresponding BRESP, BVALID, and BREADY for each request.
        - For Reads you must see a corresponding RRESP, RVALID, and RREADY for each request.
        - If there is any packet that is not completed (check both the Writes and Reads interface), investigate those first before moving forward, because PCIe has a strict packet ordering rule and that pending request might have halted the data pipeline.
    - If the M_AXI and S_AXI interfaces do not show anything and this is the first AXI packet prior to the failure (or no other pending transaction), then check the following:
        - The Bridge Enable bit in the Bridge Control Register (Offset 0x148 on s_axil_* AXI Lite interface) must be set to 1 before any data can flow through
        - Check the AXIS_TX/RX interface and see if you can spot the packet there.
        - If it is visible in the AXIS* interface, then the problem is in the Bridge.
        - For AXI MM Gen2, internal state machines can be seen by following the AXIS_* signals datapath