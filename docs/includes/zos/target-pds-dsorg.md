    In your `deploy.yml`, add a section for each PDS type under the key: `spec`. If the target PDS doesn't exists, it will be created using these   parameters. Use the name in the `spec` key of the target definition.

    ``` yml
    <dataset_specification_name>:
      type: <library_type>
      space_primary: <primary_value>
      space_secondary: <secondary_value>
      record_length: <record_length>
      block_size: <block_size>
    ```

    - **specification_name**: the name of your specification you will us in the target PDS section

    - **library_type**: type of dataset (also known as dsorg). Options: LIBRARY, PDS, PDSE, SEQ, KSDS, ESDS, RRDS, LDS, LARGE.

    - **space_primary**: space to allocate for the dataset. Defaults to 5M.

    - **space_secondary**: secondary (extent) space to allocate for the dataset. Defaults to 1/10 of primary space.

    - **record_format**: record format of dataset. Options: FB (default), F, FBA, FBS, U, VB, VBA, VBS.  Block size varies on record format: FBA=32718, FB=32720, VBA=32743, VB/U=32760.

    - **record_length**: logical record length, expressed in bytes. Defaults vary on format. F/FB/FBS=80, FBA=133, VB/VBA/VBS=137, U=0.

    - **block_size**:  block size of dataset. Default varies on record format: FBA=32718, FB=32720, VBA=32743, VB/U=32760.

    **Examples**:

    ``` yml
    # for LOAD MODULES
    common_pds_load_spec:
      type: LIBRARY
      space_primary: 600
      space_secondary: 300
      record_length: 0
      block_size: 32760

    # for DBRM modules
    common_pds_binary_spec:
      type: LIBRARY
      space_primary: 600
      space_secondary: 300
      record_format: FB
      record_length: 80
      block_size: 0

    # for text type files
    common_pds_txt_spec:
      type: LIBRARY
      space_primary: 150
      space_secondary: 300
      record_format: FB
      record_length: 80
    ```