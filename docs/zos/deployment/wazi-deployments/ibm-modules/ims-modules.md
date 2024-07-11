# IMS Modules

Use this module to perform common deployment related task in your IMS system such as generating IMS Database Descriptors (DBD), Program Specification Blocks (PSB), Application Control Blocks (ACB), running IMS commands (type-1, type-2, DBRC), and interacting with the IMS Catalog.

## Available Modules

**[ims_acb_gen](https://www.ibm.com/docs/en/SSQ2R2_16.0.0/com.ibm.wazi.deploy.doc/wd_templ_shell_command.html)**

This building block generates an IMS application control block (ACB).

**[ims_catalog_populate](https://www.ibm.com/docs/en/SSQ2R2_16.0.0/com.ibm.wazi.deploy.doc/wd_templ_shell_command.html)**

This building block loads, inserts, or updates instances of IMS program specification blocks (PSB) or IMS database descriptors (DBD) into the database data sets of the IMS catalog from ACB library data sets. You can also read the IMS catalog.

**[ims_catalog_purge](https://www.ibm.com/docs/en/SSQ2R2_16.0.0/com.ibm.wazi.deploy.doc/wd_templ_shell_command.html)**

This building block removes the segments that represent an instance of IMS program specification blocks (PSB) or IMS database descriptors (DBD), all the instances of a DBD version, or an entire DBD or PSB record from the IMS catalog. It can also analyze the catalog and generate delete statements for the ACBs that are eligible for deletion, as well as update the ACB retention criteria.

**[ims_command](https://www.ibm.com/docs/en/SSQ2R2_16.0.0/com.ibm.wazi.deploy.doc/wd_templ_shell_command.html)**

This building block submits IMS commands. It is not related to artifacts.

**[ims_dbrc](https://www.ibm.com/docs/en/SSQ2R2_16.0.0/com.ibm.wazi.deploy.doc/wd_templ_shell_command.html)**

This building block submits IMS Database Recovery Control (DBRC) commands. It is not related to artifacts.

**[ims_mfsutl](https://www.ibm.com/docs/en/SSQ2R2_16.0.0/com.ibm.wazi.deploy.doc/wd_templ_shell_command.html)**

This building block creates and stores the Message Format Service (MFS) control blocks. It runs the MFSUTL procedure by default and hence updates the FORMAT library. It can also be used to run the MFSTEST procedure to update the TFORMAT library. In this case, you must modify the procname variable in the environment variables file.

**[ims_olcutl](https://www.ibm.com/docs/en/SSQ2R2_16.0.0/com.ibm.wazi.deploy.doc/wd_templ_shell_command.html)**

This building block copies a source library with your new definitions to a target library. Issuing the Online Change command sequence to prepare and commit an online change causes the inactive library to become the active library. The libraries concerned are ACBLIB, FORMAT, and MODBLKS.

## Configuration

Create a new variable under `spec` in your deployment configuration file for your IMS modules and define the parameters.

**IMS application control block**

Create a new dataset mapping for your IMS program specification block (PSB) and IMS database descriptor (DBD) and use `is_ims_psb` or `is_ims_dbd` variable to define the IMS type.

``` yaml
types:
  ...
  is_ims_psb: <True | False>
  is_ims_dbd: <True | False>
```

- **is_ims_psb**: set True is your artifact is IMS program specification block (PSB)
- **is_ims_dbd**: set True is your artifact is IMS database descriptor (DBD)

Create a group variable for your ACB Gen parameteres. Refer on this variable in your deployment method configuration, if not the default will be used.

``` yaml
<acbgen variable>:
  acb_lib: <hlq>.ACBLIB
  command_input: "BUILD"
  steplib :
    - <hlq>.SDFSRESL
  reslib:
    - <hlq>.SDFSRESL
  psb_lib:
    - <hlq>.PSB
  dbd_lib:
    - <hlq>.DBD
  build_psb: False
  compression: <PRECOMP|POSTCOMP>
  all_psb: <true|false>

ims_acb_gen_default:
  acb_lib: <hlq>.ACBLIB
  command_input: "BUILD"
  steplib :
    - <hlq>.SDFSRESL
  reslib:
    - <hlq>.SDFSRESL
  psb_lib:
    - <hlq>.PSB
  dbd_lib:
    - <hlq>.DBD
  build_psb: False
  compression: <PRECOMP|POSTCOMP>
  all_psb: <true|false>
```

**Populate IMS catalog**

Create a group variable for your IMS Catalog population parameteres. Refer on this variable in your deployment method configuration, if not the default will be used.

``` yaml
<ims catalog populate variable>:
   acb_lib:
     - <hlq>.ACBLIB
   mode: "READ"
   ims_id: "IMWZ"
   steplib :
     - <hlq>..SDFSRESL
   reslib:
     - <hlq>.SDFSRESL
   proclib:
     - <hlq>.PROCLIB
   psb_lib:
     - <hlq>.PSBLIB
     - <hlq>.PSBLOD
   dbd_lib:
     - <hlq>.DBDLIB
   online_batch: False
   buffer_pool_param_dataset: <hlq>.PROCLIB(DFSVSMDC)
   primary_log_dataset:
     dataset_name: <hlq>.IMSLOG
     disposition: "SHR"

ims_catalog_populate_default:
   acb_lib:
     - <hlq>.ACBLIB
   mode: "READ"
   ims_id: "IMWZ"
   steplib :
     - <hlq>..SDFSRESL
   reslib:
     - <hlq>.SDFSRESL
   proclib:
     - <hlq>.PROCLIB
   psb_lib:
     - <hlq>.PSBLIB
     - <hlq>.PSBLOD
   dbd_lib:
     - <hlq>.DBDLIB
   online_batch: False
   buffer_pool_param_dataset: <hlq>.PROCLIB(DFSVSMDC)
   primary_log_dataset:
     dataset_name: <hlq>.IMSLOG
     disposition: "SHR"
```

**Purge IMS catalog**

Create a group variable for your purge IMS Catalog parameteres. Refer on this variable in your deployment method configuration, if not the default will be used.

``` yaml
<ims catalog purge variable>:
  mode: "ANALYSIS"
  online_batch: False
  ims_id: "IMWZ"
  reslib:
    - <hlq>.SDFSRESL
  steplib:
    - <hlq>.SDFSRESL
  proclib:
    - <hlq>.PROCLIB
  psb_lib:
    - <hlq>.PSBLIB
  dbd_lib:
    - <hlq>.DBDLIB
  buffer_pool_param_dataset: <hlq>.PROCLIB(DFSVSMDC)
  primary_log_dataset:
    dataset_name: <hlq>.IMSLOG'
    disposition: "SHR"

ims_catalog_purge_default:
  mode: "ANALYSIS"
  online_batch: False
  ims_id: "IMWZ"
  dbrc: False
  irlm_id: "The IRLM ID if IRLM is enabled. Cannot be specified when online_batch is true."
  reslib:
    - '<hlq>.SDFSRESL'
  steplib:
    - '<hlq>.SDFSRESL'
  proclib:
    - '<hlq>.PROCLIB'
  psb_lib:
    - '<hlq>.PSBLIB'
    - '<hlq>.PSBLOADA'
  dbd_lib:
    - '<hlq>.DBDLIB'
  buffer_pool_param_dataset: '<hlq>.PROCLIB(DFSVSMDC)'
  primary_log_dataset:
    dataset_name: '<hlq>.IMSLOG'
    disposition: "SHR"
```

**Submit IMS commands**

Create a group variable for your IMS command parameteres. Refer on this variable in your deployment method configuration, if not the default will be used.

``` yaml
<ims command variable>:
  batch:
    - command: "QUERY PGM SHOW(ALL)"
      plex: <plex name>
    - command: "DISPLAY STATUS"
      plex: "PLXWZ"

ims_command_default:
  command: "QUERY PGM SHOW(ALL)"
  plex: <plex name>
  route:
    - "IMWZ"
```

**IMS Database Recovery Control commands**

Create a group variable for your IMS Database Recovery Control commands parameteres. Refer on this variable in your deployment method configuration, if not the default will be used.

``` yaml
<ims recovery variable>:
  command:
    - "LIST.RECON STATUS"
  steplib:
    - <hlq>.SDFSRESL
  recon1: <hlq>.RECON1
  recon2: <hlq>.RECON2
  recon3: <hlq>.RECON3

ims_dbrc_default:
  command:
    - "LIST.RECON STATUS
  steplib:
    - <hlq>.SDFSRESL
  recon1: <hlq>.RECON1
  recon2: <hlq>.RECON2
  recon3: <hlq>.RECON3
```

**Message Format Service utility**

Create a group variable for your Message Format Service utility parameteres. Refer on this variable in your deployment method configuration, if not the default will be used.

``` yaml
<ims mfsutil variable>:
   max_rc: <RC>
   jobcard: <mfsutil jo
   joblib: <hlq>.PROCLI
   procname: <proc name
   params:
    - "COMPR=COMPRESS"
    - "PXREF=XREF"

ims_mfsutl_default:
   max_rc: <RC>
   jobcard: <mfsutil jo
   joblib: <hlq>.PROCLI
   procname: <proc name
   params:
    - "COMPR=COMPRESS"
    - "PXREF=XREF"
```

**Issuing Online Change command sequence**

Create a group variable for your Message Format Service utility parameteres. Refer on this variable in your deployment method configuration, if not the default will be used.

``` yaml
<olcutil variable name>:
   procname: <olc proc name>
   lstype:
     - type: "ACBLIB"
       params:
         - "SOUT='*'"
         - "TYPE=ACB"
         - "IN=S"
         - "OUT=U"
     - type: "FORMAT"
       params:
         - "SOUT='*'"
         - "TYPE=FORMAT"
         - "IN=S"
         - "OUT=U"
     - type: "MODBLKS"
       params:
         - "SOUT='*'"
         - "TYPE=MODBLKS"
         - "IN=S"
         - "OUT=U"
   jobcard: <olcutil job card>
   joblib: <hlq>.PROCLIB
   max_rc : <rc>

ims_olcutl_default:
   procname: <olc proc name>
   lstype:
     - type: "ACBLIB"
       params:
         - "SOUT='*'"
         - "TYPE=ACB"
         - "IN=S"
         - "OUT=U"
     - type: "FORMAT"
       params:
         - "SOUT='*'"
         - "TYPE=FORMAT"
         - "IN=S"
         - "OUT=U"
     - type: "MODBLKS"
       params:
         - "SOUT='*'"
         - "TYPE=MODBLKS"
         - "IN=S"
         - "OUT=U"
   jobcard: <olcutil job card>
   joblib: <hlq>.PROCLIB
   max_rc : <rc>
```

## Samples

deploy.yml

``` yaml
  # ACB gen ********************************************************************
  my_ims_acb_gen:
     acb_lib: '{{ env.spec.app_hlq }}.MY.ACBLIB'
     command_input: "BUILD"
     steplib :
       - "DFS.V15R1M0.SDFSRESL"
     reslib:
       - "DFS.V15R1M0.SDFSRESL"
     psb_lib:
       - "{{ env.spec.app_hlq }}.MY.PSBA"
       - "{{ env.spec.app_hlq }}.MY.PSBB"
     dbd_lib:
       - "{{ env.spec.app_hlq }}.MY.DBDA"
     build_psb: False
     compression: PRECOMP,POSTCOMP
     all_psb: True

  ims_acb_gen_default:
     acb_lib: '{{ env.spec.app_hlq }}.ACBLIB'
     command_input: "BUILD"
     steplib :
       - "DFS.V15R1M0.SDFSRESL"
     reslib:
       - "DFS.V15R1M0.SDFSRESL"
     psb_lib:
       - "{{ env.spec.app_hlq }}.PSB2"
       - "{{ env.spec.app_hlq }}.PSB3"
     dbd_lib:
       - "{{ env.spec.app_hlq }}.DBD2"
     build_psb: False
     compression: PRECOMP,POSTCOMP
     all_psb: True

  # populate catalog ***********************************************************
  my_ims_catalog_populate:
     acb_lib:
       - '{{ env.spec.app_hlq }}.MY.ACBLIB'
     mode: "READ"
     ims_id: "IMWZ"
     steplib :
       - '{{ env.spec.app_hlq }}.MY.SDFSRESL'
     reslib:
       - '{{ env.spec.app_hlq }}.MY.SDFSRESL'
     proclib:
       - '{{ env.spec.app_hlq }}.MY.PROCLIB'
     psb_lib:
       - '{{ env.spec.app_hlq }}.MY.PSBLIB'
       - '{{ env.spec.app_hlq }}.MY.PSBLOD'
     dbd_lib:
       - '{{ env.spec.app_hlq }}.MY.DBDLIB'
     online_batch: False
     buffer_pool_param_dataset: '{{ env.spec.app_hlq }}.MY.PROCL(DFSVSMDC)'
     primary_log_dataset:
       dataset_name: '{{ env.spec.app_hlq }}.MY.IMSLOG'
       disposition: "SHR"

  ims_catalog_populate_default:
     acb_lib:
       - '{{ env.spec.app_hlq }}.ACBLIB'
     mode: "READ"
     ims_id: "IMWZ"
     steplib :
       - '{{ env.spec.app_hlq }}.SDFSRESL'
     reslib:
       - '{{ env.spec.app_hlq }}.SDFSRESL'
     proclib:
       - '{{ env.spec.app_hlq }}.PROCLIB'
     psb_lib:
       - '{{ env.spec.app_hlq }}.PSBLIB'
       - '{{ env.spec.app_hlq }}.PSBLOAD'
     dbd_lib:
       - '{{ env.spec.app_hlq }}.DBDLIB'
     online_batch: False
     buffer_pool_param_dataset: '{{ env.spec.app_hlq }}.PROCLIB(DFSVSMDC)'
     primary_log_dataset:
       dataset_name: '{{ env.spec.app_hlq }}.IMSLOG'
       disposition: "SHR"
       record_format: "VB"
       record_length: 6140
       block_size: 6144
       primary: 10
       primary_unit: "TRK"
       secondary: 10
       secondary_unit: "TRK"
       normal_disposition: "CATLG"
       abnomal_disposition: "DELETE"
       volumes:
         - VOL001
       type: "???"
       storage_class: ""
       management_class: ""
       data_class: ""
     bootstrap_dataset:
       dataset_name: '{{ env.spec.app_hlq }}.IMSBST'
       disposition: "NEW"
       primary: 10
       primary_unit: "TRK"
       secondary: 10
       secondary_unit: "TRK"
       normal_disposition: "CATLG"
       abnomal_disposition: "DELETE"
       volumes:
        - VOL001
       type: "SEQ"
       storage_class: ""
       management_class: ""
       data_class: ""
     check_timestamp: False
     control_statements: "UPDATE"
     dbrc: False
     directory_dataset:
       dataset_name: '{{ env.spec.app_hlq }}.IMSDIR'
       disposition: "NEW"
       primary: 10
       primary_unit: "TRK"
       secondary: 10
       secondary_unit: "TRK"
       normal_disposition: "CATLG"
       abnomal_disposition: "DELETE"
       volumes:
        - VOL001
       type: "SEQ"
       storage_class: ""
       management_class: ""
       data_class: ""
     directory_staging_dataset:
       dataset_name: '{{ env.spec.app_hlq }}.IMSSTG'
       disposition: "NEW"
       primary: 10
       primary_unit: "TRK"
       secondary: 10
       secondary_unit: "TRK"
       normal_disposition: "CATLG"
       abnomal_disposition: "DELETE"
       volumes:
        - VOL001
       type: "SEQ"
       storage_class: ""
       management_class: ""
       data_class: ""
     irlm_id: "The IRLM ID if IRLM is enabled. Cannot be specified when online_batch is true."
     modstat: '{{ env.spec.app_hlq }}.MODSTAT'
     secondary_log_dataset:
       dataset_name: '{{ env.spec.app_hlq }}.IMSLOG2'
       disposition: "SHR"
       record_format: "VB"
       record_length: 6140
       block_size: 6144
       primary: 10
       primary_unit: "TRK"
       secondary: 10
       secondary_unit: "TRK"
       normal_disposition: "CATLG"
       abnomal_disposition: "DELETE"
       volumes:
         - VOL001
       type: "???"
       storage_class: ""
       management_class: ""
       data_class: ""
     sysabend: "*"
     temp_acb_dataset:
       dataset_name: '{{ env.spec.app_hlq }}.TMPACB'
       disposition: "NEW"
       primary: 10
       primary_unit: "TRK"
       secondary: 10
       secondary_unit: "TRK"
       normal_disposition: "CATLG"
       abnomal_disposition: "DELETE"
       volumes:
        - VOL001
       type: "SEQ"
       storage_class: ""
       management_class: ""
       data_class: ""

  # purge catalog **************************************************************
  my_ims_catalog_purge:
     mode: "ANALYSIS"
     online_batch: False
     ims_id: "IMWZ"
     reslib:
       - '{{ env.spec.app_hlq }}.MY.SDFSRESL'
     steplib:
       - '{{ env.spec.app_hlq }}.MY.SDFSRESL'
     proclib:
       - '{{ env.spec.app_hlq }}.MY.PROCLIB'
     psb_lib:
       - '{{ env.spec.app_hlq }}.MY.PSBLIB'
     dbd_lib:
       - '{{ env.spec.app_hlq }}.MY.DBDLIB'
     buffer_pool_param_dataset: '{{ env.spec.app_hlq }}.MY.PROCL(DFSVSMDC)'
     primary_log_dataset:
       dataset_name: '{{ env.spec.app_hlq }}.MY.IMSLOG'
       disposition: "SHR"

  ims_catalog_purge_default:
     mode: "ANALYSIS"
     online_batch: False
     ims_id: "IMWZ"
     dbrc: False
     irlm_id: "The IRLM ID if IRLM is enabled. Cannot be specified when online_batch is true."
     reslib:
       - '{{ env.spec.app_hlq }}.SDFSRESL'
     steplib:
       - '{{ env.spec.app_hlq }}.SDFSRESL'
     proclib:
       - '{{ env.spec.app_hlq }}.PROCLIB'
     psb_lib:
       - '{{ env.spec.app_hlq }}.PSBLIB'
       - '{{ env.spec.app_hlq }}.PSBLOADA'
     dbd_lib:
       - '{{ env.spec.app_hlq }}.DBDLIB'
     buffer_pool_param_dataset: '{{ env.spec.app_hlq }}.PROCLIB(DFSVSMDC)'
     primary_log_dataset:
       dataset_name: '{{ env.spec.app_hlq }}.IMSLOG'
       disposition: "SHR"
       record_format: "VB"
       record_length: 6140
       block_size: 6144
       primary: 10
       primary_unit: "TRK"
       secondary: 10
       secondary_unit: "TRK"
       normal_disposition: "CATLG"
       abnomal_disposition: "DELETE"
       volumes:
         - VOL001
       type: "???"
       storage_class: ""
       management_class: ""
       data_class: ""
     delete_by_dbd_version:
     - member_name: DBDABCDE
       version_number: 02
     - member_name: xxx
       version_number: 03
     update_retention_criteria:
     - resource: DBD
       member_name: DBDNAMEA
       instances: 10
       days: 03
     - resource: PSB
       member_name: PSBNAMEA
       instances: 10
       days: 03
     delete:
     - resource: DBD
       member_name: DBDNAMEA
       time_stamp: 10
       days: 03
     managed_acbs: False
     resource_chkp_freq: 150
     sysut1:
       dataset_name: '{{ env.spec.app_hlq }}.SYSPUR'
       disposition: "NEW"
       block_size: 10000
       primary: 10
       primary_unit: "TRK"
       secondary: 10
       secondary_unit: "TRK"
       normal_disposition: "CATLG"
       abnomal_disposition: "DELETE"
       volumes:
         - VOL001
       type: "SEQ"
       storage_class: ""
       management_class: ""
       data_class: ""

  # command ********************************************************************
  my_ims_command:
   batch:
     - command: "QUERY PGM SHOW(ALL)"
       plex: "PLXWZ"
     - command: "DISPLAY STATUS"
       plex: "PLXWZ"

  ims_command_default:
   command: "QUERY PGM SHOW(ALL)"
   plex: "PLXWZ"
   route:
     - "IMWZ"

  # database recovery command **************************************************
  my_ims_dbrc:
     command:
       - "LIST.RECON STATUS"
     steplib:
       - "{{ env.spec.app_hlq }}.MY.SDFSRESL"
     recon1: "{{ env.spec.app_hlq }}.MY.RECON1"
     recon2: "{{ env.spec.app_hlq }}.MY.RECON2"
     recon3: "{{ env.spec.app_hlq }}.MY.RECON3"

  ims_dbrc_default:
     command:
       - "LIST.RECON STATUS"
     steplib:
       - "{{ env.spec.app_hlq }}.SDFSRESL"
     recon1: "{{ env.spec.app_hlq }}.RECON1"
     recon2: "{{ env.spec.app_hlq }}.RECON2"
     recon3: "{{ env.spec.app_hlq }}.RECON3"


  # mfs util *******************************************************************
  my_ims_mfsutl:
     max_rc: 4
     jobcard: "MYMFSUTL  JOB 'WD-MFSUTL',MSGLEVEL=(1,1),MSGCLASS=R,NOTIFY=&SYSUID"
     joblib: "{{ env.spec.app_hlq }}.MY.PROCLIB"
     procname: "MYMFSUTL"
     params:
      - "COMPR=COMPRESS"
      - "PXREF=XREF"

  ims_mfsutl_default:
     max_rc: 4
     jobcard: "MFSUTL  JOB 'WD-MFSUTL',MSGLEVEL=(1,1),MSGCLASS=R,NOTIFY=&SYSUID"
     joblib: "{{ env.spec.app_hlq }}.PROCLIB"
     procname: "MFSUTL"
     params:
      - "COMPR=COMPRESS"
      - "PXREF=XREF"


  # olc util *******************************************************************
  my_ims_olcutl:
     procname: "OLCUTL"
     lstype:
       - type: "ACBLIB"
         params:
           - "SOUT='*'"
           - "TYPE=ACB"
           - "IN=S"
           - "OUT=U"
       - type: "FORMAT"
         params:
           - "SOUT='*'"
           - "TYPE=FORMAT"
           - "IN=S"
           - "OUT=U"
       - type: "MODBLKS"
         params:
           - "SOUT='*'"
           - "TYPE=MODBLKS"
           - "IN=S"
           - "OUT=U"
     jobcard: "MYOLCUTL JOB 'WD-OLCUTL',MSGLEVEL=(1,1),MSGCLASS=R,NOTIFY=&SYSUID"
     joblib: "{{ env.spec.app_hlq }}.MY.PROCLIB"
     max_rc : 4


  ims_olcutl_default:
     procname: "OLCUTL"
     lstype:
       - type: "ACBLIB"
         params:
           - "SOUT='*'"
           - "TYPE=ACB"
           - "IN=S"
           - "OUT=U"
       - type: "FORMAT"
         params:
           - "SOUT='*'"
           - "TYPE=FORMAT"
           - "IN=S"
           - "OUT=U"
       - type: "MODBLKS"
         params:
           - "SOUT='*'"
           - "TYPE=MODBLKS"
           - "IN=S"
           - "OUT=U"
     jobcard: "OLCUTL JOB 'WD-OLCUTL',MSGLEVEL=(1,1),MSGCLASS=R,NOTIFY=&SYSUID"
     joblib: "{{ env.spec.app_hlq }}.PROCLIB}}"
     max_rc : 4
```

deployment plan

``` yaml

- name: IMS ACB/POPULATE/PURGE/COMMAND/DBRC/MFSUTL/OLCUTL ACTIVITIES
    short_name: IMS_OPERATIONS
    description: |
      sample ims acbgen, populate, purge, command, dbrc, mfs, olc activities
    actions:
      - name: GENERATE ACB/POPULATE/PURGE/COMMAND/DBRC/MFSUTL/OLCUTL actions
        short_name: IMS_ACBGEN
        description: |
          sample ims acbgen, populate, purge, command, dbrc, mfs, olc actions
        steps:
          - name: TEST STEP FOR IMS_ACB_GEN
            short_name: TEST_ACBGEN
            description: |
              sample acb generation
            properties:
            - key: template
              value: ims_acb_gen
            - key: var_ims_acb_gen
              value: my_ims_acb_gen
            tags:
              - always
            plan_tags:
              - always
          - name: TEST STEP FOR CATALOG_POPULATE
            short_name: TEST_CATALOG_POPULATE
            description: |
              sample ims catalog populate
            properties:
              - key: template
                value: ims_catalog_populate
              - key: var_ims_catalog_populate
                value: my_ims_catalog_populate
            tags:
              - always
            plan_tags:
              - always
          - name: TEST STEP FOR CATALOG_PURGE
            short_name: TEST_CATALOG_PURGE
            description: |
              sample ims catalog purge
            properties:
            - key: template
              value: ims_catalog_purge
            - key: var_ims_catalog_purge
              value: my_ims_catalog_purge
            tags:
              - always
            plan_tags:
              - always
          - name: TEST STEP FOR IMS_COMMAND
            short_name: TEST_IMS_COMMAND
            description: |
              sample ims command execution
            properties:
            - key: template
              value: ims_command
            - key: var_ims_command
              value: my_ims_command
            tags:
              - always
            plan_tags:
              - always
          - name: TEST STEP FOR IMS_DBRC
            short_name: TEST_IMS_DBRC
            description: |
              sample ims database recover control command
            properties:
            - key: template
              value: ims_dbrc
            - key: var_ims_dbrc
              value: my_ims_dbrc
            tags:
              - always
            plan_tags:
              - always
          - name: TEST STEP FOR IMS_MFUTL
            short_name: TEST_IMS_MFUTL
            description: |
              sample nessage format service control block creation
            properties:
            - key: template
              value: ims_mfsutl
            - key: var_ims_mfsutl
              value: my_ims_mfsutl
            tags:
              - always
            plan_tags:
              - always
          - name: TEST STEP FOR ONLINE_CHANGE
            short_name: TEST_ONLINE_CHANGE
            description: |
              sample online change command sequence
            properties:
            - key: template
              value: ims_olcutl
            - key: var_ims_olcutl
              value: my_ims_olcutl
            tags:
              - always
            plan_tags:
              - always
        tags:
          - always
        plan_tags:
          - always
    types:
      - name: 'PSB'
      - name: 'DBD'
    is_artifact: True
    tags:
      - always
    plan_tags:
      - always
```