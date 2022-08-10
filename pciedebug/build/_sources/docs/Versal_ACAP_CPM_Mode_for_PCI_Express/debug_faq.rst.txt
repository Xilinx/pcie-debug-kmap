.. _versal_cpm_debug_checklist:

General Debug Checklist
=======================

* Review Versal ACAP PCB Design User Guide (UG863) and Versal ACAP Schematic Review Checklist (XTP546)
    * https://docs.xilinx.com/r/en-US/ug863-versal-pcb-design
    * https://www.xilinx.com/cgi-bin/docs/ctdoc?cid=90f995d8-c517-4adc-a95c-13a8994d6618;d=xtp546-versal-schematic-review-checklist.zip
* Check if the PCIe IP is detected in lspci 
    * Ref: https://forums.xilinx.com/t5/Design-and-Debug-Techniques-Blog/Debugging-PCIe-Issues-using-lspci-and-setpci/ba-p/1148199
* If the PCIe is not detected in lspci, do a warm reboot of the host machine
    * Re-run lspci 
* If the PCIe IP is still not detected, check if the ltssm state is at L0. Enable PCIe Link Debug Feature.
    * Ref: https://forums.xilinx.com/t5/Design-and-Debug-Techniques-Blog/Debugging-Versal-ACAP-Integrated-Block-for-PCIe-Express-link/ba-p/1203707 
        * See "PCIe Link Debug Enablement" section in PG346
* If the ltssm is not in L0 or the ltssm goes to recovery multiple times, check the eye diagram as discussed in the link below.
    * https://forums.xilinx.com/t5/Design-and-Debug-Techniques-Blog/Debugging-Versal-ACAP-Integrated-Block-for-PCIe-Express-link/ba-p/1203707  
* If the eye diagram is not wide enough and the link issues persist, see the document in the following answer record. The document provided in the answer record is for UltraScale+ device but the debug theory applies to Versal ACAP devices as well.
    * https://www.xilinx.com/support/answers/73361.html 
        * Key things to check are: noise in power and clock jitter. Confirm both are as per the spec.
* If the link is up but the packets are not getting returned or written into memory, capture relevant interfaces in Vivado ILA as described in the link below:
    * https://forums.xilinx.com/t5/Design-and-Debug-Techniques-Blog/Debugging-Versal-ACAP-CPM-Mode-for-PCI-Express-Designs-using/ba-p/1218411
* The above design could also be used as an example design.
    *  Start with this design to check if the link and general read/write work or not before doing the debug on the user design.
* Read the status of phy_rdy
    * Ref: https://www.xilinx.com/html_docs/registers/am012/am012-versal-register-reference.html#cpm4_pcie0_attr___phy_rdy.html
* For Gen3 configuration, check by bypassing phase-2 and phase-3
    * Ref: https://www.xilinx.com/html_docs/registers/am012/am012-versal-register-reference.html#cpm4_pcie0_attr___pl_eq_bypass_phase23.html
* Check if the BARs have been enumerated or not:
    * https://www.xilinx.com/html_docs/registers/am012/am012-versal-register-reference.html#cpm4_pcie0_attr___pfx_bar0_control_0.html
    * https://www.xilinx.com/html_docs/registers/am012/am012-versal-register-reference.html#cpm4_pcie0_attr___pfx_bar0_control_1.html
    * https://www.xilinx.com/html_docs/registers/am012/am012-versal-register-reference.html#cpm4_pcie0_attr___pfx_bar0_control_2.html
    * https://www.xilinx.com/html_docs/registers/am012/am012-versal-register-reference.html#cpm4_pcie0_attr___pfx_bar0_control_2.html
* For MSI/MSI-X related issue, see: https://www.xilinx.com/html_docs/registers/am012/am012-versal-register-reference.html#cpm4_pcie0_attr___cfg_interrupt.html

.. note::
    * To read CPM registers, use xsdb. Check the link below for the details on reading CPM registers using xsdb
        * https://forums.xilinx.com/t5/Design-and-Debug-Techniques-Blog/Register-based-debugging-of-Versal-ACAP-CPM-Mode-for-PCI-Express/ba-p/1221922 

..    .. _versal_cpm_faqs:

.. General FAQs
============
