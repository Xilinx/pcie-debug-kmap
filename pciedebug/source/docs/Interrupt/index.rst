.. _interrupts:

===============
Interrupt Issue
===============

.. _interrupt_debug_checklist:

General Debug Checklist
-----------------------
- Check the Interrupt Enable bit in the PCIe Configuration Space. 
    - If you are using MSI, the MSI Control register has this Enable bit.
    - If you are using MSI-X, the MSI-X Control register has this Enable bit.
    - If you are using Legacy Interrupt, there is no enable, but check the Interrupt Disable bit in the Command Register within the PCI Configuration Space, and Interrupt Pin and the Interrupt Line register of the PCI Configuration Space. Ensure that cfg_interrupt_int is held until cfg_interrupt_done/fail is received.
        - Certain IP only need this signal asserted for a clock cycle, and some require it to be held steady until the IP responds with a done or fail indicator.
        - Some are also one hot encoded, so check the Product Guide for a given IP.

.. _interrupt_docs_debug_collaterals:

Documents and Debug Collaterals
-------------------------------

.. csv-table::
    :align: left
    :file: csv/documents_and_debug_collaterlas.csv
    :header-rows: 1

.. _interrupt_useful_links:

Useful Links
------------

.. csv-table::
    :align: left
    :file: csv/useful_links.csv
    :header-rows: 1

.. .. _interrupt_faqs:

.. General FAQs
------------

.. .. _interrupt_issues_answers:

.. Issues and Answers
.. ------------------

.. .. _interrupt_debug_tips_questions:

.. Issues and Debug Tips/Questions
.. -------------------------------

.. .. _interrupt_specific_issues:

.. Specific Issues 
.. ----------------

.. .. _interrupt_misc:

.. Miscellaneous	
.. --------------
