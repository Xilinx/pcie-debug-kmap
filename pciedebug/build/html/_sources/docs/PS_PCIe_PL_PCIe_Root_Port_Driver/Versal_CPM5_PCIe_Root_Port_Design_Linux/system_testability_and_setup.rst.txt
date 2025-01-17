.. _ps_pcie_pl_pcie_driver_debug_checklist:

System Testability and Setup
============================

Loading Images onto SD
=======================

To boot via SD card, the correct files need to be loaded onto the SD card. First, the SD card should be connected to the system. The following three files from the ``<PETALINUX_PROJECT_FOLDER>/images/linux`` path must be copied to the SD card:

- ``boot.scr``
- ``BOOT.BIN``
- ``image.ub``

Once the files are finished copying to the SD card, the device should be safely ejected. The system is now ready to work directly with the VPK120.

Booting and Testing
===================

Setting up Components
----------------------

**Boot Mode Configuration**

On the VPK120, ensure that the Boot Mode is set to the SD configuration. To do this, configure switch SW1 such that it is set to ON-OFF-OFF-OFF (in order from positions 1 to 4). Refer to the VPK120 user guide for more information.

### Connecting the SD Card and PCIe Components

Insert the SD card into the SD card socket on the VPK120 board.

Connect the PCIe slot of the X-PCIE-04 card to the PCIe edge connector on the VPK120 board.

In this example design, the CPM5 preset is selected as "CPM5 PCIe Controller0." PCIe Controller0 drives Quad 102 and Quad 103 lanes, landing on Lane 8-Lane 15 of the VPK120 board. Therefore, an external lane shifter card (X-PCIE-04) is needed to shift the "Lane 8-Lane 15" signals to "Lane 0-Lane 7."

.. image:: System_Testability_and_Setup/img_36.jpg

If PCIe Controller1 is used, it drives Quad 104 and Quad 105 lanes, landing on Lane 0-Lane 7 of the VPK120 board. In this case, the additional X-PCIE-04 lane-shifter card is not needed.

**Connecting Other Components**

- Connect the other end of the X-PCIE-04 card to the PCIe slot on the solder side (back/under side) of the X-PCIE-03 card.
- Connect the M.2 PCIe carrier card assembly with SSD into the PCIe slot on the component side (top/front side) of the X-PCIE-03.
- Assuming use of a modular ATX power supply to provide PCIe AUXPWR to the X-PCIE-03, connect the main "24-pin ATX" cable assembly and one "6+2 PCIe" cable assembly to the PSU. Also, connect the jumper bridge to the free end of the "24-pin ATX" cable. The free end of the "6+2 PCIe" cable assembly provides PCIe AUXPWR.
- Connect the DC power brick supplied with the VPK120 to J16 on the VPK120 board.
- Connect the ATX power supply AUXPWR cable to the X-PCIE-03 at J5.
- Connect the USB cable provided with the VPK120 to J344, and then to an available USB port on the PC.
- Connect the DC power brick and the ATX power supply to AC power.

The following image shows the final test setup:

.. image:: System_Testability_and_Setup/img_37.jpg

Once all connections are made, open the Putty application on the connected machine. Select the connection type as "Serial" and choose the appropriate COM port number. For "Speed," select 115200. After clicking "Open," a terminal window will appear.

.. image:: System_Testability_and_Setup/img_38.jpg

### Powering On the VPK120

The system is now ready to be powered on. Ensure that the power cables are plugged in, and that the proper power supply units are turned on. To boot the system, flip the switch next to the 6-pin power connector on the VPK120.

Interacting with PetaLinux
===========================

### Boot Process

Once the system begins booting, the terminal will start showing information about the boot process of the VPK120. Eventually, the user will be automatically logged in as the root user in Linux.

### Driver Initialization

Below are print messages that show the "Driver Initialization" portion of the kernel console output.

### Verifying PCIe Devices

To verify if the Linux OS can detect the connected PCIe devices, the following command should be entered in the terminal:

.. code:: bash

    lspci

Sample output is as follows:

.. code:: bash

[    1.215511] NFS: Registering the id_resolver key type
[    1.220646] Key type id_resolver registered
[    1.224864] Key type id_legacy registered
[    1.228929] nfs4filelayout_init: NFSv4 File Layout Driver Registering...
[    1.235695] nfs4flexfilelayout_init: NFSv4 Flexfile Layout Driver Registering...
[    1.243345] jffs2: version 2.2. (NAND) (SUMMARY)  © 2001-2006 Red Hat, Inc.
[    1.270279] NET: Registered PF_ALG protocol family
[    1.275134] xor: measuring software checksum speed
[    1.281423]    8regs           :  6784 MB/sec
[    1.287156]    32regs          :  7344 MB/sec
[    1.293180]    arm64_neon      :  6046 MB/sec
[    1.297571] xor: using function: 32regs (7344 MB/sec)
[    1.302671] Key type asymmetric registered
[    1.306804] Asymmetric key parser 'x509' registered
[    1.311772] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 244)
[    1.319249] io scheduler mq-deadline registered
[    1.323819] io scheduler kyber registered
[    1.327892] io scheduler bfq registered
[    1.337388] xilinx-cpm-pcie fcdd0000.pcie: host bridge /axi/pcie@fcdd0000 ranges:
[    1.344993] xilinx-cpm-pcie fcdd0000.pcie:      MEM 0x00e0000000..0x00efffffff -> 0x00e0000000
[    1.353701] xilinx-cpm-pcie fcdd0000.pcie:      MEM 0x8000000000..0x807fffffff -> 0x8000000000
[    1.362447] xilinx-cpm-pcie fcdd0000.pcie: ECAM area [mem 0x600000000-0x600ffffff] can only accommodate [bus 00-0f] (reduced from [bus 00-ff] desired)
[    1.376077] xilinx-cpm-pcie fcdd0000.pcie: ECAM at [mem 0x600000000-0x600ffffff] for [bus 00-0f]
[    1.385239] xilinx-cpm-pcie fcdd0000.pcie: PCIe Link is UP
[    1.391441] xilinx-cpm-pcie fcdd0000.pcie: PCI host bridge to bus 0000:00
[    1.398303] pci_bus 0000:00: root bus resource [bus 00-ff]
[    1.403838] pci_bus 0000:00: root bus resource [mem 0xe0000000-0xefffffff]
[    1.410774] pci_bus 0000:00: root bus resource [mem 0x8000000000-0x807fffffff pref]
[    1.418522] pci 0000:00:00.0: [10ee:b348] type 01 class 0x060400
[    1.424598] pci 0000:00:00.0: reg 0x10: [mem 0x00000000-0xfffffffffff 64bit pref]
[    1.432196] pci 0000:00:00.0: supports D1
[    1.436253] pci 0000:00:00.0: PME# supported from D0 D1 D3hot D3cold
[    1.443233] pci 0000:00:00.0: bridge configuration invalid ([bus 00-00]), reconfiguring
[    1.451396] pci 0000:01:00.0: [144d:a808] type 00 class 0x010802
[    1.457482] pci 0000:01:00.0: reg 0x10: [mem 0xe0000000-0xe0003fff 64bit]
[    1.464591] pci_bus 0000:01: busn_res: [bus 01-ff] end is updated to 01
[    1.471282] pci 0000:00:00.0: BAR 0: no space for [mem size 0x100000000000 64bit pref]
[    1.479273] pci 0000:00:00.0: BAR 0: failed to assign [mem size 0x100000000000 64bit pref]
[    1.487613] pci 0000:00:00.0: BAR 8: assigned [mem 0xe0000000-0xe00fffff]
[    1.494474] pci 0000:01:00.0: BAR 0: assigned [mem 0xe0000000-0xe0003fff 64bit]
[    1.501859] pci 0000:00:00.0: PCI bridge to [bus 01]
[    1.506865] pci 0000:00:00.0:   bridge window [mem 0xe0000000-0xe00fffff]
[    1.536034] Serial: 8250/16550 driver, 4 ports, IRQ sharing disabled
[    1.543388] Serial: AMBA driver
[    1.550471] brd: module loaded
[    1.555744] loop: module loaded
[    1.561814] tun: Universal TUN/TAP device driver, 1.6
[    1.567112] CAN device driver interface
[    1.571306] usbcore: registered new interface driver asix
[    1.576786] usbcore: registered new interface driver ax88179_178a
[    1.582949] usbcore: registered new interface driver cdc_ether
[    1.588844] usbcore: registered new interface driver net1080
[    1.594561] usbcore: registered new interface driver cdc_subset
[    1.600548] usbcore: registered new interface driver zaurus
[    1.600804] nvme nvme0: pci function 0000:01:00.0
[    1.606186] usbcore: registered new interface driver cdc_ncm
[    1.610917] pci 0000:00:00.0: enabling device (0000 -> 0002)
[    1.616623] usbcore: registered new interface driver r8153_ecm
[    1.622316] nvme 0000:01:00.0: enabling device (0000 -> 0002)
[    1.634054] VFIO - User Level meta-driver version: 0.3
[    1.639598] usbcore: registered new interface driver uas
[    1.644995] usbcore: registered new interface driver usb-storage

Two lines of output should appear—one for the X-PCIe adapter and one for the NVMe drive connected.

.. image:: System_Testability_and_Setup/img_39.jpg

For more detailed information about each PCIe device, the following commands can be used:

.. code:: bash

    lspci -vvvs 00:00.0  # For the Xilinx device
    lspci -vvvs 01:00.0  # For the NVMe device

The system should have automatically mounted the drive. The result of the ``lsblk`` command is shown in the image below:

.. image:: <System_Testability_and_Setup/img_40.jpg>

### Performing I/O Tests

To measure the drive's performance, I/O tests can be performed.

#### Testing Write Speed

To test the drive's write speed, enter the following command into the terminal:

.. code:: bash

    sync; dd if=/dev/zero of=tempfile bs=1M count=1024; sync

This command copies 1GB of data and outputs the write speed of the drive.

#### Testing Read Speed

To test the read speed, clear the cache first and then run a similar command. Enter the following in this order:

.. code:: bash

    sudo /sbin/sysctl -w vm.drop_caches=3
    dd if=tempfile of=/dev/null bs=1M count=1024

This command outputs the read speed for a 1GB file copy and shows the speed of this operation. The commands create a 1GB file called ``tempfile`` to measure read and write speeds.
