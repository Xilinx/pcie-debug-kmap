.. _qdma_debug_topics:

axis_c2h_status_drop is Asserted
===========================================

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

