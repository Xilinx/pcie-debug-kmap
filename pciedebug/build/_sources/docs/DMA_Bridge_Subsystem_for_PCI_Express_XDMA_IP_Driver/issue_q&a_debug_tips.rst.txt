.. .. _dma_bridge_xdma_ip_issues_answers:

.. Issues and Answers
.. ==================

.. _dma_bridge_xdma_ip_debug_tips_questions:

Issues/Debug Tips/Questions
===========================

        * DMA uses PCIe Base IP and GT similar to the base PCIe Integrated IP. 
            * If there are issues related to link up, enumeration, general PCIe boot-up, or a detection issue, please follow the PCIe debug strategy similar to the base PCIe Integrated IP. 
        * Driver fails to load
            * Enable debug in the driver. Check the output of the dmesg command to help narrow down where the issue is.Once it has been narrowed down to which function calls it fails, do a PIO transfer to read or write to the particular register that the driver is accessing to see what response you get. The primary section to look for is the probe function inside of the xdma-core.c file. This probe function is called when you insert the driver into the Kernel and will read various DMA status registers to indicate which features are available and set an initialization value to it.
        * XDMA driver hangs when doing C2H streaming transfer
            * Check by loading the driver into the kernel by enabling the descriptor "credit based". Modify the Insmod command in the load_driver.sh file as follows before sourcing it.
                * insmod ../driver/xdma.ko enable_credit_mp=1
