.. .. _qdma_issues_answers:

.. Issues and Answers
.. ==================

.. _qdma_issues_debug_tips_questions:

Issues/Debug Tips/Questions
===============================

* Review the below links from QDMA Linux Driver Documentation; it might be helpful in debugging Linux driver issues:
    * QDMA Debug File System Support https://xilinx.github.io/dma_ip_drivers/master/QDMA/linux-kernel/html/debugfs.html
    * QDMA Linux Driver Use Cases https://xilinx.github.io/dma_ip_drivers/master/QDMA/linux-kernel/html/qdma_usecases.html
    * QDMA Linux Driver Design Flow https://xilinx.github.io/dma_ip_drivers/master/QDMA/linux-kernel/html/qdma_design.html
    * DMA Control Application (dma-ctl) https://xilinx.github.io/dma_ip_drivers/master/QDMA/linux-kernel/html/dmactl.html
    * Linux Distributions and Kernel Versions Support https://xilinx.github.io/dma_ip_drivers/master/QDMA/linux-kernel/html/system-requirements.html
* Review the below links from QDMA DPDK Driver Documentation; it might be helpful in debugging DPDK driver issues:
    * Linux Distributions and Kernel Versions Support https://xilinx.github.io/dma_ip_drivers/master/QDMA/DPDK/html/system-requirements.html
    * QDMA DPDK Driver UseCases https://xilinx.github.io/dma_ip_drivers/master/QDMA/DPDK/html/qdma_usecases.html
    * DPDK Driver debug https://xilinx.github.io/dma_ip_drivers/master/QDMA/DPDK/html/driver-design.html#dpdk-driver-debug 
* QDMA Driver failed to load (qdma:qdma_device_online: qdma_init failed -16.)
    * Make sure to connect st_rx_msg_rdy, tm_dsc_sts_rdy and soft_reset_n to 1'b1
* tm_dsc_sts_error reports error in h2c direction. The issue seems to occur after the first PIDX update write to QDMA. 
    * Make sure the Ring Size configuration is done correctly. 
* CMPT Packet is not sent
    * Review Software Context, Completion Context and Prefetch Context for each queue. 
    * Review Software Context, Completion Context and Prefetch Context for each queue. 
    * Which mode is used? Streaming mode or Memory Mapped mode?
    * How many queues are enabled?
    * How many packets are sent per queue?
    * What is the packet size?
    * When the issue occurs what happens when you do tear down? Does it timeout?
    * Is this custom driver or the driver downloaded from Xilinx github?
    * If it is Xilinx github, was there any modification done?
    * What commands were used to run the design? List all commands used. (This will help to reproduce the issue locally if feasible)
    * Is this Internal mode or Descriptor bypass mode?
    * Is C2H Prefetch enabled?
    * How many Queues is the QDMA configured for? Which queues are being used?
    * What are mask registers set to? Review all mask registers. (Note: '1' means the interrupt corresponding to that bit is enabled. Even though the mask bits are set to '0', the errors are still set in the corresponding registers. Only the interrupts are not propagated to the aggregator). 
    * Is the issue deterministic? Does it happen always after sending certain number of packets? 
    * what is the status of tm_dsc_sts_vld signal? [QDMA Traffic Manager Credit Output Ports Interface]
    * What is the Vivado version being used?
    * Create a counter outside of the IP to count the packets sent to QDMA IP and compare them with the value reported by the QDMA registers:
        * QDMA_C2H_STAT_S_AXIS_C2H_ACCEPTED (0xA88) (Number of C2H packet accepted from the user application)
        * QDMA_C2H_STAT_S_AXIS_CMPT_ACCEPTED (0xA8C) (Number of C2H completion packet accepted from the user application)
        * QDMA_C2H_STAT_DESC_RSP_PKT_ACCEPTED (0xA90) (Number of descriptor response packets accepted)
    * Check if s_axis_c2h_ctrl_has_cmpt and s_axis_c2h_cmpt_ctrl_cmpt_type are same or not

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
* axis_c2h_status_drop is asserted  
    * Check if desc_rsp_err is set or not in QDMa_C2H_ERR_STAT
    * Check the following registers:
        * QDMA_C2H_DROP_LEN_MISMATCH (0xBB4)
        * QDMA_C2H_DROP_DESC_RSP_LEN (0xBB8)
        * QDMA_C2H_DROP_QID_FIFO_LEN (0xBBC)
        * QDMA_C2H_DROP_PAYLOAD_CNT (0xBB0)
        * Interrupt Decode 2 Register (0xE38)
        * C2H_PACKETS_DROP (0x088)
    * Make sure the descriptor is still available; check tm_dsc_sts_error/tm_dsc_sts_avl
    * Make sure PIDX/CIDX is updated correctly
    * Check s_axis_c2h_tready


    