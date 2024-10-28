.. _PG302_important_design_considerations:


Important Design Considerations from PG302
==========================================

.. container:: Introduction

    When tackling design issues, navigating a lengthy product guide can be overwhelming. To streamline your troubleshooting process, this article lists essential design considerations directly from the product guide, highlighting key points to keep in mind. This article condenses over 200 pages into a focused, 10-12 page summary, making it easier to access the most relevant information quickly. For full details, be sure to click the provided link to explore each consideration further in the original product guide.

.. note::
    :class: highlight-box

    • https://docs.amd.com/r/en-US/pg302-qdma/Descriptor-Engine?tocId=IapAFAz_pMIftrJvFO5TaQ
    
        - If a queue is associated with interrupt aggregation, AMD recommends that the status descriptor be turned off, and instead the DMA status be received from the interrupt aggregation ring.

.. note::
    :class: highlight-box

    • https://docs.amd.com/r/en-US/pg302-qdma/H2C-Stream-Engine?tocId=5bP1owSpu0KFRSL~uEv2lQ
    
        - The total length of all descriptors put together must be less than 64 KB.
        - For internal mode queues, each descriptor defines a single AXI4-Stream packet to be transferred to the H2C AXI-ST interface. A packet with multiple descriptors straddling is not allowed due to the lack of per queue storage. However, packets with multiple descriptors straddling can be implemented using the descriptor bypass mode.

.. note::
    :class: highlight-box

    • https://docs.amd.com/r/en-US/pg302-qdma/C2H-Stream-Engine?tocId=w7xytGq781SYeea3XB7G3A
    
        - In Simple Bypass Mode, the engine does not track anything for the queue, and the user logic can define its own method to receive descriptors. The user logic is then responsible for delivering the packet and associated descriptor through the simple bypass interface. The ordering of the descriptors fetched by a queue in the bypass interface and the C2H stream interface must be maintained across all queues in bypass mode.

.. note::
    :class: highlight-box

    • https://docs.amd.com/r/en-US/pg302-qdma/AXI-Memory-Mapped-Bridge-Master-Interface
    
        - One or more PCIe BAR of any physical function (PF) or virtual function (VF) can be mapped to the AXI-MM bridge master interface. This selection must be made prior to design compilation.
        - Virtual function group (VFG) refers to the VF group number. It is equivalent to the PF number associated with the corresponding VF. VFG_OFFSET refers to the VF number with respect to a particular PF. Note that this is not the FIRST_VF_OFFSET of each PF.
        - Note that all VFs belonging to the same PF share the same PCIe to AXI translation vector. Therefore, the AXI address space of each VF is concatenated together. Use VFG_OFFSET to calculate the actual starting address of AXI for a particular VF.

.. note::
    :class: highlight-box

    • https://docs.amd.com/r/en-US/pg302-qdma/PCIe-RQ/RC
    
        - With a 512-bit interface, straddling is enabled. While straddling is supported, all combinations of RQ straddled transactions might not be implemented.

.. note::
    :class: highlight-box

    • https://docs.amd.com/r/en-US/pg302-qdma/General-Design-of-Queues
    
        - If queue size is 8, which contains the entry index 0 to 7, the last entry (index 7) is reserved for status. This index should never be used for PIDX update, and PIDX update should never be equal to CIDX. For this case, if CIDX is 0, the maximum PIDX update would be 6.

.. note::
    :class: highlight-box

    • https://docs.amd.com/r/en-US/pg302-qdma/Limitations
    
        - Use AXI SmartConnect to support Narrow Burst.
        - ECC and Slave Narrow Burst support is mutually exclusive.
        - If you want an ECC feature, the recommendation is to up-size your AXI Master externally.

.. note::
    :class: highlight-box

    • https://docs.amd.com/r/en-US/pg302-qdma/Performance-and-Resource-Utilization
    
        - Global register for timer should have a value of 30 for 3 μs.
        - The driver should update TX/RX PIDX in batches of 64.
        - The driver should update the H2C PIDX in batches of 64, and also update for the last descriptor of the scatter gather list.
        - For optimal QDMA streaming performance, packet buffers of the descriptor ring should be aligned to at least 256 bytes.
        - AMD recommends that you limit the total outstanding descriptor fetch to be less than 8 KB on the PCIe. For example, limit the outstanding credits across all queues to 512 for a 16B descriptor.

.. note::
    :class: highlight-box

    • https://docs.amd.com/r/en-US/pg302-qdma/Performance-and-Resource-Utilization
    
        - AMD recommends that this port be asserted once in 32 packets or 64 packets. And if there are no more descriptors left then assert h2c_byp_in_st_sdi at the last descriptor. This requirement is per queue basis, and applies to AXI4 (H2C and C2H) bypass transfers and AXI4-Stream H2C transfers.
        - For AXI4-Stream C2H Simple bypass mode, the dsc_crdt_in_fence port should be set to 1 for performance reasons. This recommendation assumes the user design already coalesced credits for each queue and sent them to the IP. In internal mode, set the fence bit in the QDMA_C2H_PFCH_CFG_2 (0xA84) register.

.. note::
    :class: highlight-box

    • https://docs.amd.com/r/en-US/pg302-qdma/Descriptor-Context
    
        - Prior to enabling the queue, the hardware and credit context must first be cleared. After this is done, the software context can be programmed and the qen bit can be set to enable the queue. After the queue is enabled, the software context should only be updated through the direct mapped address space to update the Producer Index and Interrupt Arm® bit, unless the queue is being disabled.
        - Reading the context when the queue is enabled is not recommended as it can result in reduced performance.

.. note::
    :class: highlight-box

    • https://docs.amd.com/r/en-US/pg302-qdma/Software-Descriptor-Context-Structure-0x0-C2H-and-0x1-H2C
    
        - irq_req: Interrupt due to error waiting to be sent (waiting for irq_arm). This bit should be cleared when the queue context is initialized.
        - err_wb_sent: A writeback/interrupt was sent for an error. Once this bit is set no more writebacks or interrupts will be sent for the queue. This bit should be cleared when the queue context is initialized.
        - irq_no_last: This bit should be initialized to 0 when the queue context is initialized.
        - dsc_sz: If bypass mode is not enabled, 32B is required for Memory Mapped DMA, 16B is required for H2C Stream DMA, and 8B is required for C2H Stream DMA.
        - fetch_max: The max outstanding is fetch_max + 1. Higher value can increase the single queue performance.
        - fcrd_en: Set to 1 for C2H ST.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/Descriptor-Engine?tocId=IapAFAz_pMIftrJvFO5TaQ

    - If a queue is associated with interrupt aggregation, AMD recommends that the status descriptor be turned off, and instead the DMA status be received from the interrupt aggregation ring.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/H2C-Stream-Engine?tocId=5bP1owSpu0KFRSL~uEv2lQ

    - The total length of all descriptors put together must be less than 64 KB.
    - For internal mode queues, each descriptor defines a single AXI4-Stream packet to be transferred to the H2C AXI-ST interface. A packet with multiple descriptors straddling is not allowed due to the lack of per queue storage. However, packets with multiple descriptors straddling can be implemented using the descriptor bypass mode.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/C2H-Stream-Engine?tocId=w7xytGq781SYeea3XB7G3A

    - In Simple Bypass Mode, the engine does not track anything for the queue, and the user logic can define its own method to receive descriptors. The user logic is then responsible for delivering the packet and associated descriptor through the simple bypass interface. The ordering of the descriptors fetched by a queue in the bypass interface and the C2H stream interface must be maintained across all queues in bypass mode.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/AXI-Memory-Mapped-Bridge-Master-Interface

    - One or more PCIe BAR of any physical function (PF) or virtual function (VF) can be mapped to the AXI-MM bridge master interface. This selection must be made prior to design compilation.
    - Virtual function group (VFG) refers to the VF group number. It is equivalent to the PF number associated with the corresponding VF. VFG_OFFSET refers to the VF number with respect to a particular PF. Note that this is not the FIRST_VF_OFFSET of each PF.
    - Note that all VFs belonging to the same PF share the same PCIe to AXI translation vector. Therefore, the AXI address space of each VF is concatenated together. Use VFG_OFFSET to calculate the actual starting address of AXI for a particular VF.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/PCIe-RQ/RC

    - With a 512-bit interface, straddling is enabled. While straddling is supported, all combinations of RQ straddled transactions might not be implemented.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/General-Design-of-Queues

    - If queue size is 8, which contains the entry index 0 to 7, the last entry (index 7) is reserved for status. This index should never be used for PIDX update, and PIDX update should never be equal to CIDX. For this case, if CIDX is 0, the maximum PIDX update would be 6.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/Limitations

    - Use AXI SmartConnect to support Narrow Burst.
    - ECC and Slave Narrow Burst support is mutually exclusive.
    - If you want an ECC feature, the recommendation is to up-size your AXI Master externally.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/Performance-and-Resource-Utilization

    - Global register for timer should have a value of 30 for 3 μs.
    - The driver should update TX/RX PIDX in batches of 64.
    - The driver should update the H2C PIDX in batches of 64, and also update for the last descriptor of the scatter-gather list.
    - For optimal QDMA streaming performance, packet buffers of the descriptor ring should be aligned to at least 256 bytes.
    - AMD recommends that you limit the total outstanding descriptor fetch to be less than 8 KB on the PCIe. For example, limit the outstanding credits across all queues to 512 for a 16B descriptor.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/Performance-and-Resource-Utilization

    - AMD recommends that this port be asserted once in 32 packets or 64 packets. And if there are no more descriptors left then assert h2c_byp_in_st_sdi at the last descriptor. This requirement is per queue basis, and applies to AXI4 (H2C and C2H) bypass transfers and AXI4-Stream H2C transfers.
    - For AXI4-Stream C2H Simple bypass mode, the dsc_crdt_in_fence port should be set to 1 for performance reasons. This recommendation assumes the user design already coalesced credits for each queue and sent them to the IP. In internal mode, set the fence bit in the QDMA_C2H_PFCH_CFG_2 (0xA84) register.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/Descriptor-Context

    - Prior to enabling the queue, the hardware and credit context must first be cleared. After this is done, the software context can be programmed and the qen bit can be set to enable the queue. After the queue is enabled, the software context should only be updated through the direct mapped address space to update the Producer Index and Interrupt Arm® bit, unless the queue is being disabled.
    - Reading the context when the queue is enabled is not recommended as it can result in reduced performance.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/Software-Descriptor-Context-Structure-0x0-C2H-and-0x1-H2C

    - irq_req: Interrupt due to error waiting to be sent (waiting for irq_arm). This bit should be cleared when the queue context is initialized.
    - err_wb_sent: A writeback/interrupt was sent for an error. Once this bit is set no more writebacks or interrupts will be sent for the queue. This bit should be cleared when the queue context is initialized.
    - irq_no_last: This bit should be initialized to 0 when the queue context is initialized.
    - dsc_sz: If bypass mode is not enabled, 32B is required for Memory Mapped DMA, 16B is required for H2C Stream DMA, and 8B is required for C2H Stream DMA.
    - fetch_max: The max outstanding is fetch_max + 1. Higher value can increase the single queue performance.
    - fcrd_en: Set to 1 for C2H ST.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/Descriptor-Fetch

    - If fetch crediting is enabled, the user logic is required to provide a credit for each descriptor that should be fetched.
    - In each direction, C2H and H2C are allocated 256 entries for descriptor fetch completions. Each entry is the width of the datapath. If sufficient space is available, the fetch is allowed to proceed. A given queue can only have one descriptor fetch pending on PCIe at any time.
    - Available descriptors are always - 2. At any time, the software should not update the PIDX to more than - 2.
    - If queue size is 8, which contains the entry index 0 to 7, the last entry (index 7) is reserved for status. This index should never be used for the PIDX update, and the PIDX update should never be equal to CIDX. For this case, if CIDX is 0, the maximum PIDX update would be 6.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/Internal-Mode-Writeback-and-Interrupts-AXI-MM-and-H2C-ST

    - It is recommended the wbi_chk bit be set for all internal mode operation, including when interval mode is enabled.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/Descriptor-Bypass-Mode-Writeback/Interrupts

    - If interrupts are enabled, the user logic must monitor the traffic manager output for the irq_arm. After the irq_arm bit is observed for the queue, a descriptor with the sdi bit is sent to the DMA. Once a descriptor with the sdi bit is sent, another irq_arm assertion must be observed before another descriptor with the sdi bit can be sent.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/Traffic-Manager-Output-Interface

    - While the tm_dsc_sts interface is a valid/ready interface, it should not be back-pressured for optimal performance.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/Errors?tocId=RVdoy7Fzh1DBbxgMq3ytwg

    - After the queue is invalidated, if there is an error you can determine the cause by reading the error registers and context for that queue. You must clear and remove that queue, and then add the queue back later when needed.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/Operation

    - Any descriptors that have already started the source buffer fetch will continue to be processed. Reassertion of the run bit will result in resetting internal engine state and should only be done when the engine is quiesced.
    - Descriptors are received from either the descriptor engine directly or the Descriptor Bypass Input interface. Any queue that is in internal mode should not be given descriptors through the Descriptor Bypass Input interface.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/AXI-Memory-Mapped-Descriptor-for-H2C-and-C2H-32B

    - Internal mode memory mapped DMA must configure the descriptor queue to be 32B and follow the above descriptor format.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/Internal-and-Bypass-Modes

    - If the packet is present in host memory in non-contiguous space, then it has to be defined by more than one descriptor, and this requires that the queue be programmed in bypass mode.
    - When fcrd_en is enabled in the software context, DMA will wait for the user application to provide credits. When fcrd_en is not set, the DMA uses a pointer update, fetches descriptors and sends the descriptor out. The user application should not send in credits.
    - There are some requirements imposed on the user logic when using the bypass mode. Because the bypass mode allows a packet to span multiple descriptors, the user logic needs to indicate to QDMA which descriptor marks the Start-Of-Packet (SOP) and which marks the End-Of-Packet (EOP).
    - At the QDMA H2C Stream bypass-in interface, among other pieces of information, the user logic needs to provide: Address, Length, SOP, and EOP. It is required that once the user logic feeds SOP descriptor information into QDMA, it must eventually feed EOP descriptor information also. Descriptors for these multi-descriptor packets must be fed in sequentially.
    - Other descriptors not belonging to the packet must not be interleaved within the multi-descriptor packet. The user logic must accumulate the descriptors up to the EOP descriptor, before feeding them back to QDMA. Not doing so can result in a hang.
    - The QDMA will generate a TLAST at the QDMA H2C AXI4-Stream data output once it issues the last beat for the EOP descriptor. This is guaranteed because the user is required to submit the descriptors for a given packet sequentially.
    - Quality of service can be severely affected if the packet sizes are large. The Stream engine is designed to saturate PCIe for packet sizes as low as 128B, so AMD recommends that you restrict the packet size to be host page size or maximum transfer unit as required by the user application.
    - A performance control provided in the H2C Stream Engine is the ability to stall requests from being issued to the PCIe RQ/RC if a certain amount of data is outstanding on the PCIe side as seen by the H2C Stream Engine. To use this feature, the SW must program a threshold value in the H2C_REQ_THROT (0xE24) register.


.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/Handling-Descriptors-With-Errors?tocId=C1GC1G3Yi2rqbFYc6COtCg

    - For a queue in bypass mode, it is the responsibility of the user logic to not issue a batch of descriptors with an error descriptor. Instead, it must send just one descriptor with error input asserted on the H2C Stream bypass-in interface and set the SOP, EOP, no_dma signal, and sdi or mrkr-req signal to make the H2C Stream Engine send a writeback to Host.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/C2H-Stream-Engine?tocId=iIzB4_5EQe28ijZNG1QubA

    - The QDMA requires software to post full ring size so the C2H stream engine can fetch the needed number of descriptors for all received packets. If there are not enough descriptors in the descriptor ring, the QDMA will stall the packet transfer. For performance reasons, the software is required to post the PIDX as soon as possible to ensure there are always enough descriptors in the ring.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/C2H-Stream-Modes

    - If you already have the descriptor cached on the device, there is no need to fetch one from the host and you should follow the simple bypass mode for the C2H Stream application. In simple bypass mode, do not provide credits to fetch the descriptor, and instead, you need to send in the descriptor on the descriptor bypass interface.
    - For simple bypass transfer to work, a prefetch tag is needed and it can be fetched from the QDMA IP.
    - The user application must request a prefetch tag before sending any traffic for a simple bypass queue through the C2H ST engine. Invalid queues or non-bypass queues should not request any tags using this method, as it might reduce performance by freezing tags that never get used.
    - For the queues that share the same prefetch tag, the data and descriptors need to come in the same order. For Simple Bypass, the data and descriptors are both controlled by the user, so they need to guarantee the order is maintained.
    - If a current qid is invalidated, a new prefetch tag must be requested with a valid qid.
    - Prefetched tag must be assigned to input port c2h_byp_in_st_csh_pfch_tag[6:0] for all transfers.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/C2H-Stream-Packet-Type

    - dma<n>_s_axis_c2h_mty = empty byte should be set in last beat.
    - dma<n>_s_axis_c2h_cmpt_ctrl_wait_pld_pkt_id = This completion packet has to wait for the data packet with this ID to be sent before the CMPT packet can be sent.
    - When the user application sends the data packet, it must count the packet ID for each packet. The first data packet has a packet ID of 1, and it increments for each data packet.
    - For the regular C2H packet, the data packet and the completion packet is a one-to-one match. Therefore, the number of data packets with dma<n>_s_axis_c2h_ctrl_has_cmpt as 1'b1 should be equal to the number of CMPT packets with dma<n>_s_axis_c2h_cmpt_ctrl_cmpt_type as HAS_PLD.
    - Depth and width of the FIFO depends on the use case. Width is dependent on the largest CMPT size for the application, and depth is dependent on performance needs. For best performance for 64 Byte CMPT, a depth of 512 is recommended.
    - The immediate data packet and the marker packet do not consume the descriptor; instead, they write to the C2H Completion Ring. The software needs to size the C2H Completion Ring large enough to accommodate the outstanding immediate packets and the marker packets.
    - Zero Byte packets are not supported in Internal mode and Cache bypass mode. The QDMA might hang if zero byte packets are dropped due to not available descriptors. Zero Byte Packets are supported in Simple bypass mode.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/C2H-Stream-Modes

    - When prefetch mode is enabled, the user application cannot send credits as input in QDMA Descriptor Credit input ports.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/Completion-Engine?tocId=N~lHogTrZWEFBwBMSKiHgw

    - The user-defined portion of the CMPT packet typically needs to specify the length of the data packet transferred and whether or not descriptors were consumed as a result of the data packet transfer. Immediate and marker type packets do not consume any descriptors. The exact contents of the user-defined data are up to the user to determine.
    - Maximum buffer size register 0xB50 bits[31:26] is programmed to 0 (default value). This value might result in an overflow depending on the simulator or the synthesis tool used. To avoid overflow, set 0xB50 bits[31:26] to maximum value of 63.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/Completion-Status-Structure

    - In order to make the QDMA Subsystem for PCIe write Completion Status to the Completion ring, Completion Status must be enabled in the Completion context.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/Completion-Context-Structure

    - baddr4_low: Since the minimum alignment supported is 64B in this case, this field must be 0.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/Slave-Bridge

    - If slave reads and writes are valid, IP prioritizes reads over writes. You are recommended to have proper arbitration (leave some gaps between reads so writes can pass through).

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/Slave-Address-Translation-Examples

    - The slave bridge does not support narrow burst AXI transfers. To avoid narrow burst transfers, connect the AXI smart-connect module which will convert narrow burst to full burst AXI transfers.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/Function-Map-Table

    - Along with FMAP table programming in the IP, you must program the FMAP table in the Mailbox IP. This is needed for function level reset (FLR) procedure.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/Queue-Setup

    - Set-up Completion Context. If interrupts/status writes are desired (enabled in the Completion Context), an initial Completion CIDX update is required to send the hardware into a state where it is sensitive to trigger conditions. This initial CIDX update is required, because when out of reset, the hardware initializes into an unarmed state.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/Function-Level-Reset

    - When a VF is reset, only the resources associated with this VF are reset. When a PF is reset, all resources of the PF, including that of its associated VFs, are reset. Because FLR is a privileged operation, it must be performed by the PF driver running in the management system.
    - Quiesce: The software must ensure all pending transaction is completed. This can be done by polling the Transaction Pending bit in the Device Status register (in PCIe Configuration Space), until it is cleared or times out after a certain period of time.
    - Initiate Function Level Reset bit (bit 15 of PCIe Device Control Register) of the target function should be set to 1 to trigger FLR process in PCIe.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/Host-Profile

    - Host profile must be programmed to represent root port host. Host profile can be programmed through context programming.
    - H2C AXI4-MM steering bit and C2H AXI4-MM steering bit should set to 0s. If not, DMA AXI4-MM transfers do not work. For most cases, host profile context structure is all 0s, and host profile must still be programmed to represent a host.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/Resets

    - After soft_reset, you must reinitialize the queues and program all queue context.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/Expansion-ROM

    - The maximum size for the Expansion ROM BAR should be no larger than 16 MB. Selecting an address space larger than 16 MB can result in a non-compliant core.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/Data-Path-Errors

    - Parity errors are not recoverable and can result in unexpected behavior. Any DMA during and after the parity error should be considered invalid.
    - If there is a parity error and transfer hangs or stops, the DMA will log the error. You must investigate and fix the parity issues. Once the issues are fixed, clear that queue and reopen the queue to start a new transfer.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/QDMA-Global-Ports

    - sys_clk should be driven by the ODIV2 port of reference clock IBUFDS_GTE4.
    - PCIe reference clock should be driven from the port of reference clock IBUFDS_GTE4.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/AXI-Bridge-Slave-Ports

    - Only the INCR burst type is supported.
    - s_axib_wstrb can be equal to 0 in the beginning of a valid data cycle and will appropriately calculate an offset to the given address. However, the valid data identified by s_axib_wstrb must be continuous from the first byte enable to the last byte enable.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/QDMA-Descriptor-Bypass-Input-Ports

    - QDMA hangs if the last descriptor without h2c_byp_in_st_sdi has an error. This results in a missing writeback and hw_ctxt.dsc_pend bit that are asserted indefinitely.
    - For performance reasons, AMD recommends that this port be asserted once in 32 or 64 descriptors and assert at the last descriptor if there are no more descriptors left.
    - In Cache Bypass mode, you must loop back c2h_byp_out_pfch_tag[6:0] to c2h_byp_in_st_csh_pfch_tag[6:0]. In Simple Bypass mode, you need to pass in the Prefetch tag value from MDMA_C2H_PFCH_BYP_TAG (0x140C) register.
    - AXI4-Stream C2H Simple Bypass mode and Cache Bypass mode both use the same bypass ports, c2h_byp_in_st_csh_*.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/QDMA-Descriptor-Bypass-Output-Ports

    - h2c_byp_out_rdy: When this interface is not used, Ready must be tied-off to 1.
    - h2c_byp_out_cidx [15:0]: The ring index of the descriptor fetched. The User must echo this field back to QDMA when submitting the descriptor on the bypass-in interface.
    - c2h_byp_out_cidx [15:0]: The ring index of the descriptor fetched. The User must echo this field back to QDMA when submitting the descriptor on the bypass-in interface.
    - c2h_byp_out_rdy: When this interface is not used, Ready must be tied-off to 1.
    - When Descriptor bypass option is selected in the AMD Vivado™ IDE but the descriptor bypass bit is not set in context programming, you will see valid signals getting asserted with CIDX updates.


.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/QDMA-Descriptor-Credit-Input-Ports

    - dsc_crdt_in_fence: The fence bit should only be set for a queue that is enabled and has both descriptors and credits available; otherwise, a hang condition might occur.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/QDMA-Traffic-Manager-Credit-Output-Ports

    - tm_dsc_sts_rdy: When this interface is not used, Ready must be tied-off to 1.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/Queue-Status-Ports

    - qsts_out_rdy: Ready must be tied to 1 so status output will not be blocked. Even if this interface is not used, the ready port must be tied to 1.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/Registering-Signals

    - To simplify timing and increase system performance in a programmable device design, keep all inputs and outputs registered between the user application and the subsystem. This means that all inputs and outputs from the user application should come from, or connect to, a flip-flop. While registering signals might not be possible for all paths, it simplifies timing analysis and makes it easier for the AMD tools to place and route the design.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/Recognize-Timing-Critical-Signals

    - The constraints provided with the example design identify the critical signals and timing constraints that should be applied.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/Make-Only-Allowed-Modifications

    - You should not modify the subsystem. Any modifications can have adverse effects on system timing and protocol compliance. Supported user configurations of the subsystem can only be made by selecting the options in the customization IP dialog box when the subsystem is generated.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/AXI-BARs-Tab

    - No Address Translation: When this option is selected, the DMA will not do any address translation. One full 64-bit BAR space is provided, and you are responsible for any address translation if required. When address translation is required by DMA, do not select this option.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/PCIe-DMA-Tab

    - CMPT Coalesce Max buffer:
        - Completion (CMPT) Coalesce Max buffer supports up to 64 buffers. Select one of 16 or 32 (default 16). Each entry of the CMPT Coalesce Buffer coalesces multiple Completions (up to 64B) to form a single queue before writing to the host to improve bandwidth utilization. A deeper CMPT Coalesce Buffer allows coalescing within more queues but will increase the area as a downside.
    - Data Protection:
        - When Data Protection is not enabled:
            - You must always give the parity on CMPT.
        - When Data Protection is enabled:
            - You must send CRC/ECC values on C2H data and the control interface.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/Example-Design-with-Descriptor-Bypass-In/Out-Loopback

    - After the setup initial C2H stream data transfer, the prefetch tag is valid until the qid is valid. When the current qid becomes invalid, you must generate a new tag.

.. note::
    :class: highlight-box

    https://docs.amd.com/r/en-US/pg302-qdma/Using-the-Drivers

    - Note: Starting from the 2022.1 release of the Linux driver for QDMA, if a design is using streaming queues, they must be explicitly enabled through API as they are not configured at module load.

