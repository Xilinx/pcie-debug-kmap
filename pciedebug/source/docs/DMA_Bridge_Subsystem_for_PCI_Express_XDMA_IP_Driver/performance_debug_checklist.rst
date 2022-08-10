.. _dma_bridge_xdma_ip_performance_debug:

XDMA Performance Debug
======================

- How are you measuring the performance? 
- Check the Link Status in lspci to ensure that your link is coming up to the full speed and width     
- Have you checked Xilinx Video - “Getting the Best Performance with Xilinx’s DMA for PCI Express” ? 
- Have you checked XDMA Debug Guide – AR71435? 
- Have you checked XDMA Performance Number answer record – AR68049? 
- Are you using the Xilinx provided driver or Custom driver? 
- If you are using Xilinx provided driver, did you download the driver from an answer record or from the GitHub? If you are using the driver from answer record 65444, can you try with the latest XDMA driver available in the GitHub: https://github.com/Xilinx/dma_ip_drivers/tree/master/XDMA/linux-kernel 
- Which version of Vivado are you using? If it is not the latest Vivado version, can you try with the latest version? 
- Which silicon device are you using? Is it production or ES? 
- Have you tried by using prefetchable BAR memory? -- possible to get performance improvement. 
- One of the main factors affecting data throughput is interrupt processing. Once data transfer is completed, the DMA sends an interrupt to the host and waits for ISR to process the status. However, this wait time is not predictable and so the overall total data transfer time is slow and unpredictable. There are a couple of options you can try to work around this. 
    - MSI-X interrupt: Users can try using MSI-X interrupt Instead of MSI or legacy interrupts. With MSI-X interrupt, the data rate is better than with an MSI or legacy interrupt-based design. -- How? Can we explain this with some lower level details? 
    - Poll mode (See AR:71435): Users can try using Poll mode which gives the best data rate. With Poll mode, there are no interrupts to process.  
- Have you tried Descriptor Credit based transfer? The example design supports this only for C2H streaming. (See: AR71435) -- How? Can we explain this with some lower level details? 
- Have you checked MPS, MRRS values? Systems with better MPS will give a better performance; a typical system would have 128Bytes MPS. (See WP350 for more details). For e.g. the x58 supports up to 256-byte maximum payload size (MPS), and the x38 supports up to 128-byte MPS. Overall, the x58 is a high-end, efficient machine and the x38 is a value-based machine. The performance of the x38 will be lower in comparison to the x58. 
- Have you checked if you have a stable link? Does LTSSM go to recovery intermittently or continuously? (See: AR71355) 
- Do you have a link analyzer to see if there are any NAKs being issued? You could also check this by looking at the PIPE interface using Gen3 descrambler module. See Xilinx Blog for more details.  
- What speed and lane width configuration are you using? Have you tried with Gen3x8? 
- What is the transfer size of the DMA? Larger the transfer size (within the limit of the application), better is the performance.  
- How many channels are you using in XDMA? Higher the number of channels, better the performance but the tradeoff is, it will consume more logic inside the device.  
- Have you checked credit information? Is there enough credit from the link partner for the XDMA IP to initiate data transfer? 
- Are you using BRAM or DDR for your endpoint memory? 
- If you have configured your design for Gen3, can you try by configuring it as Gen2? Gen3 could be more error-prone if the signal integrity on the board is not robust enough. Configuring the IP for a lower speed will reduce the possibility of signal integrity issue kicking in. If this happens, you should see an increase in performance.   
- What is the AXI side data width and frequency configured for? Have you tried higher values if the option is available?  
- Do you have AXI Smart Connect in your design? If so, can you try by replacing it with AXI Interconnect IP? 
- If you have AXI Interconnect, try by using it in synchronous mode.  
- See if your AXI system is on the same data width. This could provide some performance boost if the performance is affected due to hardware.  
- Try by disabling Narrow Burst, if it is enabled.  
- Check in the XDMA log if there is a call for repeated ISR.  
