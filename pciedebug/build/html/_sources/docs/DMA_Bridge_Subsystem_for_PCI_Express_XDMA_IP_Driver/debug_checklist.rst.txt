.. _dma_bridge_xdma_ip_debug_checklist:

General Debug Checklist
=======================

* Check the status of top-level interface signals
    * :ref:`top_level_interface_signals`
* Check the DMA status ports
    * :ref:`channel_0_3_status_ports`
* Review 'Example H2C Flow'
    * :ref:`example_h2c_flow`
* Review 'Example C2H Flow'
    * :ref:`example_c2h_flow`
* Review the driver flow
    * :ref:`initial_setup_for_h2c_and_c2h_transfers`
    * :ref:`dma_h2c_transfer_summary`
    * :ref:`dma_c2h_transfer_summary`
* For issues related to AXI-Stream designs, capture the following interfaces in Vivado ILA
    * :ref:`h2c_channel_0_3_axi4_stream_interface_signals`
    * :ref:`c2h_channel_0_3_axi4_stream_interface_signals`
* For issues related to AXI-Memory Mapped designs, capture the following interfaces in Vivado ILA
    * :ref:`axi4_memory_mapped_read_address_interface_signals`
    * :ref:`axi4_memory_mapped_read_interface_signals`
    * :ref:`axi4_memory_mapped_write_address_interface_signals`
    * :ref:`axi4_memory_mapped_write_interface_signals`
    * :ref:`axi4_memory_mapped_write_response_interface_signals`
* To track read/write to DMA internal registers, capture the following interfaces in Vivado ILA
    * :ref:`config_axi4_lite_memory_mapped_write_master_interface_signals`
    * :ref:`config_axi4_lite_memory_mapped_read_master_interface_signals`
    * :ref:`config_axi4_lite_memory_mapped_write_slave_interface_signals`
    * :ref:`config_axi4_lite_memory_mapped_read_slave_interface_signals`
* For issues related to DMA Bypass, capture the following interface in Vivado ILA
    * :ref:`axi4_memory_mapped_master_bypass_read_address_interface_signals`
    * :ref:`axi4_memory_mapped_master_bypass_read_interface_signals`
    * :ref:`axi4_memory_mapped_master_bypass_write_address_interface_signals`
    * :ref:`axi4_memory_mapped_master_bypass_write_interface_signals`
    * :ref:`axi4_memory_mapped_master_bypass_write_response_interface_signals`
* For issues related to Descriptor Bypass Port, capture the following interfaces in Vivado ILA
    * :ref:`h2c_0_3_descriptor_bypass_port`
    * :ref:`c2h_0_3_descriptor_bypass_ports`

.. _dma_bridge_xdma_ip_faqs:

General FAQs
============

  * The channel register space is defined as 0x00 - H2C Channel Identifier   0x04 - H2C Channel Control etc. in PG195. How to find out address of these registers for different channels?
      * Refer to 'PCIe to DMA Address Format' table in PG195. For e.g. address for 'H2C Channel Control' register in Channel-1 will be: 0x00000104
          * Bit 7:0 - Byte Offset
          * Bit 11:8 - Channel ID[3:0]
          * Bit 15:12 - Submodule within the DMA ( 0 for H2C channel and 1 for C2H Channel)
          * Bit 31:16 - Reserved
  * Is XDMA Driver supported on ARM Processor?
      * No, it is not supported.
  * What is the difference between xdma0_bypass_h2c_0, xdma0_bypass_c2h_0 and xdma0_bypass?
      * xdma0_bypass_h2c* and xdma0_bypass_c2h_* devices are for internal use only so should be ignored.
  * Is it possible to combine different devices such as xdma0_c2h_0, xdma0_c2h_1, xdma0_c2h_2, xdma0_c2h_3 as a single device and select the individual channel through a parameter?
      * No, that is not possible. Each channel has its device and the driver is setup that way to be used.



