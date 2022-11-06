.. _qdma_general_debug_checklist:

General Debug Checklist
=======================

* Review: https://support.xilinx.com/s/article/000033516?language=en_US
* Review: https://support.xilinx.com/s/article/000033539?language=en_US
* For " read file: Input/output error", check (Xilinx Answer 72813)
* If the simulation of the example design generated with "AXI Stream with Completion" is not working, make sure PF0 BAR2 is enabled. The example design implements registers which are accessed from the host through BAR2. 
* Check if the base address of all ring buffer (H2C, C2H and CMPT) are aligned to the 4K address. 
* Make sure the FMAP register is programmed. This is required to associate how many queues are allocated to each function. 
* Check if fetch crediting is enabled or not (See Credit Descriptor Context Structure); the user logic is required to provide a credit for each descriptor that should be fetched.
* Make sure the hardware and credit context are cleared before enabling the queue.
* Check PIDX and CIDX values. Make sure there are enough descriptors.
* In Simple Bypass Mode, if there is an issue where packets are getting dropped, make sure the correct value is provided to  c2h_byp_in_st_csh_pfch_tag[6:0] for all transfers. Check MDMA_C2H_PFCH_BYP_TAG (0x140C) in the register dump and confirm the value matches the value provided in c2h_byp_in_st_csh_pfch_tag[6:0]. For more information, see the below link:
    https://docs.xilinx.com/r/en-US/pg347-cpm-dma-bridge/C2H-Stream-Modes?tocId=k~agbMXDqpS_4dpKXB2MLg 

QDMA Performance Debug Questions
--------------------------------
    - Is the driver being used a Linux Kernel Driver or a DPDK Driver?
    - How many queues are required and enabled? 
    - What is the traffic pattern being applied?
    - Achieved performance for desired traffic pattern?
    - Required performance for desired traffic pattern?
    - What is the configuration of the design being tested? Is it Gen3x16? 
    - Are you using the Xilinx provided driver or a Custom driver? 
    - If you are using Xilinx provided driver, did you download the driver from an answer record or form the GitHub? 
    - What are MPS and MRRS values? Typically, MRRS is 512. If line rate is not observed for packet size 4K on a system, PCIe MRRS could be tuned to 2048 depending on the system used. 
    - Are you using BRAM or DDR? 
    - Have you checked by settings BARs as prefetchable? 
    - What is the Packet size? 

QDMA Performance Debug Checklist
--------------------------------

    - Review Chapter "Improving Performance Through the CPM and PL PCIe" in UG1388
        - https://docs.xilinx.com/r/2021.1-English/ug1388-acap-system-integration-validation-methodology/Improving-Performance-Through-the-CPM-and-PL-PCIe
    - Review " Performance and Utilization" in PG344 
        - https://docs.xilinx.com/r/en-US/pg344-pcie-dma-versal/Performance-and-Resource-Utilization?tocId=6QfBEhQFba9swgGuwrX89A
    - Make sure that “Relax ordering” is enabled and Host accept Relax ordering.
    - Make sure “Extended tag” is enabled.
    - Make sure all the Ring base address and data address are 4K aligned. (or to MPS boundary) 
    - Review the following note in: https://www.xilinx.com/support/answers/71453.html 
        - Typically, networking applications optimize for small packet performance and so can use more queues to saturate the Ethernet interface, while compute or storage applications might optimize for 4KB performance and saturate with fewer queues. As the report suggests, more queues help achieve small packet performance, but the max number of queues cannot exceed the number of threads available for the application. 
        - For the streaming mode this report suggests that 4 and more queues with prefetch enabled results in the high performance for different packet sizes. 
        - For the memory mapped mode, the QDMA IP easily achieves the line rate with the typical 4K workload even with a single queue when using BRAM. If DDR is desired, more queues might be needed to obtain the best performance. This depends on the memory configuration and the access pattern. For example, concurrent read and write to the same memory bank would greatly reduce the efficiency and should be avoided if possible. 
        - The bi-directional performance should be expected to be lower than uni-directional H2C and C2H, because the PCIe RQ interface is shared. 
        - In a multi-socket machine where NUMA is enabled, the latency for DMA reads can be prohibitively high, causing lower performance. Caution must be taken in the driver to avoid using the memory far away from the CPU core. 
        - Are you using a PCIe slot which hangs off directly from the CPU? 
        - Recommended PCIe setting:  Enable Extended Tag, Enable Relaxed Ordering 
        - Recommended QDMA setting: TX Queue depth = 2048, RQ Queue depth = 2048, Packet Buffer Size = 4096, Burst Size = 4
    - DPDK Driver Specific Settings: 
        - Boot Setting default_hugepagesz=1GB hugepagesz=1G hugepages=20 
        - If the system in use runs additional apps, it is always good practice to set aside CPU cores for dpdk workload. 
        - The additional kernel command-line parameters below can be added to GRUB boot settings: isolcpus=1-<n> nohz_full=1- rcu_nocbs=1-<n> 
        - isolcpus isolates cpus 1 to from kernel scheduling so that they are set aside for dedicated tasks like dpdk. 
        - nohz_full will put cpus 1 to in adaptive-ticks mode so as not to interrupt them with scheduling-clock interrupts. 
        - rcu_nocbs will fence off cpus 1 to from random interruptions of softirq RCU callbacks. 
        - Performance Setting: 
            - isolcpus=1-16  
            - Disable iptables/ip6tables service  
            - Disable irqbalance service  
            - Disable cpuspeed service Point scaling-governor to performance
    - Disable CPU power savings from the kernel command-line 
        - In some cases, disabling CPU power savings from the BIOS alone might not make the CPU(s) run on full horse-power. As an additional confirmation, disable power savings from the kernel command-line too: 
            - processor.max_cstate=0 intel_idle.max_cstate=0 intel_pstate=disable 
                - This will disable power savings on CPUs and disable intel_idle as well as the intel_pstate cpu frequency scaling driver. In addition, always double-confirm from 'cat /proc/cpuinfo | grep -i Mhz' output that all of the intended CPUs are operating at max speed. 
    - General Guidance
        - The internal QDMA descriptor fetch engine is limited in its operation and is optimized for smaller numbers of queues (16 or less). This is common for compute applications.
        - To achieve high QDMA performance in cases where large numbers of queues are used (32+), small packet sizes are used (512-bytes or less), and worst-case traffic patterns are used (round-robin one transfer per queue); external descriptor management or Simple Bypass mode should be used. This is common for networking applications.
            - You may still be able to achieve sufficient performance for your application without using simple bypass if you can avoid one of the conditions described above.
                - Decrease the number of Queues by coalescing data streams into fewer queues.
                    - 16 or fewer Queues
                - Increase packet size by combining transactions in the user-logic to create larger transfers.
                    - DPDK C2H: 1+ KBytes, DPDK H2C: 512+ Bytes
                    - Linux Kernel Driver C2H: 1+Kbytes, Linux Kernel Drive H2C: 2Kbyte
                - Modify the traffic pattern to avoid round-robin.
                    - Send more transactions (burst) to a fewer numbers of queues (16 or less) before cycling in/out traffic from new queues.
                        - Min burst size = (packet size limit from (2) / (actual packet size)
    - Make sure the driver is not reading the context when the queue is enabled; it can result in reduced performance.
    - Excessive writebacks events can severely reduce the descriptor enginer performance and consume bandwidth to the host. 
    - H2C stream interface is shared by all the queues.  Stream engine is designed to saturate PCIe for packets sizes as low as 128b. Recommendation: restrict the packet size to be host page size or maximum transfer unit as required by the user application. 
    - If there is both H2C and C2H traffic, use H2C_REQ_THROT, it will throttle H2C making way for more PCIe resource for C2H.
    - The QDMA Subsystem for PCIe has a shallow completion  input FIFO of depth 2. For bett erperformance, add FIFO for completi on  input as shown in the diagram below. Depth and width of the FIFO depends on the use case. Width is dependent on the largest CMPT size for the applicati on, and depth is dependent on performance needs. For best performance for 64 Byte CMPT, a depth of 512 is recommended.
    - If there is an issue with H2C performace, make sure h2c_byp_in_st_sdi port is asserted once in every 32 or 64 descriptors. If “h2c_byp_in_st_sdi” port is always high, for every packet transferred, there needs to be a status update going from QDMA to the Host. This will impact performance because the DMA will have to share the bus with “status update” for every single packet along with the other requests going to the Host. Also, it slows down the descriptor engine as it needs to switch context every time there is a status update. 
