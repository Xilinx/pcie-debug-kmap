.. _qdma_debug_topics:

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


