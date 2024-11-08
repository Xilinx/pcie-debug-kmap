.. _PG302_important_design_considerations:


Important Design Considerations from PG302
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
