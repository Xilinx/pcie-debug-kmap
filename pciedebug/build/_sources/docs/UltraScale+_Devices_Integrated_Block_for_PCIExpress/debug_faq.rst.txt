.. _ultrascale_debug_checklist:

General Debug Checklist
=======================

* Check if the PCIe IP is detected in lspci
    * Ref: https://forums.xilinx.com/t5/Design-and-Debug-Techniques-Blog/Debugging-PCIe-Issues-using-lspci-and-setpci/ba-p/1148199
* If the PCIe is not detected in lspci, do a warm reboot of the host machine
    * Re-run lspci
* If the PCIe IP is still not detected, check if the ltssm state is at L0. Enable PCIe Link Debug Feature.
    * Ref: https://forums.xilinx.com/t5/Design-and-Debug-Techniques-Blog/Debugging-PCI-Express-Link-Training-Issues-with-Integrated/ba-p/1097525
    * LTSSM can also be checked by capturing the following signals
        * * :ref:`configuration_status_interface_port_descriptions_ltssm`
* If the ltssm is not in L0 or the ltssm goes to recovery multiple times, check the eye diagram as discussed in the link below.
    * Ref: https://forums.xilinx.com/t5/Design-and-Debug-Techniques-Blog/Debugging-PCI-Express-Link-Training-Issues-with-Integrated/ba-p/1097525
* If the eye diagram is not wide enough and the link issues persist, see the document in the following answer record.
    * Ref: https://www.xilinx.com/support/answers/73361.html
* Check if the phy_link is asserted
    * :ref:`configuration_status_interface_port_descriptions_phy_link`
* To check what speed and width the link is negotiated to, capture the following signals in Vivado ILA
    * :ref:`configuration_status_interface_port_descriptions_negotiated_width`
* If the memory read/write is failing and the upstream Memory Read/Write are failing, check if Memory Space Enable and Bus Master Enable bits in the command register are asserted. Capture the following signals in Vivado ILA to check the status.
    * :ref:`configuration_status_interface_port_descriptions_function_status`
* Check if there are any local errors occurred by checking the following signal. This signal many not work for all PCIe Link Width/Speed configurations. This signal should be used just as a reference.
    * :ref:`configuration_status_interface_port_descriptions_local_error`
* Make sure the following signal is asserted
    * :ref:`configuration_status_interface_port_descriptions_link_training`
* If the issue is related to completions, check the following signals
    * :ref:`requester_completion_descriptor_fields_byte_count`
    * :ref:`requester_completion_descriptor_fields_dword_count`
    * :ref:`requester_completion_descriptor_fields_error_code`

.. note::
    The signals referenced above are from product guide PG213.Please refer to the latest version of the document for new updates and more details. 

.. _ultrascale_faqs:

General FAQs
============

  * Which Data Alignment option should be used for better performance?
      * For performance critical applications, Dword Aligned mode should be used instead of Address-Aligned mode.
  * Does the PIO example design generated with the UltraScale+ Devices Integrated Block for PCI Express IP support more than 1 DW TLP?
      * In Address Align Mode, the PIO design supports single Dword payload Read and Write PCI Express transactions to 32-/64-bit address memory spaces and I/O space with support for completionTLPs.
      * In the case of Dword Align Mode, the PIO Design supports multiple Dword payload (Up to 256 DW) read and write PCI Express transactions to 32-bit Address Memory Spaces with support for completion TLPs. 
  * Does UltraScale+ Devices Integrated Block for PCI Express IP support Loopback Master Capability?
      * The Loopback Master Capability is supported in Root Port mode only
  * Is Lane Reversal Supported in UltraScale+ Devices Integrated Block for PCI Express IP?
      * Lane Reversal is supported. However, with UltraScale+ Integrated Block for PCI Express IP as an Endpoint, Lane reversal must not be enabled if the Link Partner has the Lane reversal capability. By default EndPoint configuration has Lane Reversal Support disabled through the attribute DISABLE_LANE_REVERSAL. 


