.. _qdma_debug_gotchas:

Debug Gotchas
=============


* If a queue is associated with interrupt aggregation, Xilinx recommends that the status descriptor be turned off, and instead the DMA status be received from the interrupt aggregation ring.
    * 
* To help a user-developed traffic manager prioritize the workload, the available descriptor to be fetched (incremental PIDX value) of the PIDX update is sent to the user logic on the interface. Using this interface it is possible to implement a design that can tm_dsc_sts prioritize and optimize the descriptor storage.
    * 
* Pre-fetch mode can be enabled on a per queue basis, and when enabled, causes the descriptors to be opportunistically prefetched so that descriptors are available before the packet data is available. The status can be found in prefetch context. This significantly reduces the latency by allowing packet data to be transferred to the PCIe integrated block almost immediately, instead of having to wait for the relevant descriptor to be fetched.
    * 
* Queue-based interrupts and user interrupts are allowed on PFs and VFs, but error interrupts are allowed only on PFs.
    * 
* The driver can be in polling or interrupt mode. Either way, the driver identifies the new CMPT entry either by matching the color bit or by comparing the PIDX value in the status descriptor against its current software CIDX value.
    * 
* Prior to enabling the queue, the hardware and credit context must first be cleared.
    * 
* The descriptor engine is informed of the availability of descriptors through an update to a queue’s descriptor PIDX. This portion of the context is direct mapped to the QDMA_DMAP_SEL_H2C_DSC_PIDX and QDMA_DMAP_SEL_C2H_DSC_PIDX address space.
    * 
* In normal operation, for an enabled queue, each time the irq_arm bit is asserted or PIDX of a queue is updated, the descriptor engine asserts tm_dsc_sts_valid.
    * 
* The memory mapped DMA engines (H2C and C2H) are enabled by setting the run bit in the Memory Mapped Engine Control Register. When the run bit is deasserted, descriptors can be dropped. Any descriptors that have already started the source buffer fetch will continue to be processed. Reassertion of the run bit will result in resetting internal engine state and should only be done when the engine is quiesced.
    * 
* There are two primary error categories for the DMA Memory Mapped Engine. The first is an error bit that is set with an incoming descriptor. In this case, the DMA operation of the descriptor is not processed but the descriptor will proceed through the engine to status update phase with an error indication. This should result in a writeback, interrupt, and/or marker response depending on context and configuration. It will also result in the queue being invalidated. The second category of errors for the DMA Memory Mapped Engine are errors encountered during the execution of the DMA itself. This can include PCIe read completions errors, and AXI bresp errors (H2C), or AXI errors and PCIe write errors due to bus master enable or functio bresp level reset (FLR), as well as RAM ECC errors. The first enabled error is logged in the DMA engine. Please refer to the Memory Mapped Engine error logs. If an error occurs on the read, the DMA write will be aborted if possible. If the error was detected when pulling write data from RAM, it is not possible to abort the request. Instead invalid data parity will be generated to ensure the destination is aware of the problem. After the descriptor which encountered the error has gone through the DMA engine, it will proceed to generate status updates with an error indication. As with descriptor errors, it will result in the queue being invalidated. See Descriptor Engine Errors.
    * 
* The H2C stream interface is shared by all the queues, and has the potential for a head of line blocking issue if the user logic does not reserve the space to sink the packet. Quality of service can be severely affected if the packet sizes are large. The Stream engine is designed to saturate PCIe for packet sizes as low as 128B, so Xilinx recommends that you restrict the packet size to be host page size or maximum transfer unit as required by the user application
    * 
* A performance control provided in the H2C Stream Engine is the ability to stall requests from being issued to the PCIe RQ/RC if a certain amount of data is outstanding on the PCIe side as seen by the H2C Stream Engine. To use this feature, the SW must program a threshold value in the H2C_REQ_THROT (0xE24) register. After the H2C Stream Engine has more data outstanding to be delivered to the user logic than this threshold, it stops sending further read requests to the PCIe RQ/RC. This feature is disabled by default and can be enabled with the H2C_REQ_THROT (0xE24) register. This feature helps improve the C2H Stream performance, because the H2C Stream Engine can make requests at a much faster rate than the C2H Stream Engine. This can potentially use up the PCIe side resources for H2C traffic which results in C2H traffic suffering The H2C_REQ_THROT (0xE24) register also allows the SW to separately enable and program the threshold of the maximum number of read requests that can be outstanding in the H2C Stream engine. Thus, this register can be used to individually enable and program the thresholds for the outstanding requests and data in the H2C Stream engine.
    * 
* The QDMA requires software to post full ring size so the C2H stream engine can fetch the needed number of descriptors for all received packets. If there are not enough descriptors in the descriptor ring, the QDMA will stall the packet transfer. For performance reasons, the software is required to post the PIDX as soon as possible to ensure there are always enough descriptors in the ring
    * 
* In cache bypass or internal mode prefetch mode can be turned on which will prefetch descriptor and that this will reduce transfer latency significantly. When prefetch mode is enabled, user can not send credits as input in "QDMA Descriptor Credit input ports". Credits for all queues will be maintained by prefetch engine. 
    * 
* When C2H Streaming Completion is enabled, after the packet is transferred, CMPT entry and CMPT status are written to C2H Completion ring. PIDX in the Completion status can be used to indicate the currently available completion to be processed 
    * 
* If the PCIe link goes down during DMA operations, transactions may be lost and the DMA may not be able to complete. In such cases, the AXI4 interfaces will continue to operate. Outstanding read requests on the C2H Bridge AXI4 MM interface receive correct completions or completion with a slave error response. The DMA will log a link down error in the status register. It is the responsibility of the driver to have a timeout and handle recovery of a link down situation.
    * 
  
.. note::
	The above debug gotchas are taken from QDMA Product Guide PG302.Please refer to the latest version of the document for new updates and more details. 
