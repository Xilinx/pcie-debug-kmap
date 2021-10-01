.. _versal_cpm_issues_answers:

.. Issues and Answers
.. ==================

.. _versal_cpm_debug_tips_questions:

Issues and Debug Tips/Questions
===============================

    * The host is not able to detect VMK180/VCK190 boards with ES1 device
        * Make sure the SSC is turned off. This can be done in the BIOS
    * PDI programming fails in VMK180/VCK190 boards with ES1 device
        * In PS-PMC IO Configuration, make sure the following properties are set for PMC_MIO_37
            * External Usage: GPIO 
            * Output Data: high
            * Drive Strength: 8mA 
            * Slew: slow 
            * Pull: pullup 
            * Direction: out

