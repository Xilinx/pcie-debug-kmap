.. _ps_pcie_pl_pcie_driver_debug_checklist:

General Debug Checklist
=======================

    * PS-PCIe Driver Debug Checklist
            *   The PCI Express Controller Programing Model section in UG1085 summarizes programming of the PCI Express controller for Endpoint and Root Port mode operations. Review that section to make sure programming of the PS-GT Transceiver Interface, IOU for Reset Pin, PCI Express Controller and Bridge initialization has been done correctly.
            *   Try with the latest version of PetaLinux.
            *   Make sure that the correct version of the BSP is being used for a particular board and silicon revision if the design being run is based on BSP and not on template flow.
            *   For specific user design, it is recommended to configure and build a PetaLinux project from the HDF file generated from that design.
            *   Confirm that the correct driver and DTS are being used
            *   Test with an off the shelf card (for example, NIC as an endpoint)
            *   When debugging a user design, it is helpful to compare register values with the example design for a development board such as the ZCU102. The command below can be used to read a range of registers:
                    *   xsct% mrd 0xFD480000 100
            * If the BARs are not assigned, check if the device is advertising a valid class code. If an incorrect class code is provided when configuring Root Port mode in PCW, BAR assignment fails during Linux enumeration.
    * PL-PCIe Driver Debug Checklist
        *  When setting up a Zynq UltraScale+ MPSoC system for PetaLinux with XDMA PL-PCIe (Bridge Mode) in Root Port configuration, there are a number of settings and options that should be used to experience seamless interoperability of the System, IP, and the PetaLinux Driver (pcie-xdma-pl). (https://www.xilinx.com/support/answers/70854.html) provides details on the following topics.
              *  AXI BAR Translation Registers for correct DTS enumeration
              *  Endpoint Devices with Non-Prefetch BARs
              *  AXI-Lite Control Bus on AXI Bridge
              *  Root Port PCI Express BAR Disable
    * Common Checklist
        * Review: PS/PL PCIe Drivers - Release Notes (https://www.xilinx.com/support/answers/70702.html)
        * Review Xilinx PCI Express (PS-PCIe/PL-PCIe) Drivers Debug Guide (https://www.xilinx.com/support/answers/71210.html)
        * Unlike x86 platforms where the BIOS enables Memory space (MemEn) in the device configuration space, embedded platforms do not enable MemEn bits during enumeration. An Endpoint driver with relevant PCIe API’s is required for this. To check BAR access via devmem, you should set MemEn via the “setpci” command as shown below. setpci is a utility for querying and configuring PCI devices.
            * setpci -s 01:00.1 COMMAND=0x7 
        * If an endpoint is connected and if the link is established successfully, the log file reports ‘Link is UP’. Confirm that the link is UP before proceeding with further investigation. 
        * Endpoint functionality can be checked with endpoint drivers. The respective endpoint driver must be enabled in the kernel build.
        * If the device is not deteced in lspci, try by rescanning as shown below.
            * echo 1 > /sys/bus/pci/devices/0000\:00\:00.0/remove
            * echo 1 > /sys/bus/pci/rescan
        
        

.. .. _dma_bridge_rootport_faqs:

.. General FAQs
.. ============
