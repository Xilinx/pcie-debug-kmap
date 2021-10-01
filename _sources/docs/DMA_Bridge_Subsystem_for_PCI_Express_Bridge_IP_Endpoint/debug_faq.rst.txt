.. _dma_bridge_ip_debug_checklist:

General Debug Checklist
=======================

* Confirm the clocking architecture is correct
    * :ref:`clocking_diagram_ultrascale+_devices`
* Confirm the reset connection is correct
    * Endpoint:
        * :ref:`endpoint_system_reset_connection_ultrascale+_devices`
    * Rootport:
        * :ref:`root_port_system_reset_connection_ultrascale+_devices`
* Check the status of Phy Status Control Register 
    * :ref:`phy_status_control_register`
* If the issue is related to incoming or outgoing packets from the user logic, check the following interface signals in Vivado ILA
    * :ref:`axi_slave_interface_signals`
    * :ref:`axi_master_interface_signals`
* If there is an issue with bridge register reads or write, check the following interface
    * :ref:`axi4-lite_control_interface_signals`
* For interrupt related issues, check the following signals and register:
    * :ref:`axi_bridge_for_pcie_gen3_msi_signals`
    * :ref:`axi_bridge_for_pcie_gen3_msi-x_signals`
    * :ref:`dma_bridge_subsystem_for_pcie_in_bridge_mode_interrupt_signals`
* For Root Port related issues, check:
    * :ref:`root_port_status_control_register`
    * :ref:`root_port_error_fifo_read_register`
    * :ref:`interrupt_decode_register`

.. note::
    Please refer to the latest version of PG194 for new updates and more details on referenced signals and registers. 

.. .. _dma_bridge_ip_faqs:

.. General FAQs
.. ============
