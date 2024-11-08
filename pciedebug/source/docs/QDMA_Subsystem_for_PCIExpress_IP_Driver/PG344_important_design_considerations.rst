.. _PG344_important_design_considerations:


Important Design Considerations from PG344
==========================================

.. container:: Introduction

    When tackling design issues, navigating a lengthy product guide can be overwhelming. To streamline your troubleshooting process, this article lists essential design considerations directly from the product guide, highlighting key points to keep in mind. This article condenses over 200 pages into a focused, 10-12 page summary, making it easier to access the most relevant information quickly. For full details, be sure to click the provided link to explore each consideration further in the original product guide.

.. note::
   :class: highlight-box

   **Link**: `Resets <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/Resets?tocId=h1v7mXtUbwccVJ7YmHm9Jg>`_

   - If your board is designed to use the same PCIe edge connectors to operate with CPM and PL PCIE, then AMD recommend using PS reset using the Control Interface and Processing System (CIPS) IP core.

.. note::
   :class: highlight-box

   **Link**: `Descriptor Engine <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/Descriptor-Engine?tocId=zPLzp4_2KsOEq1OQBKu9mA>`_

   - The descriptor engine will have only one DMA read outstanding per queue at a time and can read as many descriptors as can fit in a queue is associated with interrupt aggregation, AMD recommends that the status descriptor be turned off, and instead the DMA status be received from the interrupt aggregation ring.

.. note::
   :class: highlight-box

   **Link**: `H2C Stream Engine <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/H2C-Stream-Engine?tocId=8B1g_MoJi9B~JYNKl5PE8w>`_

   - The total length of all descriptors put together must be less than 64 KB.
   - A packet with multiple descriptors straddling is not allowed due to the lack of per queue storage.

.. note::
   :class: highlight-box

   **Link**: `C2H Stream Engine <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/C2H-Stream-Engine?tocId=kLLbtbEfgONrsb5bysKeHQ>`_

   - In Simple Bypass Mode, the engine does not track anything for the queue, and the user logic can define its own method to receive descriptors. The user logic is then responsible for delivering the packet and associated descriptor through the simple bypass interface.
   - The ordering of the descriptors fetched by a queue in the bypass interface and the C2H stream interface must be maintained across all queues in bypass mode.

.. note::
   :class: highlight-box

   **Link**: `AXI Memory Mapped Bridge Master Interface <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/AXI-Memory-Mapped-Bridge-Master-Interface>`_

   - The AXI MM Bridge Master interface is used for high bandwidth access to AXI Memory Mapped space from the host. The interface supports up to 32 outstanding AXI reads and writes. One or more PCIe BAR of any physical function (PF) or virtual function (VF) can be mapped to the AXI-MM bridge master interface. This selection must be made prior to design compilation.
   - Note that all VFs belonging to the same PF share the same PCIe to AXI translation vector. Therefore, the AXI address space of each VF is concatenated together. Use VFG_OFFSET to calculate the actual starting address of AXI for a particular VF.

.. note::
   :class: highlight-box

   **Link**: `AXI Memory Mapped Bridge Slave Interface <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/AXI-Memory-Mapped-Bridge-Slave-Interface>`_

   - The AXI-MM Bridge Slave interface is used for high bandwidth memory transfers between the user logic and the Host. AXI to PCIe translation is supported through the AXI to PCIe BARs. The interface will split requests as necessary to obey PCIe MPS and 4 KB boundary crossing requirements. Up to 32 outstanding read and write requests are supported.

.. note::
   :class: highlight-box

   **Link**: `Interrupt Module <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/Interrupt-Module>`_

   - Queue-based interrupts and user interrupts are allowed on PFs and VFs, but error interrupts are allowed only on PFs.
   
.. note::
   :class: highlight-box

   **Link**: `General Design of Queues <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/General-Design-of-Queues>`_

   - PIDX update should never be equal to CIDX. For this case, if CIDX is 0, the maximum PIDX update would be 6.

.. note::
   :class: highlight-box

   **Link**: `QDMA Subsystem Limitations <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/QDMA-Subsystem-Limitations>`_

   - Use AXI SmartConnect to support Narrow Burst.
   - ECC and Slave Narrow Burst support is mutually exclusive.
   - If you want an ECC feature, the recommendation is to up-size your AXI Master externally.

.. note::
   :class: highlight-box

   **Link**: `Performance and Resource Utilization <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/Performance-and-Resource-Utilization?tocId=6QfBEhQFba9swgGuwrX89A>`_

   - Following are the QDMA register settings recommended by AMD for better performance. Performance numbers can vary based on systems and OS used.
   - AMD recommends that you limit the total outstanding descriptor fetch to be less than 8 KB on the PCIe. For example, limit the outstanding credits across all queues to 512 for a 16B descriptor.

.. note::
   :class: highlight-box

   **Link**: `Descriptor Context <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/Descriptor-Context>`_

   - After the queue is enabled, the software context should only be updated through the direct mapped address space to update the Producer Index and Interrupt Arm® bit, unless the queue is being disabled.
   - The hardware context and credit context contain only status. It is only necessary to interact with the hardware and credit contexts as part of queue initialization to clear them to all zeros.

.. note::
   :class: highlight-box

   **Link**: `Software Descriptor Context Structure 0x0-C2H and 0x1-H2C <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/Software-Descriptor-Context-Structure-0x0-C2H-and-0x1-H2C>`_

   - If bypass mode is not enabled, 32B is required for Memory Mapped DMA, 16B is required for H2C Stream DMA, and 8B is required for C2H Stream DMA.

.. note::
   :class: highlight-box

   **Link**: `Credit Descriptor Context Structure <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/Credit-Descriptor-Context-Structure>`_

   - The credit descriptor context is for internal DMA use only and it can be read from the indirect bus for debug.

.. note::
   :class: highlight-box

   **Link**: `Descriptor Fetch <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/Descriptor-Fetch>`_

   - If fetch crediting is enabled, the user logic is required to provide a credit for each descriptor that should be fetched.
   - If queue size is 8, which contains the entry index 0 to 7, the last entry (index 7) is reserved for status. This index should never be used for the PIDX update, and the PIDX update should never be equal to CIDX. For this case, if CIDX is 0, the maximum PIDX update would be 6.

.. note::
   :class: highlight-box

   **Link**: `Internal Mode Writeback and Interrupts AXI-MM and H2C-ST <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/Internal-Mode-Writeback-and-Interrupts-AXI-MM-and-H2C-ST>`_

   - It is recommended the wbi_chk bit be set for all internal mode operation, including when interval mode is enabled.

.. note::
   :class: highlight-box

   **Link**: `Descriptor Bypass Mode <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/Descriptor-Bypass-Mode?tocId=OnYtjyqAgfk9Lr7lh5_z4w>`_

   - To perform DMA operations, the user logic drives descriptors (must be QDMA format) into the descriptor bypass input interface.

.. note::
   :class: highlight-box

   **Link**: `Descriptor Bypass Mode Writeback/Interrupts <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/Descriptor-Bypass-Mode-Writeback/Interrupts>`_

   - Once a descriptor with the sdi bit is sent, another irq_arm assertion must be observed before another descriptor with the sdi bit can be sent.
   - If you set the sdi bit when the arm bit is not properly observed, an interrupt might or might not be sent, and software might hang indefinitely waiting for an interrupt.
   - When interrupts are not enabled, setting the sdi bit has no restriction. However, excessive writeback events can severely reduce the descriptor engine performance and consume write bandwidth to the host.

.. note::
   :class: highlight-box

   **Link**: `Traffic Manager Output Interface <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/Traffic-Manager-Output-Interface>`_

   - While the tm_dsc_sts interface is a valid/ready interface, it should not be back-pressured for optimal performance. Since multiple events trigger a tm_dsc_sts cycle, if internal buffering is filled, descriptor fetching will be halted to prevent generation of new events.

.. note::
   :class: highlight-box

   **Link**: `Errors <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/Errors?tocId=tsnP~q1s6VOFgCB4k~4Hlw>`_

   - After the queue is invalidated, if there is an error you can determine the cause by reading the error registers and context for that queue. You must clear and remove that queue, and then add the queue back later when needed.
   - If the descriptor fetch itself encounters an error, the descriptor will be marked with an error bit. If the error bit is set, the contents of the descriptor should be considered invalid.

.. note::
   :class: highlight-box

   **Link**: `Memory Mapped DMA <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/Memory-Mapped-DMA>`_

   - PCIe-to-PCIe, and AXI MM-to-AXI MM DMAs are not supported.

.. note::
   :class: highlight-box

   **Link**: `Operation <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/Operation>`_

   - Any descriptors that have already started the source buffer fetch will continue to be processed. Reassertion of the run bit will result in resetting internal engine state and should only be done when the engine is quiesced.
   - Once sufficient read completion data is received, the write request will be issued to the destination interface in the same order that the read data was requested. Before the request is retired, the destination interfaces must accept all the write data and provide a completion response.

.. note::
   :class: highlight-box

   **Link**: `AXI Memory Mapped Descriptor for H2C and C2H 32B <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/AXI-Memory-Mapped-Descriptor-for-H2C-and-C2H-32B>`_

   - Internal mode memory mapped DMA must configure the descriptor queue to be 32B and follow the above descriptor format. In bypass mode, the descriptor format is defined by the user logic, which must drive the H2C or C2H MM bypass input port.

.. note::
   :class: highlight-box

   **Link**: `Internal and Bypass Modes <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/Internal-and-Bypass-Modes>`_

   - If the packet is present in host memory in non-contiguous space, then it has to be defined by more than one descriptor, and this requires that the queue be programmed in bypass mode.
   - When fcrd_en is enabled in the software context, DMA will wait for the user application to provide credits. When fcrd_en is not set, the DMA uses a pointer update, fetches descriptors and sends the descriptor out. The user application should not send in credits.
   - Because the bypass mode allows a packet to span multiple descriptors, the user logic needs to indicate to QDMA which descriptor marks the Start-Of-Packet (SOP) and which marks the End-Of-Packet (EOP).
   - At the QDMA H2C Stream bypass-in interface, among other pieces of information, the user logic needs to provide: Address, Length, SOP, and EOP. It is required that once the user logic feeds SOP descriptor information into QDMA, it must eventually feed EOP descriptor information also.
   - Descriptors for these multi-descriptor packets must be fed in sequentially. Other descriptors not belonging to the packet must not be interleaved within the multidescriptor packet.
   - The user logic must accumulate the descriptors up to the EOP descriptor, before feeding them back to QDMA. Not doing so can result in a hang. The QDMA will generate a TLAST at the QDMA H2C AXI4-Stream data output once it issues the last beat for the EOP descriptor. This is guaranteed because the user is required to submit the descriptors for a given packet sequentially.
   - The Stream engine is designed to saturate PCIe for packet sizes as low as 128B, so AMD recommends that you restrict the packet size to be host page size or maximum transfer unit as required by the user application.

.. note::
   :class: highlight-box

   **Link**: `H2C Stream Descriptor 16B <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/H2C-Stream-Descriptor-16B>`_

   - This H2C descriptor format is only applicable for internal mode.

.. note::
   :class: highlight-box

   **Link**: `Descriptor Metadata <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/Descriptor-Metadata>`_

   - Passing metadata on the tuser is not supported for a queue in bypass mode and consequently there is no input to provide the metadata on the QDMA H2C Stream bypass-in interface.

.. note::
   :class: highlight-box

   **Link**: `Zero Length Descriptor <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/Zero-Length-Descriptor>`_

   - The user logic must set both the SOP and EOP for a zero byte descriptor. If not done, an error will be flagged by the H2C Stream Engine.

.. note::
   :class: highlight-box

   **Link**: `H2C Stream Status Descriptor Writeback <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/H2C-Stream-Status-Descriptor-Writeback>`_

   - The format of the H2C-ST status descriptor written to the descriptor ring is different from that written into the interrupt coalesce entry.

.. note::
   :class: highlight-box

   **Link**: `Handling Descriptors With Errors <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/Handling-Descriptors-With-Errors?tocId=tis~ohdvqbaLPsOGV4YzOQ>`_

   - For a queue in bypass mode, it is the responsibility of the user logic to not issue a batch of descriptors with an error descriptor. Instead, it must send just one descriptor with error input asserted on the H2C Stream bypass-in interface and set the SOP, EOP, no_dma signal, and sdi or mrkr-req signal to make the H2C Stream Engine send a writeback to Host.

.. note::
   :class: highlight-box

   **Link**: `C2H Stream Engine <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/C2H-Stream-Engine?tocId=rz5SyePZBHI8hPsLfqBusg>`_

   - The buffer size is fixed per queue basis.
   - The QDMA requires software to post full ring size so the C2H stream engine can fetch the needed number of descriptors for all received packets.
   - For performance reasons, the software is required to post the PIDX as soon as possible to ensure there are always enough descriptors in the ring.

.. note::
   :class: highlight-box

   **Link**: `C2H Prefetch Engine <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/C2H-Prefetch-Engine>`_

   - sw_crdt (Software credit): The software must initialize it to 0 and then treat it as read-only.

.. note::
   :class: highlight-box

   **Link**: `C2H Stream Modes <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/C2H-Stream-Modes>`_

   - The descriptors from the C2H bypass input interfaces have one interface for both simple mode and cache mode (note that both simple bypass and cache bypass use the same interface).
   - If you already have the descriptor cached on the device, there is no need to fetch one from the host and you should follow the simple bypass mode for the C2H Stream application. In simple bypass mode, do not provide credits to fetch the descriptor, and instead, you need to send in the descriptor on the descriptor bypass interface.
   - AXI4-Stream C2H Simple Bypass mode and Cache Bypass mode both use same bypass in ports (c2h_byp_in_st_csh_* ports).
   - For simple bypass transfer to work, a prefetch tag is needed and it can be fetched from the QDMA IP.
   - The user application must request a prefetch tag before sending any traffic for a simple bypass queue through the C2H ST engine. Invalid queues or non-bypass queues should not request any tags using this method, as it might reduce performance by freezing tags that never get used.
   - The prefetch tag needs to be reserved upfront before any traffic can start. One prefetch tag per target host is required.
   - In most applications, one prefetch tag for a host is needed. In Simple Bypass mode, the tag is not tied to any descriptor ring. For the queues that share the same prefetch tag, the data and descriptors need to come in the same order.
   - For Simple Bypass, the data and descriptors are both controlled by the user, so they need to guarantee the order is maintained. For example when the data stream has packets in the order of Q0, Q1, and Q2, on descriptor input, you cannot send Q1, Q2, Q0 etc. The order needs to be maintained.
   - The user application writes to the MDMA_C2H_PFCH_BYP_QID (0x1408) register with the qid for a simple bypass queue, then reads from MDMA_C2H_PFCH_BYP_TAG (0x140C) register to retrieve the corresponding prefetch tag. This tag must be driven with all bypass_in descriptors for as long as the current qid is valid. If a current qid is invalidated, a new prefetch tag must be requested with a valid qid.
   - The prefetch tag points to the CAM that stores the active queues in the prefetch engine. Also the qid that was used to prefetch tag needs to be used as the qid for all simple bypass packets. Assign the qid to dma_s_axis_c2h_ctrl_qid.
   - The prefetch tag and the qid that was used to fetch the tag should be used for all simple bypass packets. This information needs to be communicated to the user side.
   - The c2h_byp_in_st_csh_pfch_tag[6:0] port can have the same prefetch_tag for as long as the original qid is valid.
   - No sequence is required between descriptor bypass in, data payload, and completion packets.
   - When prefetch mode is enabled, the user application cannot send credits as input in QDMA Descriptor Credit input ports.
   - In cache bypass mode, prefetch tag is maintained by the IP internally. Signal c2h_byp_out_pfch_tag[6:0] should be looped back as an input c2h_byp_in_st_csh_pfch_tag[6:0]. The prefetch tag points to the cam that stores the active queues in the prefetch engine.
   - No sequence is required between payload and completion packets.

.. note::
   :class: highlight-box

   **Link**: `Handling Descriptors With Errors <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/Handling-Descriptors-With-Errors?tocId=GpF6nsAjv5m5zjd_It3VnA>`_

   - For a queue in bypass mode, it is the responsibility of the user logic to not issue a batch of descriptors with an error descriptor. Instead, it must send just one descriptor with error input asserted on the C2H Stream bypass-in interface and set the SOP, EOP, no_dma signal, and sdi or mrkr_req signal to make the C2H Stream Engine send a writeback to Host.

.. note::
   :class: highlight-box

   **Link**: `Completion Engine <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/Completion-Engine?tocId=Ceq2lA4K4EK694IQPAqrAg>`_

   - Although not a requirement, a CMPT is typically used with a C2H queue.
   - The user-defined portion of the CMPT packet typically needs to specify the length of the data packet transferred and whether or not descriptors were consumed as a result of the data packet transfer. 
   - Maximum buffer size register 0xB50 bits[31:26] is programmed to 0 (default value). This value might result in an overflow depending on the simulator or the synthesis tool used. To avoid overflow, set 0xB50 bits[31:26] to maximum value of 63.

.. note::
   :class: highlight-box

   **Link**: `Completion Context Structure <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/Completion-Context-Structure>`_

   - baddr4_low: Since the minimum alignment supported is 64B in this case, this field must be 0.
   - pidx: Completion Ring Producer Index. This is a field written by the hardware. The software must initialize it to 0 and then treat it as read-only. Color bit to be used on Completion.

.. note::
   :class: highlight-box

   **Link**: `Completion Status Structure <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/Completion-Status-Structure>`_

   - In order to make the QDMA write Completion Status to the Completion ring, Completion Status must be enabled in the Completion context.

.. note::
   :class: highlight-box

   **Link**: `Completion Status/Interrupt Moderation <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/Completion-Status/Interrupt-Moderation>`_

   - When in TRIGGER_EVERY, TRIGGER_USER, and TRIGGER_USER_COUNT mode, the software must read all the Completion entries in the Completion ring as indicated by an interrupt (or a Completion Status write). 

.. note::
   :class: highlight-box

   **Link**: `Address Translation <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/Address-Translation>`_

   - When this option is selected, one full 64-bit BAR space is given for slave data transfer. You must set up any address translation if needed. If No Address Translation is not selected, DMA will do address translation.

.. note::
   :class: highlight-box

   **Link**: `Slave Address Translation Examples <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/Slave-Address-Translation-Examples>`_

   - The slave bridge does not support narrow burst AXI transfers.

.. note::
   :class: highlight-box

   **Link**: `Legacy Interrupt <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/Legacy-Interrupt>`_

   - To enable the legacy interrupt, the software needs to set the en_lgcy_intr bit in the register QDMA_GLBL_GLBL_INTERRUPT_CFG (0x2C4).

.. note::
   :class: highlight-box

   **Link**: `Function Map Table <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/Function-Map-Table>`_

   - Along with FMAP table programming in the IP, you must program the FMAP table in the Mailbox IP. This is needed for function level reset (FLR) procedure.

.. note::
   :class: highlight-box

   **Link**: `Context Programming <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/Context-Programming>`_

   - A host profile table context needs to be programmed before any context settings.

.. note::
   :class: highlight-box

   **Link**: `Queue Setup <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/Queue-Setup>`_

   - If interrupts/status writes are desired (enabled in the Completion Context), an initial Completion CIDX update is required to send the hardware into a state where it is sensitive to trigger conditions. This initial CIDX update is required, because when out of reset, the hardware initializes into an unarmed state.

.. note::
   :class: highlight-box

   **Link**: `Host Profile <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/Host-Profile>`_

   - Host profile must be programmed to represent root port host. Host profile can be programmed through context programming. Select QDMA_CTXT_SELC_HOST_PROFILE (4'hA) in QDMA_IND_CTXT_CMD.
   - H2C AXI4-MM steering bit and C2H AXI4-MM steering bit should be set to 0s. If not, DMA AXI4-MM transfers do not work. For most cases, host profile context structure is all 0s, and host profile must still be programmed to represent a host.

.. note::
   :class: highlight-box

   **Link**: `Resets <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/Resets?tocId=fmctYn5tKkX~OxMI1rYFeg>`_

   - Reset the QDMA logic through the soft_reset_n port. This port needs to be held in reset for a minimum of 100 clock cycles (axi_aclk cycles). This does not reset PCIe hard block. It resets only the DMA portion of logic. This reset can be asserted if there is a DMA hang or some error condition.
   - The use cases that prompt the use of soft_reset include:
     - DMA hangs and user is not getting proper values.
     - DMA transfers have errors, but the PCIe links are good. DMA records some asynchronous error.
   - After soft_reset, you must reinitialize the queues and program all queue context.

.. note::
   :class: highlight-box

   **Link**: `Expansion ROM <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/Expansion-ROM>`_

   - The maximum size for the Expansion ROM BAR should be no larger than 16 MB. Selecting an address space larger than 16 MB can result in a non-compliant core.

.. note::
   :class: highlight-box

   **Link**: `Data Path Errors <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/Data-Path-Errors>`_

   - Any DMA during and after the parity error should be considered invalid. If there is a parity error and transfer hangs or stops, the DMA will log the error. You must investigate and fix the parity issues.

.. note::
   :class: highlight-box

   **Link**: `AXI Bridge Slave Ports <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/AXI-Bridge-Slave-Ports>`_

   - The valid data identified by s_axib_wstrb must be continuous from the first byte enable to the last byte enable.

.. note::
   :class: highlight-box

   **Link**: `AXI4 Stream H2C Ports <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/AXI4-Stream-H2C-Ports>`_

   - m_axis_h2c_tuser_err : If set, indicates the packet has an error. The error could come from the PCIe, or the error could be in the DMA transfer. AMD recommends that you look at the error registers and context for details.

.. note::
   :class: highlight-box

   **Link**: `AXI4 Stream C2H Ports <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/AXI4-Stream-C2H-Ports>`_

   - s_axis_c2h_ctrl_len [15:0] : ctrl_len is in bytes and should be valid in first beat of the packet.
   - s_axis_c2h_mty [5:0] : Empty byte should be set in last beat.

.. note::
   :class: highlight-box

   **Link**: `AXI4 Stream C2H Completion Ports <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/AXI4-Stream-C2H-Completion-Ports>`_

   - HAS_PLD. The CMPT packet has a corresponding payload packet, and it needs to wait for the payload packet to be sent before sending the CMPT packet.
   - s_axis_c2h_cmpt_tvalid must be asserted until s_axis_c2h_cmpt_tready is asserted.

.. note::
   :class: highlight-box

   **Link**: `VDM Ports <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/VDM-Ports>`_

   - When this interface is not used, Ready must be tied-off to 1.

.. note::
   :class: highlight-box

   **Link**: `QDMA Descriptor Bypass Input Ports <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/QDMA-Descriptor-Bypass-Input-Ports>`_

   - For performance reasons, AMD recommends that this port be asserted once in 32 or 64 descriptors and assert at the last descriptor if there are no more descriptors left.
   - If h2c_byp_in_st_no_dma is set, then both h2c_byp_in_st_sop and h2c_byp_in_st_eop must be set.
   - h2c_byp_in_mm_len[27:0] : The DMA data length. The upper 12 bits must be tied to 0. Thus only the lower 16 bits of this field can be used for specifying the length.
   - h2c_byp_in_mm_sdi : For performance reasons, AMD recommends that this port be asserted once in 32 or 64 descriptors and be asserted at the last descriptor if there are no more descriptors left.
   - c2h_byp_in_st_csh_pfch_tag[6:0] : In Cache Bypass mode, you must loop back c2h_byp_out_pfch_tag[6:0] to c2h_byp_in_st_csh_pfch_tag[6:0]. In Simple Bypass mode, user needs to pass in the Prefetch tag value from MDMA_C2H_PFCH_BYP_TAG (0x140C) register.

.. note::
   :class: highlight-box

   **Link**: `QDMA Descriptor Bypass Output Ports <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/QDMA-Descriptor-Bypass-Output-Ports>`_

   - h2c_byp_out_cidx [15:0] : The User must echo this field back to QDMA when submitting the descriptor on the bypass-in interface.
   - h2c_byp_out_rdy : When this interface is not used, Ready must be tied-off to 1.
   - c2h_byp_out_cidx [15:0] : The User must echo this field back to QDMA when submitting the descriptor on the bypass-in interface.
   - c2h_byp_out_rdy : When this interface is not used, Ready must be tied-off to 1.

.. note::
   :class: highlight-box

   **Link**: `QDMA Descriptor Credit Input Ports <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/QDMA-Descriptor-Credit-Input-Ports>`_

   - dsc_crdt_in_vld : When asserted the user must be presenting valid data on the bus and maintain the bus values until both valid and ready are asserted on the same cycle.
   - dsc_crdt_in_fence : The fence bit should only be set for a queue that is enabled, and has both descriptors and credits available, otherwise a hang condition might occur.

.. note::
   :class: highlight-box

   **Link**: `QDMA Traffic Manager Credit Output Ports <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/QDMA-Traffic-Manager-Credit-Output-Ports>`_

   - tm_dsc_sts_rdy : When this interface is not used, Ready must be tied-off to 1.

.. note::
   :class: highlight-box

   **Link**: `Queue Status Ports <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/Queue-Status-Ports>`_

   - qsts_out_rdy : Ready must be tied to 1 so status output will not be blocked. Even if this interface is not used, the ready port must be tied to 1.

.. note::
   :class: highlight-box

   **Link**: `QDMA PF Address Register Space <https://docs.amd.com/r/en-US/pg344-pcie-dma-versal/QDMA-PF-Address-Register-Space>`_

   - When you generate the IP in default mode, not all registers are exposed. For example, debug registers will be missing. Refer to the qdma_v5_0_pf_registers.csv file to identify the debug registers. To expose all registers, use the following tcl command during IP generation:
   - set_property CONFIG.debug_mode {DEBUG_REG_ONLY} [get_ips qdma_0]
