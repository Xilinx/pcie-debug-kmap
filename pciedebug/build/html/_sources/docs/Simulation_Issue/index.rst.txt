.. _simulation_issue:

================
Simulation Issue
================

.. _simulation_debug_checklist:

General Debug Checklist
-----------------------
* Check by bypassing EQ Phase 2 and 3 using PL_EQ_BYPASS_PHASE23 if there is an issue in simulation with Gen3 design
* For PCIe DMA simulation issues, always ensure that the IP is generated in Vivado with target language set to Verilog. A Timeout error might be seen if the target language is set to VHDL
* When using third party simulators, always ensure that the corresponding supported version of the simulator is used for a given Vivado version
* Check all reset and clock signals. Are these working as expected? Are the frequencies and polarities correct?
* Check the top level connections. Are TX and RX connected as expected?
* Are the Unisim_ver models being called first? If not, put them first in the library call list of the simulation launch.
* Is the script calling any FAST libraries?
* Are both sides expecting PIPE or serial (i.e. is one sending via txp/txn, and the other pipe_txdata)?
* Are the transactions on the user interface synchronous to the user clocks?
* Are all top level inputs driven?

.. _versal_acap_cpm5_simulation_example_design:

Versal ACAP CPM5 Simulation Example Design
------------------------------------------
* Versal ACAP CPM5 QDMA Simulation Example Design
    - https://github.com/Xilinx/XilinxCEDStore/tree/2022.2/ced/Xilinx/IPI/Versal_CPM5_QDMA_Simulation_Design
    - Read me: https://github.com/Xilinx/XilinxCEDStore/blob/2022.2/ced/Xilinx/IPI/Versal_CPM5_QDMA_Simulation_Design/readme.txt
* Veral ACAP CPM5 BMD Simulation Example Design
    - https://github.com/Xilinx/XilinxCEDStore/tree/2022.2/ced/Xilinx/IPI/Versal_CPM5_PCIe_BMD_Simulation_Design
    - Read me: https://github.com/Xilinx/XilinxCEDStore/blob/2022.2/ced/Xilinx/IPI/Versal_CPM5_PCIe_BMD_Simulation_Design/readme.txt

.. _simulation_docs_debug_collaterals:

Documents and Debug Collaterals
-------------------------------

.. csv-table::
    :align: left
    :file: csv/documents_and_debug_collaterlas.csv
    :header-rows: 1

.. _simulation_useful_links:

Useful Links
------------

.. csv-table::
    :align: left
    :file: csv/useful_links.csv
    :header-rows: 1

.. .. _simulation_faqs:

.. General FAQs
.. ------------

.. .. _simulation_issues_answers:

.. Issues and Answers
.. ------------------

.. .. _simulation_debug_tips_questions:

.. Issues and Debug Tips/Questions
.. -------------------------------

.. .. _simulation_specific_issues:

.. Specific Issues 
.. ----------------

.. .. _simulation_misc:

.. Miscellaneous	
.. --------------
