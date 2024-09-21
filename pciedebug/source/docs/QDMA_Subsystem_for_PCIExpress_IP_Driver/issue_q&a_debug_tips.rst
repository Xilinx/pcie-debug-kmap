.. .. _qdma_issues_answers:

.. Issues and Answers
.. ==================

.. _qdma_issues_debug_tips_questions:

Issues/Debug Tips/Questions
===============================

* QDMA Driver failed to load (qdma:qdma_device_online: qdma_init failed -16.)
    * Make sure to connect st_rx_msg_rdy, tm_dsc_sts_rdy and soft_reset_n to 1'b1
* tm_dsc_sts_error reports error in h2c direction. The issue seems to occur after the first PIDX update write to QDMA. 
    * Make sure the Ring Size configuration is done correctly. 
* C2H Hang
    * Is the C2H transfer hitting 4K boundery in host memory when the hang occurs?
    * What is the status of s_axis_c2h_tready and s_axis_c2h_cmpt_tready signals?
    * Do you have console log before the hang occurs?
    * In the test, is it only C2H traffic or there is H2C traffic as well?
    * How many queues are active when the hang occurs?

* qdma_hw_error_handler( ): Detected Invalid PIDX update error
    * There could be two reasons for this issue:
        * PIDX update if bigger than the Ring size
        * There are no descriptors in the ring and DMA is trying to fetch more descriptors. If there are descriptors or not, look at the context dump for PIDX and CIDX. If they are at same level, it tells the ring is empty, the driver needs to post more descriptors.
    * Questions:
        * Do you see packet drops on AXI side? Check the register dump. Check AXI4-Stream Status Ports (axis_c2h_status_error, axis_c2h_status_drop)
        * Dump register from 0xA80 to 0xBF4.
        * Do you see if there are any error bit sets in completion entry?
            * “Detected Completion queue gets full error” if the completion queue is full then there should be any error bit set completion entry.
        * If the packets are being dropped on AXI side, it might be due to lack of descriptors. 
    
    * AXI_ST C2H block needs to be designed with ‘QDMA Traffic Manager Credit Output Ports (tm_dsc_sts_*). Confirm this has been done or not. (Note: tm_dsc_sts_* signals must be used in both internal mode or bypass mode in order not to drop any packets. )  These ports give how many credit are there for a given Q and so how many packets can the user send to QDMA. If a user sends more packets than the number of credits available then QDMA will drop those packets. 
    * Check 0XB10 (QDMA_C2H_STAT_DESC_RSP_DROP_ACCEPTED) register. It gives number of packets that are dropped.  
    * What is the Stream packet size (like 64 or 256 bytes, etc)?
    * For smaller packet sizes like 64bytes all these 4 registers should have same value for a good transfer (0xA88, 0xA8C, 0XA90 and 0xA94)
        * QDMA_C2H_STAT_S_AXIS_C2H_ACCEPTED (0XA88) - Number of C2H packet accepted from the userapplication.
        * QDMA_C2H_STAT_S_AXIS_CMPT_ACCEPTED (0xA8C) - Number of C2H completion packet accepted from the user application. 
        * QDMA_C2H_STAT_DESC_RSP_PKT_ACCEPTED (0xA90) - Number of descriptor response packets accepted. 
        * QDMA_C2H_STAT_AXIS_PKG_CMP (0xA94) - The number of C2H packets completed from the C2H DMA write engine.
    * Make sure the user logic is feeding the same QID to C2H and CMPT. 
* QDMA IP not detected in lspci
    * Check the following signals in ILA: 
        * user_link_up, axi_aresetn, soft_reset_n, phy_ready


    