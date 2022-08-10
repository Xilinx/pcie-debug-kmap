.. .. _dma_bridge_ip_issues_answers:

.. Issues and Answers
.. ==================

.. _dma_bridge_ip_issues_debug_tips_questions:

Issues/Debug Tips/Questions
===========================

    * Relation between PCIe link up and axi_aresetn
        * When the PCIe IP is in D0 uninitialized state which means it physically linked up but there hasn’t been any exchanges of INITFC and enumeration (Config TLPs) and thus no TLPs can get through. Only when axi_aresetn (which is essentially user_reset from PCIe IP) is released, that’s when the Transaction Layer is up. AXI peripherals should therefore use the axi_aresetn indication from the AXI Bridge IP before sending over a packet because this indicates when the Bridge and PCIe link is usable].
    * Accessing the host testbench before the link is established results in the bridge IP to hang
        * If the memory access is done after the link is established, it will return Slave Error or Decode error depending on whether it’s non-existent PCIe or AXI memory addresses. These type of errors should always get a response. When the memory access is attempted when there is no link, the system would hang as there is no response on the AXI bus as ready is not asserted without a link. In AXI, there is no Timeout mechanism by default unlike PCIe.  A possible workaround would be to use the firewall IP.
    * The AXI outstanding number is set to be 8 (C_S_AXI_NUM_READ = 8), but only one AXI read can be issued instead of 8.
        * Check by disabling "AXI Slave narow burst"
    * The read access from the PCIe link reads the data equal to the size of the AXI bus width event though the read access is shorter than the AXI bus width
        * This is a limitation in the IP. For PCIe® requests with lengths greater than 1 Dword, the size of the data burst on the MasterAXI interface will always equal the width of the AXI data bus even when the request received from the PCIe link is shorter than the AXI bus width. 
