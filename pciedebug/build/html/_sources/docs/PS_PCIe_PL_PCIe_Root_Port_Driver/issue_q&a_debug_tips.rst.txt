.. .. _dma_bridge_rootport_issues_answers:

.. Issues and Answers
.. ==================

.. _dma_bridge_rootport_issues_debug_tips_questions:

Issues/Debug Tips/Questions
===========================

* Mellanox PCIe NIC card is connected to the PCIe slot on ZCU102 board.  During Linux boot up, the Mellanox card ("Connect4-Lx”) is recognized and associated with the mlx5 driver, which starts its probe process. However, the probe encounters problem when allocating interrupts and fails. Below is an excerpt from "dmesg", showing the Mellanox card being recognized during boot by its mlx5 driver and probed: 
    * [    4.940428] pci 0000:01:00.0: enabling device (0000 -> 0002) 
    * [    4.946132] pci 0000:01:00.0: mellanox_check_broken_intx_masking+0x0/0x1b8 took 11111 usecs 
    * [    4.954572] mlx5_core 0000:01:00.0: assign IRQ: got 51 
    * [    4.954852] mlx5_core 0000:01:00.0: enabling bus mastering 
    * [    4.954929] mlx5_core 0000:01:00.0: firmware version: 14.26.1040 
    * [    4.960966] mlx5_core 0000:01:00.0: 16.000 Gb/s available PCIe bandwidth, limited by 5 GT/s x4 link at 0000:00:00.0 (capable of 63.008 Gb/s with 8 GT/s x8 link) 
    * [    5.278293] mlx5_core 0000:01:00.0: mlx5_load:1068:(pid 26): Failed to alloc IRQs 
    * [    6.003727] mlx5_core 0000:01:00.0: init_one:1349:(pid 26): mlx5_load_one failed with error code -28 
    * [    6.012869] mlx5_core 0000:01:00.0: disabling bus mastering 
    * [    6.012981] mlx5_core: probe of 0000:01:00.0 failed with error -28 
    * [    6.019199] pci 0000:01:00.1: enabling device (0000 -> 0002) 
    * [    6.025095] mlx5_core 0000:01:00.1: assign IRQ: got 52 
    * [    6.025685] mlx5_core 0000:01:00.1: enabling bus mastering 
    * [    6.025765] mlx5_core 0000:01:00.1: firmware version: 14.26.1040 
    * [    6.031808] mlx5_core 0000:01:00.1: 16.000 Gb/s available PCIe bandwidth, limited by 5 GT/s x4 link at 0000:00:00.0 (capable of 63.008 Gb/s with 8 GT/s x8 link) 
    * [    6.349033] mlx5_core 0000:01:00.1: mlx5_load:1068:(pid 26): Failed to alloc IRQs 
    * [    7.072889] mlx5_core 0000:01:00.1: init_one:1349:(pid 26): mlx5_load_one failed with error code -28 
    * [    7.082038] mlx5_core 0000:01:00.1: disabling bus mastering 
    * [    7.082160] mlx5_core: probe of 0000:01:00.1 failed with error -28 
    * Answer: 
        * PS-PCIe in Root Port mode does not support receiving MSI-X interrupts. 
* Can PS-PCIe® on ZU+ in Endpoint mode meet 100ms requirement?
    * When using PS-PCIe® on ZU+ in Endpoint mode, running FSBL is enough to set up the block for endpoint mode operation. FSBL should be able to program the PS/PS-PCIe® and GTR within 100 ms. However, this doesn’t include PL-bitstream programming as including that would make this greater than 100 ms. (Ref: UG1137)
* Link Down: Endpoint is not detected (PS-PCIe)
    * Ensure the Endpoint card has fit in properly on the PCIe slot of the Rootport.
    * Check the link status by doing ‘devmem 0xfd480238’ in the Linux prompt.
        * Value should be 0x3 if the link is up https://www.xilinx.com/html_docs/registers/ug1087/ug1087-zynq-ultrascale-registers.html#pcie_attrib___attr_101.html
    * Read LTSSM state from 0xfd480228. 8 down to 3 gives the LTSSM coding. For LTSSM coding values, see (PG054).
    * If the link is found to be up via reading PCIe Status register but the device is not seen in 'lspci':
        * Check if the endpoint card was programmed before Rootport booted
        * Do a rescan of the PCIe bus by using the following command on ZCU102 Linux prompt
            * $ echo 1 > /sys/bus/pci/devices/0000:01:00.0/remove
            * $ echo 1 > /sys/bus/pci/rescan
    * If the rescan did not help:
        * Try with the latest FSBL.
        * Test on some other platform (x86) just to confirm the EP card has no issues.
    * When EP is reset without resetting RP, the LTSSM on RP goes through either the Disabled or hot-reset states which are known to have some chances of link failures with errors.
        * Issue Rootport Reset (0xFD1A0100 bit 17 pcie_ctrl_reset only, this is like PERST) https://www.xilinx.com/html_docs/registers/ug1087/ug1087-zynq-ultrascaleregisters.html#crf_apb___rst_fpd_top.html
* nwl-pcie fd0e0000.pcie: Unsupported request Detected (PS-PCIe)
    * If the following error is reported when trying to initiate DMA from a third party GPU, make sure to enable the bus mastering bit in the root port as well as endpoint (EP).
        * [ 3130.483591] nwl-pcie fd0e0000.pcie: Unsupported request Detected
        * [ 3130.490863] nwl-pcie fd0e0000.pcie: Non-Fatal Error Detected
        * The Bus mastering bit can be set by using the following commands:
            * setpci -s 00:00.0 COMMAND=0x4
            * setpci -s 01:00.0 COMMAND=0x7
            * setpci -s 01:00.1 COMMAND=0x7
            * Note: 
                * confirm the bus structure before executing the above command.
                * In the kernel EP driver flow, when EP driver invokes "pci_enable_device_mem" API, the bus mastering bit will be set. If there is no driver, it has to be done manually. On an x86 machine this bit is set by the BIOS, on ARM system this needs to be enabled using the above API.



