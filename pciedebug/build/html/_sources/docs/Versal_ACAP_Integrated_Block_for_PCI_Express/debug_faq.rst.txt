.. _versal_integrated_block_debug_checklist:

General Debug Checklist
=======================

* * Review Versal ACAP PCB Design User Guide (UG863) and Versal ACAP Schematic Review Checklist (XTP546)
    * https://docs.xilinx.com/r/en-US/ug863-versal-pcb-design
    * https://www.xilinx.com/cgi-bin/docs/ctdoc?cid=90f995d8-c517-4adc-a95c-13a8994d6618;d=xtp546-versal-schematic-review-checklist.zip
* Check if the PCIe IP is detected in lspci 
    * Ref: https://forums.xilinx.com/t5/Design-and-Debug-Techniques-Blog/Debugging-PCIe-Issues-using-lspci-and-setpci/ba-p/1148199
* If the PCIe is not detected in lspci, do a warm reboot of the host machine
    * Re-run lspci 
* If the PCIe IP is still not detected, check if the ltssm state is at L0. Enable PCIe Link Debug Feature.
    * Ref: https://forums.xilinx.com/t5/Design-and-Debug-Techniques-Blog/Debugging-Versal-ACAP-Integrated-Block-for-PCIe-Express-link/ba-p/1203707 
        * See "PCIe Link Debug Enablement" section in PG343
* If the ltssm is not in L0 or the ltssm goes to recovery multiple times, check the eye diagram as discussed in the link below.
    * https://forums.xilinx.com/t5/Design-and-Debug-Techniques-Blog/Debugging-Versal-ACAP-Integrated-Block-for-PCIe-Express-link/ba-p/1203707  
* If the eye diagram is not wide enough and the link issues persist, see the document in the following answer record. The document provided in the answer record is for UltraScale+ device but the debug theory applies to Versal ACAP devices as well.
    * https://www.xilinx.com/support/answers/73361.html 
        * Key things to check are: noise in power and clock jitter. Confirm both are as per the spec.

.. _versal_integrated_block_faqs:

General FAQs
============

* The Versal ACAP Integrated Block for PCI Express now comes with BMD example design with the generation of the IP. Is it possible to generate PIO example design instead?
    * Yes, the PIO example design can be generated. To do so, enter the command below at the Vivado Tcl console prompt after theVersal ACAP Integrated Block for PCIe® IP is generated
    * set_property config.bmd_pio_mode false [get_ips pcie_versal_0]
* Can I use CPM4 pin MIO38 to reset PL PCIe?
    * Yes this is possible. Before opening the example design, set the following Tcl property to sue the reset on MIO38 pin:
      * set_property CONFIG.insert_cips {true} [get_ips pcie_versal_0] 
* In UltraScale+ and previous devices, direct assignment of GTs are not possible in the user constraints. Does similar limitation still exist with Versal devices?
    * In Versal, the GT locations assignment can be done in the user constraints, while changing GT locations in GT customization IP is not available. 
* The PL PCIE IP Generation flow in Versal ACAP devices is different from previous devices such as UltraScale and UltraScale+. What are the major changes that went into PL based PCIe IP in Versal ACAP devices?
    * Below are the major changes:
        * GT components are updated from Common/Channel to a quad granularity.
        * GT wizard flows are modified to use the Vivado® IP integrator.
        * Only the Vivado IP integrator-based block design flow is currently supported with manual or automatic connectivity.
        * The required GT and PHY IP blocks for Versal ACAP PL PCIe interfaces are outside of the Versal ACAP PL PCIe4 IP.
    * For more information see: https://forums.xilinx.com/t5/Design-and-Debug-Techniques-Blog/Understanding-the-new-PL-PCIE-IP-Generation-flow-for-Versal-ACAP/ba-p/1215986