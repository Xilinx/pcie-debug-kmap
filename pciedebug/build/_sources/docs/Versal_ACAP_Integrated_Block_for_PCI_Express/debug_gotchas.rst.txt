.. _versal_pl_pcie_debug_gotchas:

Debug Gotchas
=============

* Check cfg_function_status signal. 
    * The output of this signal indicates the states of Command Register bits in the PCI Configuration Space of each function: I/O Space Enable, Memory Space Enable, Bus Master Enable and INTx Disable; for details, see: PG343.
* Use the following signals to trigger ILA when the width or speed change happen.
    * cfg_negotiated_width
    * cfg_current_speed
* Check cfg_local_error_out signal to see if there is any local errors e.g. Link Replay Timeout, Link Reply Rollover etc. 
    * For more details, see PG343.
* If you need to trigger ILA at certain ltssm state, use cfg_ltssm_state signal. 
    * For encoding value for each state, see PG343.




