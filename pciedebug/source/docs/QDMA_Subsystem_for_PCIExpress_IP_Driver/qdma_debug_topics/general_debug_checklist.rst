.. _qdma_general_debug_checklist:

General Debug Checklist
=======================

    - Review: https://support.xilinx.com/s/article/000033516?language=en_US
    - Review: https://support.xilinx.com/s/article/000033539?language=en_US
    - For ” read file: Input/output error”, check (Xilinx Answer 72813)
    - If the simulation of the example design generated with “AXI Stream with Completion” is not working, make sure PF0 BAR2 is enabled. The example design implements registers which are accessed from the host through BAR2.
    - Check if the base address of all ring buffer (H2C, C2H and CMPT) are aligned to the 4K address.
    - Make sure the FMAP register is programmed. This is required to associate how many queues are allocated to each function.
    - Check if fetch crediting is enabled or not (See Credit Descriptor Context Structure); the user logic is required to provide a credit for each descriptor that should be fetched.
    - Make sure the hardware and credit context are cleared before enabling the queue.
    - Check PIDX and CIDX values. Make sure there are enough descriptors.
    - QDMA Driver failed to load (qdma:qdma_device_online: qdma_init failed -16.)
    	- Make sure to connect st_rx_msg_rdy, tm_dsc_sts_rdy and soft_reset_n to 1'b1
    - tm_dsc_sts_error reports error in h2c direction. The issue seems to occur after the first PIDX update write to QDMA. 
	- Make sure the Ring Size configuration is done correctly.
    - C2H Hang
	- Confirm C2H transfer is hitting 4K boundery in host memory when the hang occurs.
	- Make sure s_axis_c2h_tready and s_axis_c2h_cmpt_tready signal are toggling correctly.
	- Check if it is dependent on number of queues active when the hang occurs.
    - QDMA IP not detected in lspci
	- Check the following signals in ILA: user_link_up, axi_aresetn, soft_reset_n, phy_ready


