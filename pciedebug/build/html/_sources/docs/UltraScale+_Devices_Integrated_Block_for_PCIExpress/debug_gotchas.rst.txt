.. _ultrascale_plus_debug_gotchas:

Debug Gotchas
=============


* When the Requester Completion (RC) interface is configured for a width of 256 or 512 bits, depending on the type of TLP and Payload size, there can be significant interface utilization inefficiencies, if a maximum of 1 TLP for 256 bits or 2 TLPs for 512 bits is allowed to start or end per interface beat. This inefficient use of RC interface can lead to overflow of the completion FIFO when Infinite Receiver Credits are advertized. You must either:
    * Restrict the number of outstanding Non Posted requests, so as to keep the total number of completions received less than 64 and within the completion of the FIFO size selected, or 
    * Use the RC interface straddle option.  
* The user application must keep the s_axis_cc_tvalid signal asserted over the duration of the packet.
    * The integrated block treats the deassertion of s_axis_cc_tvalid during the packet transfer as an error, and nullifies the corresponding Completion TLP transmitted on the link to avoid data corruption. 
* The user application must also assert the s_axis_cc_tlast signal in the last beat of the packet. The integrated block can deassert s_axis_cc_tready in any cycle if it is not ready to accept data. 
    * The user application must not change the values on the CC interface during a clock cycle that the integrated block has deasserted s_axis_cc_tready.
* The discontinue signal can be asserted only when s_axis_cc_tvalid is active-High. 
    * The integrated block samples this signal when s_axis_cc_tvalid and s_axis_cc_tready are both asserted. Thus, after assertion, the discontinue signal should not be deasserted until s_axis_cc_tready is asserted.
* s_axis_cc_tvalid: The user application must assert this signal whenever it is driving valid data on the s_axis_cc_tdata bus. 
    * The user application must keep the valid signal asserted during the transfer of a packet. 
    * The core paces the data transfer using the s_axis_cc_tready signal.
  
.. note::
	The above debug gotchas are taken from QDMA Product Guide PG213.Please refer to the latest version of the document for more details and new updates. 
