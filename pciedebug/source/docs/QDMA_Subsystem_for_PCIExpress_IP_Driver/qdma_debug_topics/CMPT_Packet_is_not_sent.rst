.. _qdma_debug_topics:

CMPT Packet is not sent
=======================

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

