.. _IP Configuration Parameters:

IP Configuration Parameters
---------------------------

.. list-table::
   :widths: 15 15 15
   :header-rows: 1

   * - Features
     - Options
     - Remarks
 
   * - DMA Interface Selection
     - - AXI MM and AXI Stream with Completion
       - AXI MM
       - AXI Stream with Completion
     - 
   
   * - Number of Queues
     -
     -
 
   * - Bridge Interface Options
     - - Bridge Slave Mode
       - VDM Enable
       - AXI-Lite Slave Interface
       - AXI-Lite CSR Slave Interface
     - 
 
   * - SRIOV
     - - SRIOV Capability
       - Enable FLR
       - Enable Mailbox among functions
       - No. of Physical Functions
     - 
  
   * - PCIE BARs
     - - DMA
       - AXI Bridge Master
       - AXI Lite Master
     - 
  
   * - Enable PF0 MSI-X Capability Structure
     -
     -
  
   * - Exteded Tag Field
     -
     -
  
   * - Enable Slot Clock Configuration
     -
     -
  
   * - Descriptor Bypass for read and write
     - - None
       - Descriptor Bypass and Internal
     -
   
   * - Performance Options
     - - Prefetch Cache Depth
       - CMPT Coalesce Max Buffer
     -
   
   * - Data Protection
     -
     -
 
   * - Descriptor Fetch Credit (enabled in software context)
     -
     -
 
   * - Are these enabled?
     - - Writeback
       - Error
       - Marker Response
     -
  
   * - Status Descriptor Enabled? (if user_trig was set when marker was inserted)
     - - check trigger mode
     -
 
   * - C2H Streaming Completion
     -
     -
 
   * - Interrupts and completion status writes individually enabled and disabled for each queue?
     -
     -
 
   * - Is Error mask enabled?
     -
     -
 
   * - Descriptor Bypass per queue?
     -
     -
 
   * - Internal mode for all queues?
     -
     -
 
   * - Prefetch enabled per queue?
     -
     -
 
   * - Interrupt mode enabled?
     -
     -
 
   * - Is Traffic manager used?
     -
     -
 
   * - C2H Stream Modes (Descriptor Prefetch Cache Mode)
     - - Simple Bypass Mode
       - Cache Bypass Mode
       - Cache Internal Mode
     -
 
   * - C2H Stream Packet Types
     - - Regular Packet
       - Immediate Data Packet
       - Marker Packet
       - Zero Length Packet
       - Disable Completion Packet
     - 
 