.. _general_debug_techniques:

=============================
PCIe General Debug Techniques 
=============================

.. _initial_check:

First thing to check
--------------------

    * If possible, check whether the issue could be reproduced with the generated example design in Gen1x1 configuration. 
    * In the case of a user design, check with Gen1x1 configuration if the IP is configured for a higher speed and lane width.

.. _check_ltssm_status:

How to check the LTSSM status?
------------------------------

See:

    * https://www.xilinx.com/support/answers/72471.html (For US+ Devices)
    * https://forums.xilinx.com/t5/Design-and-Debug-Techniques-Blog/Debugging-Versal-ACAP-Integrated-Block-for-PCIe-Express-link/ba-p/1203707 (For Versal Devices)

.. _successful_device_enumeration:

How to check the endpoint was successfully detected and enumerated ?
--------------------------------------------------------------------

See https://forums.xilinx.com/t5/Design-and-Debug-Techniques-Blog/Debugging-PCIe-Issues-using-lspci-and-setpci/ba-p/1148199 

.. _device_reenumeration:

How to enumerate the endpoint when FPGA is configured after enumeration?
------------------------------------------------------------------------

See https://www.xilinx.com/support/answers/37406.html 

.. _debug_link_training_issues:

How to debug link training issues?
----------------------------------

See https://www.xilinx.com/support/answers/73361.html 

.. _ipi_block_from_user_design:

Generating IP Block Design from the Example Design
--------------------------------------------------

The below instructions provide a technique to isolate the Versal PL PCIe IP IPI block design from a full design.
In Versal ACAP devices, it introduces the concept of Split IP where the full PL PCIe IP is an integrated IPI block consisting of various components that make up the IP. 
For more details on the Split IP concept, see the blog in the link below:  

https://forums.xilinx.com/t5/Design-and-Debug-Techniques-Blog/Understanding-the-new-PL-PCIE-IP-Generation-flow-for-Versal-ACAP/ba-p/1215986

If you have a design and wish to extract only the PL PCIe block design, you can follow the steps below.

1. Create project (e.g. project_1 as project name) and generate QDMA IP from the IP catalog 
2. Configure the QDMA IP as an endpoint
3. Right click on the IP and generate the example design 
4. In the example design, there will be two block designs generated. Close the block design ending with “_rp” 
5. Export block design with the name ending with “_ep” as .tcl file. To do this, click “File -> Export -> Export Block Design” as shown in the snapshot below. 
   
.. image:: images/generating_IPI_block_design.png

6. The generated .tcl file could be sourced in a new project to create only the QDMA IPI block design.  

Set Bus Master Enable bit
--------------------------------------------------

If you are sending upstream traffic from endpoint to the host, make sure the Bus Master Enable is set in the endpoint. You can check this by looking at lspci log.
The command to enable Bus Master Enable bit is:

> setpci -s <bb:dd.f> COMMAND=0x7

An illustrative example to set the Bus Master Enable bit can be found in the link below:

https://support.xilinx.com/s/article/Reading-PCIe-Configuration-Space-of-Versal-ACAP-Integrated-Block-for-PCI-Express-through-Configuration-Management-Interface 
    