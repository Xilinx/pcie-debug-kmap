.. _qdma_faqs:

General FAQs
============

* There are multiple QDMA IP versions for e.g. CPM QDMA and PL PCIe QDMA in Versal devices, different QDMA versions such as 3.0 and 4.0 etc. Do these need seperate driver?
    * No, the driver provided in the link below should work for all QDMA versions.
    * https://github.com/Xilinx/dma_ip_drivers
* Does the QDMA driver support metadata field?
    * Metadata is intended to be used by the user logic; the drivers that we currently provide is for reference only. It doesn't provide an API to set the metadata. 
* Is there anything built in to the completion context or completion status that will let software know that the entry was for a marker request, and not an actual packet? 
    * Yes there is completion packet and status that is generated for marker request. User can send in a completion data that is recognized for Marker and can look for it.
* Who generates tlast?
    * PCIe block does not send rc_tlast; DMA block figures out tlast based on the number of bytes.
* In simple bypass mode, what is the correct way of driving credit input ports for prefetch mode?
    * When a user choose credit input mode (simple bypass mode) then there is no need to prefetch the credits. Because the user is giving the credits to fetch the descriptors so the DMA will go and fetch those credits and send it out. So in Simple bypass mode there is no prefetch mode
* When is s_axis_c2h_mty signal asserted?
    * It is asserted only when tlast is asserted. If there are any empty bytes then it needs to be passed when tlast is asserted. If mty is asserted when it is not the last packet, it is reported as an error in QDMA_C2H_ERR_STAT (0XAF0). If the erorr occurs, the log should show - "qdma_hw_error_handler(): Detected Fatal MTY mismatch error".
* When is this error reported - "qdma_hw_error_handler(): Detected Packet length mismatch error" ?
    * This error is reported when the total packet length do not match the signal from the s_axis_c2h_ctrl.len. bit-1 of QDMA_C2H_ERR_STAT (0XAF0) is set when this error occurs. 
* PG mentions h2c_byp_in_st_at port but it is not available in IP interface?
    * To use this port, enable_at_ports {true} needs to be set in vivado tcl console. This is de-featured as it hasn't been tested thoroughly. 
* In PG302, it says "The driver sets up the mapping of the C2H descriptor queue to the CMPT queue". While going through CMPT context and DPDK driver, we don't find how this is done.
    * The User should know which completion context (completion ring) is pointed to which QID. The user (software/driver) will program 0x844 register to program the completion context; at this time, user also needs to give QID which associates which completion points to which QID (and which C2H descriptor queue).
* Can multiple C2H descriptor queues mapped to the same CMPT queue?
    * No, this use case is not supported. 
* QDMA IP supports 4 PFs and 252 VFs, how are function numbers assigned? 
    * First for 4PFs, function number 0, 1, 2, and 3 will be programmed. Then all the VF’s belonging to PF0, then VF’s belonging to PF1.. etc.
* Is Jumbo packet supported in QDMA? I have a customer wanting to use packet size of 10k. 
    * Yes Jumbo packet upto the size of 64K is supported. The provided driver breaks it into chunks of 4K to cater the requirement to meet 4K boundary in C2H direction. Jumbo packet is supported in both Internal Mode and Bypass Mode.  
* Is it the hard requirement that user logic should monitor descriptor credits first and then send C2H data to QDMA IP? 
    * Yes, the user is required to check for the descriptor credits before sending the packet and this is a hard requirement. If not, the DMA will drop the packet. 
* When does tlast get asserted for AXI4-Stream Ports? 
    * For both s_axis_c2h_tlast and m_axis_h2c_tlast, tlast port indicates that this is the last cycle of the packet transfer.

