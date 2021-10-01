.. _tandem_pcie:

===========
Tandem PCIe
===========

.. _tandem_pcie_faqs:

General FAQs
------------

- Tandem Restrictions
    - When first stage IO banks become active, all IO in those banks are alive
        - Second stage IO in those banks are active and outputs float until second stage completes
            - Users can insert OBUFT or mux to drive Z or constant until second stage is done
        - Second stage IO in unconfigured banks will pull high until second stage is done
            - Use PUDC_B to remove these pullups
    - All GTs in quad are consumed even when x2 or x1 selected in UltraScale+. Initialization granularity is per quad
    - For Tandem PROM, persist is required for all architectures. 
    - Dual-mode configuration pins cannot be used as user IO
    - ICAP cannot be used in Tandem PROM designs – mutually exclusive with persist.
    - Avoid Bank 65 for user application, especially MIG. Update Region is a Reconfigurable Partition, so it must remain logically and physically separate
    - If DMA subsystem is in stage 2, (Re)loading stage 2 bitstream will reset DMA logic. User can also generate standard partial bitstreams to reconfigure just the user application. User must manage logical decoupling for the Update Region Do NOT use these partial bitstreams as stage 2 bitstreams.


- Tandem Features
    - .. figure:: images/tandem_features.png

- PCIe Tandem Solution: Key Constraints
    - The design_switch output signifies to the user design that stage2 has been loaded
        - Can be used to multiplex Bank65 IO or manage logic connected to the PCIe core
    - IP level constraint to enable Tandem Stage1 and design cells
        - 7-Series: set_property HD.TANDEM 1 [get_cells <Stage1Cells>]
        - Ultrascale, Ultrascale+: set_property HD.TANDEM_IP_PBLOCK Stage1_Main [get_cells <Stage1Cells>]
            - Key Terms for this constraint: Stage1_Main, Stage1_Config_IO, Stage1_IO
    - IP level constraint to create physical partitioning Pblocks
        - create_pblock  <Stage1PblockName>]
        - resize_pblock [get_pblocks <Stage1PblockName>] -add {<Stage1SiteRanges>}
        - 7-series: set_property CONTAIN_ROUTING [get_pblocks <Stage1PblockName>]
        - 7-series: set_property EXCLUDE_PLACEMENT [get_pblocks <Stage1PblockName>] 
    - IP level constraint to identify pblock as stage1 and assign cells to it
        - 7-series: add_cells_to_pblock [get_pblocks <Stage1PblockName>] [get_cells <Stage1Cells]
        - Ultrascale, Ultrascale+: set_property HD.TANDEM_IP_PBLOCK Stage1_Main [get_cells <Stage1PblockName>]
    - Bitstream generation constraints
        - set_property BITSTREAM.GENERAL.COMPRESS TRUE [current_design]
        - set_property HD.OVERRIDE_PERSIST FALSE [current_design]
            - Tandem PCIe = FALSE; Tandem PROM = TRUE
        - set_property HD.TANDEM_BITSTREAMS Separate [current_design]
            - Tandem PCIe = Separate; Tandem PROM = Combined
    - User constraint to add cells to stage1: Specifically the PCIe clock and reset buffers
        - 7-series: set_property HD.Tandem 1 [get_cells <CellNames>]
        - 7-series: add_cells_to_pblock [get_pblocks <Stage1PblockName>] [get_cells <CellNames>]
        - Ultrascale, Ultrascale+: set_property HD.TANDEM_IP_PBLOCK Stage1_Main [get_cells refclk_ibuf]
        - Ultrascale, Ultrascale+: set_property HD.TANDEM_IP_PBLOCK Stage1_Config_IO [get_cells sys_reset_n_ibuf]
  
    These constraints should be used to add user-design stage1 primitives to the appropriate pblock physical partition

- PCIe Tandem Solution: Partitioning Special Handling
    - Dedicated primitives will require specific attention to maintain Tandem partitioning
        - Config Site Primitives: ICAP, STARTUP, BSCAN
        - Config Bank IO sites and PCIe reset pin bank IO (if not in the Config Bank)
    - Be aware of IPs that use dedicated primitives inside of them
        - The STARTUP primitive must be used to access the configuration flash after configuration
        - HWICAP IP uses ICAP
        - Microblaze Debug Module (MDM) uses BSCAN
        - Debug core insertion adds a BSCAN primitive during opt_design
        - MIG uses debug cores and thus BSCAN
        - MIG IO cannot straddle stage1 and stage2
    - Be aware of tool inserted primitives
        - IO Buffers: These should be manually instantiated in the desired design partition (stage1 vs stage2)
        - Debug clocking and BSCAN primitives
    - Example: I’m using a design with MIG and get an error message
        - Error: Non-Stage1 cell is placed within a stage1 site. Add the cell to stage1 or move it to a new location. Cells: <MIG IO Cells>
        - Error: Non-Stage1 cell is placed within a stage1 site. Add the cell to stage1 or move it to a new location. Cells: <BSCAN cell>
        - The error identifies that cells were inadvertently added to a stage1 partition. Customer should correct this by assigning the moving or assigning cells appropriately
    - MIG IO cells are embedded inside of MIG IP and cannot be partitioned across stage1/stage2
        - Customer must move the MIG location such that the IO pins do not conflict with a Tandem Stage1 IO Pblock
        - MIG should NOT be added to stage1 in its entirety 
        - This will make stage1 too large, increase programming time, and potentially fail to meet the 100ms boot requirement
    - MIG also uses an embedded debug core
        - This causes dbg_hub to get inserted during opt_deisgn
        - Customer must assign the appropriate primitives after opt_design using a opt_design_post.tcl script
        - set_property HD.TANDEM_IP_PBLOCK Stage1_Main [get_cells -quiet dbg_hub]
        - set_property HD.TANDEM_IP_PBLOCK Stage1_Config_IO [get_cells -quiet -hierarchical -filter { PRIMITIVE_TYPE =~ CLOCK.*.* && NAME =~  "dbg_hub/*" }]

.. _tandem_pcie_docs_debug_collaterals:

Documents and Debug Collaterals
-------------------------------

.. _tandem_pcie_useful_links:

Useful Links
------------

.. _tandem_pcie_debug_checklist:

General Debug Checklist
-----------------------

.. _tandem_pcie_issues_answers:

Issues and Answers
------------------

* The MCAP Windows driver provided in AR:64761 doesn't work for UltraScale+ Devices Integrated Block for PCI Express IP.
    * The MCAP capabilities start at offset 350h in UltraScale+ Devices Integrated Block for PCI Express IP (as opposed to 340h in UltraScale). Update the offset value and recompile the windows driver. It should work. 


.. _tandem_pcie_debug_tips_questions:

Issues and Debug Tips/Questions
-------------------------------

.. _tandem_pcie_specific_issues:

Specific Issues 
----------------

.. _tandem_pcie_misc:

Miscellaneous	
--------------
