.. _qdma_debug_topics:

Packets being dropped in Simple Bypass Mode
===========================================

    - In Simple Bypass Mode, if there is an issue where packets are getting dropped, make sure the correct value is provided to c2h_byp_in_st_csh_pfch_tag[6:0] for all transfers. Check MDMA_C2H_PFCH_BYP_TAG (0x140C) in the register dump and confirm the value matches the value provided in c2h_byp_in_st_csh_pfch_tag[6:0]. For more information, see the below link.
	https://docs.xilinx.com/r/en-US/pg347-cpm-dma-bridge/C2H-Stream-Modes?tocId=k~agbMXDqpS_4dpKXB2MLg

