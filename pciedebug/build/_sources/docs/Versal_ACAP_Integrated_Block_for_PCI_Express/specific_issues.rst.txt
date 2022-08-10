.. _versal_integrated_block_specific_issues:

Specific Issues 
================


* How to enable PCIe-ID interface option in QDMA IP example design? 
    * The PCIe-IP interface can be enabled as described below:

1. Click '+' sign on the block ending with '_support'.   

.. image:: QDMA_PCIe_ID_Interface_images/1.png
    :align: center

2. Double click on the PCIe IP and enable PCIe id interface option in 'PF IDs' tab of the GUI. 

.. image:: QDMA_PCIe_ID_Interface_images/2.png
    :align: center

3. This will enable two new interfaces with the PCIe IP as shown below:

.. image:: QDMA_PCIe_ID_Interface_images/3.png
    :align: center

4. Select both ports and make them external ports. 

.. image:: QDMA_PCIe_ID_Interface_images/4.png
    :align: center 

5. The interface ports will be brought out in the top level design as shown in the snapshot below. 

.. image:: QDMA_PCIe_ID_Interface_images/5.png
    :align: center