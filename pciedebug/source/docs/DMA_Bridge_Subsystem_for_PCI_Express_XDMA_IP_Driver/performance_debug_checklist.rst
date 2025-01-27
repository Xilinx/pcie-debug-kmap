.. _dma_bridge_xdma_ip_performance_debug:

XDMA Performance Debug Checklist
================================

- **Link Status**: Check Link Status in lspci to ensure the link is operating at full speed and width.
- **Xilinx Video**: Check Xilinx Video - "Getting the Best Performance with Xilinx’s DMA for PCI Express."  

  .. raw:: html

     <div style="border: 1px solid #000; padding: 10px;">
       <a href="https://www.xilinx.com/video/technology/getting-the-best-performance-with-dma-for-pci-express.html">
         https://www.xilinx.com/video/technology/getting-the-best-performance-with-dma-for-pci-express.html
       </a>
     </div>

- **XDMA Debug Guide**: Check XDMA Debug Guide – AR71435.
- **XDMA Performance**: Check XDMA Performance Number answer record – AR68049.
- **Driver Type**: Check if you are using the Xilinx-provided driver or a custom driver.
- **Driver Source**: If using Xilinx driver, check if it's from an answer record or GitHub. For answer record 65444, try using the latest XDMA driver from GitHub:  

  .. raw:: html

     <div style="border: 1px solid #000; padding: 10px;">
       <a href="https://github.com/Xilinx/dma_ip_drivers/tree/master/XDMA/linux-kernel">
         https://github.com/Xilinx/dma_ip_drivers/tree/master/XDMA/linux-kernel
       </a>
     </div>

- **Vivado Version**: Check the Vivado version. If it's not the latest, try updating to the latest version.
- **Silicon Device**: Check which silicon device you are using (Production or ES).
- **Prefetchable BAR Memory**: Check if you are using prefetchable BAR memory to potentially improve performance.
- **Interrupt Processing**:
  - Check if MSI-X interrupt is being used instead of MSI or legacy interrupts for better data rates.
  - Check if Poll mode is being used for optimal data rate (See AR71435).
- **Descriptor Credit Transfer**: Check if you have tried Descriptor Credit-based transfer (supported only for C2H streaming in example design - See AR71435).
- **MPS/MRRS Values**: Check MPS and MRRS values. A typical system has 128Bytes MPS. Higher MPS leads to better performance (See WP350).
  - Example: x58 supports 256-byte MPS, x38 supports 128-byte MPS.
- **Link Stability**: Check if you have a stable link. Verify if LTSSM is recovering intermittently or continuously (See AR71355).
- **Link Analyzer**: Check if you have a link analyzer to identify NAKs. You can also use the PIPE interface with Gen3 descrambler for analysis.
- **Speed and Lane Configuration**: Check the speed and lane width configuration. Try using Gen3x8 if not already configured.
- **DMA Transfer Size**: Check DMA transfer size. Larger transfer sizes (within application limits) generally result in better performance.
- **XDMA Channels**: Check the number of channels used in XDMA. More channels can improve performance but consume more logic resources.
- **Credit Information**: Check if there is sufficient credit from the link partner for the XDMA IP to initiate data transfer.
- **Endpoint Memory**: Check if you are using BRAM or DDR for endpoint memory.
- **Gen3 vs Gen2**: If using Gen3, try configuring it for Gen2. Gen3 may be more error-prone if signal integrity is not robust.
- **AXI Data Width/Frequency**: Check the AXI side data width and frequency configuration. Try higher values if possible.
- **AXI Smart Connect**: If using AXI Smart Connect, try replacing it with AXI Interconnect IP.
- **AXI Interconnect**: If using AXI Interconnect, try configuring it in synchronous mode.
- **AXI System Data Width**: Check if your AXI system is on the same data width, which could improve performance if hardware limitations affect performance.
- **Narrow Burst**: Check if Narrow Burst is enabled. If so, try disabling it.
- **ISR Calls**: Check the XDMA log for repeated ISR calls.

Third-party references that may be helpful:
------------------------------------------------

.. raw:: html

   <div style="border: 1px solid #000; padding: 10px;">
     <a href="https://www.linkedin.com/pulse/xilinx-dma-pcie-tutorial-part-1-roy-messinger">
       Xilinx DMA PCIe Tutorial - Part 1
     </a>
   </div>

.. raw:: html

   <div style="border: 1px solid #000; padding: 10px;">
     <a href="https://www.linkedin.com/pulse/xilinx-dma-pcie-tutorial-part-2-roy-messinger/">
       Xilinx DMA PCIe Tutorial - Part 2
     </a>
   </div>

.. raw:: html

   <div style="border: 1px solid #000; padding: 10px;">
     <a href="https://www.linkedin.com/pulse/xilinx-dma-pcie-tutorial-part-3-roy-messinger">
       Xilinx DMA PCIe Tutorial - Part 3
     </a>
   </div>

.. raw:: html

   <div style="border: 1px solid #000; padding: 10px;">
     <a href="https://www.linkedin.com/pulse/deep-dive-xilinx-axi-bridge-pci-express-pg194-">
       Deep Dive into Xilinx AXI Bridge for PCI Express (PG194)
     </a>
   </div>

 
