.. _dma_subsystem_bridge_endpoint_debug_gotchas:

Debug Gotchas
=======================

* An asserted bit in the Interrupt Decode register does not cause the interrupt line to assert unless the corresponding bit in the Interrupt Mask register is also set.
    * 
* Available for the DMA/Bridge Subsystem for PCIe in AXI Bridge mode, there is an optional dma_bridge_resetn input pin which allows you to reset all internal Bridge engines and registers as well as all AXI peripherals driven by axi_aresetn and axi_ctl_aresetn pins. When the following parameter is set, dma_bridge_resetn does not need to be asserted during initial link up operation because it will be done automatically by the IP. You must terminate all transactions before asserting this pin. After being asserted, the pin must be kept asserted for a minimum duration of at least equal to the Completion Timeout value (typically 50 ms) to clear any pending transfer that may currently be queued in the data path. To set this parameter, type the following command at the Tcl command line:
    * set_property -dict [list CONFIG.soft_reset_en {true}] [get_ips <ip_name>]
* For PCIe® requests with lengths greater than 1 Dword, the size of the data burst on the Master AXI interface will always equal the width of the AXI data bus even when the request received from the PCIe link is shorter than the AXI bus width. s_axi_wstrb can be used to facilitate data alignment to an address boundary.
    * s_axi_wstrb may equal 0 in the beginning of a valid data cycle and will appropriately calculate an offset to the given address. However, the valid data identified by s_axi_wstrb must be continuous from the first byte enable to the last byte enable.
* The Bridge core conforms to PCIe® transaction ordering rules. See the PCI-SIG Specifications for the complete rule set. The following behaviors are implemented in the Bridge core to enforce the PCIe transaction ordering rules on the highly-parallel AXI bus of the bridge. 
    * The bresp to the remote (requesting) AXI4 master device for a write to a remote PCIe device is not issued until the MemWr TLP transmission is guaranteed to be sent on the PCIe link before any subsequent TX-transfers. 
    * If Relaxed Ordering bit is not set within the TLP header, then a remote PCIe device read to a remote AXI slave is not permitted to pass any previous remote PCIe device writes to a remote AXI slave received by the Bridge core. The AXI read address phase is held until the previous AXI write transactions have completed and bresp has been received for the AXI write transactions. If the Relaxed Ordering attribute bit is set within the TLP header, then the remote PCIe device read is permitted to pass. 
    * Read completion data received from a remote PCIe device are not permitted to pass any remote PCIe device writes to a remote AXI slave received by the Bridge core prior to the read completion data. The bresp for the AXI write(s) must be received before the completion data is presented on the AXI read data channel.
* The integrated block for PCI Express® detects a malformed TLP. For the IP configured as an Endpoint core, a malformed TLP results in a fatal error message being sent upstream if error reporting is enabled in the Device Control register.
    * 
* The slave bridge monitors AXI read and write burst type inputs to ensure that only the INCR (incrementing burst) type is requested. Any other value on these inputs is treated as an error condition and the Slave Illegal Burst (SIB) interrupt is asserted. In the case of a read request, the Bridge asserts SLVERR for all data beats and arbitrary data is placed on the s_axi_rdata bus. In the case of a write request, the Bridge asserts SLVERR for the write response and all write data is discarded.
    * 
* The normal operation of the Bridge core is dependent on the integrated block for PCIe establishing and maintaining the point-to-point link with an external device for PCIe. If the link has been lost, it must be re-established to return to normal operation.
    * 
* When a Hot Reset is received by the Bridge core, the link goes down and the PCI Configuration Space must be reconfigured.
    * 
* Initiated AXI4 write transactions that have not yet completed on the AXI4 bus when the link goes down have a SLVERR response given and the write data is discarded. Initiated AXI4 read transactions that have not yet completed on the AXI4 bus when the link goes down have a SLVERR response given, with arbitrary read data returned.
    * 
* Any MemWr TLPs for PCIe that have been received, but the associated AXI4 write transaction has not started when the link goes down, are discarded.
    * 
* Root Port BAR does not support packet filtering (all TLPs received from PCIe link are forwarded to the user logic), however Address Translation can be configured to enable or disable, depending on the IP configuration.
    * 
* During core customization in the Vivado® Design Suite, when there is no BAR enabled, RP passes all received packets to the user application without address translation or address filtering.
    * 
*  When BAR is enabled, by default the BAR address starts at 0x0000_0000 unless programmed separately. Any packet received from the PCIe® link that hits a BAR is translated according to the PCIE-to-AXI Address Translation rules.
    *  The IP must not receive any TLPs outside of the PCIe BAR range from the PCIe link when RP BAR is enabled. If this rule cannot be enforced, it's recommended that the PCIe BAR is disabled and do address filtering and/or translation outside of the IP.
* Endpoint mode only (parameter: set_finite_credit)
    * FALSE: Infinite Completion credit is advertised to Root Complex.
    * TRUE: Finite Completion credit is advertised to Root Complex.
  .. warning::
     PCIe specification requires Endpoint to advertise Infinite Completion credit only. Most Root Complex can obey Finite Completion credit advertisement from Endpoint, however use this option with caution.
* AXI4-Lite Interfaces Read from a register that does not have all 0s as a default to verify that the interface is functional. Output s_axi_arready asserts when the read address is valid and output s_axi_rvalid asserts when the read data/response is valid. If the interface is unresponsive ensure that the following conditions are met. 
    * For older versions of the AXI Bridge for PCIe Gen3 core with the axi_ctl_aclk port, ensure the axi_ctl_aclk and axi_ctl_aclk_out pins are connected to the design and are pulsing out of the IP.
    * The interface is not being held in reset, and axi_aresetn is an active-Low reset. 
    * Ensure that the main core clocks are toggling and that the enables are also asserted. 
    * Has a simulation been run? Verify in simulation and/or a Vivado® Design Suite debug feature capture that the waveform is correct for accessing the AXI4-Lite interface.

.. note::
    The above debug gotchas are taken from QDMA Product Guide PG195.Please refer to the latest version of the document for new updates and more details. 