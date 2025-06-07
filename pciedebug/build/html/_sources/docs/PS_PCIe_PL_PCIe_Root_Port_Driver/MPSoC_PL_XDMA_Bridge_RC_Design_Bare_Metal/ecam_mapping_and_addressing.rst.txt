ECAM Mapping and Addressing
===========================

ECAM via S_AXI_Lite Interface
-----------------------------

The ECAM (Enhanced Configuration Access Mechanism) for the **XDMA PL PCIe IP** is accessed via the `S_AXI_Lite` interface.

The ECAM address for the XDMA PL PCIe IP is set at `0xA000_0000`, as shown in the figure below. This address space begins with the **Root Complex (RC) ECAM**, followed by the **Endpoint (EP) ECAM**.

.. image:: ecam_mapping_and_addressing/img_1.png

If there are 256 buses, a total of **256 MB** of ECAM space is required.  
Each device on the bus requires **1 MB**.  
To allocate space for one RC and one EP, **at least 2 MB** of ECAM is required.

xparameters.h Configuration
---------------------------

The ECAM base address is also defined in the `xparameters.h` file, as shown in the figure below.  
This value should match the `S_AXI_LITE` base address of the XDMA PL PCIe IP as indicated in the **Address Editor** shown in the previous figure.

.. image:: ecam_mapping_and_addressing/img_2.png
