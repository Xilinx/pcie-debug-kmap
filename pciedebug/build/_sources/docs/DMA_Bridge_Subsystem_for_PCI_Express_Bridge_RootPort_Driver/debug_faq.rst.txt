.. _dma_bridge_rootport_debug_checklist:

General Debug Checklist
=======================

    * PS-PCIe Driver Debug
            *   The PCI Express Controller Programing Model section in UG1085 summarizes programming of the PCI Express controller for Endpoint and Root Port mode operations. Review that section to make sure programming of the PS-GT Transceiver Interface, IOU for Reset Pin, PCI Express Controller and Bridge initialization has been done correctly.
            *   Try with the latest version of PetaLinux.
            *   Make sure that the correct version of the BSP is being used for a particular board and silicon revision if the design being run is based on BSP and not on template flow.
            *   For specific user design, it is recommended to configure and build a PetaLinux project from the HDF file generated from that design.
            *   Confirm that the correct driver and DTS are being used
            *   Test with an off the shelf card (for example, NIC as an endpoint)
            *   When debugging a user design, it is helpful to compare register values with the example design for a development board such as the ZCU102. The command below can be used to read a range of registers:
                    *   xsct% mrd 0xFD480000 100
    * PL-PCIe Driver Debug 
        *  When setting up a Zynq UltraScale+ MPSoC system for PetaLinux with XDMA PL-PCIe (Bridge Mode) in Root Port configuration, there are a number of settings and options that should be used to experience seamless interoperability of the System, IP, and the PetaLinux Driver (pcie-xdma-pl). (Xilinx Answer 70854) provides details on the following topics.
              *  AXI BAR Translation Registers for correct DTS enumeration
              *  Endpoint Devices with Non-Prefetch BARs
              *  AXI-Lite Control Bus on AXI Bridge
              *  Root Port PCI Express BAR Disable

.. .. _dma_bridge_rootport_faqs:

.. General FAQs
.. ============
