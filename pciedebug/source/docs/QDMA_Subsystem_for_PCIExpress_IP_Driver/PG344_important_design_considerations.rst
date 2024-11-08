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

