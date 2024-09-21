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
* QDMA IP not detected in lspci
    * Check the following signals in ILA: 
        * user_link_up, axi_aresetn, soft_reset_n, phy_ready


    