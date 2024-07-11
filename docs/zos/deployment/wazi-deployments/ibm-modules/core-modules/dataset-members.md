# Working with Dataset Members

These modules could be used to work with dataset members on your MVS system. You can copy, archive, delete, restore dataset members.

## Available Modules

**[member_archive](https://www.ibm.com/docs/en/SSQ2R2_16.0.0/com.ibm.wazi.deploy.doc/wd_templ_member_archive.html)**

This module copies artifacts from a PDS to a backup PDS. It creates the backup PDS if it does not exist with the characteristics of the source PDS.

**[member_copy](https://www.ibm.com/docs/en/SSQ2R2_16.0.0/com.ibm.wazi.deploy.doc/wd_templ_member_copy.html)**

This module copies artifacts to a PDS. It creates the PDS if it does not exist.

**[member_delete](https://www.ibm.com/docs/en/SSQ2R2_16.0.0/com.ibm.wazi.deploy.doc/wd_templ_member_delete.html)**

This module deletes artifacts from a PDS.

**[member_restore](https://www.ibm.com/docs/en/SSQ2R2_16.0.0/com.ibm.wazi.deploy.doc/wd_templ_member_restore.html)**

This module copies artifacts from a backup PDS to a PDS.

## Configuration

In your `deploy.yml` create a new section for your PDS mapping under `spec/types`. Define your target and backup libraries, encoding and substitution settings.

### Define dataset parameters

``` yaml
  <dataset specification identifier>:
    type: <datset type, SEQ | PDS | PDSE | LIBRARY | BASIC | LARGE>
    primary_space: <value><CYL | TRK | KB | MB | GB>
    secondary_space: <value><CYL | TRK | KB | MB | GB >
    record_format: <record format, FB | FBA | FBS | U | VB | VBA | VBS>
    record_length: <value>
```

- **dataset specification identifier**: the name of your specification you will use in the target PDS section
    - **type**: type of dataset (also known as dsorg). Options: LIBRARY, PDS, PDSE, SEQ, KSDS, ESDS, RRDS, LDS, LARGE.
    - **primary_space**: space to allocate for the dataset. Defaults to 5M.
    - **secondary_space**: secondary (extent) space to allocate for the dataset. Defaults to 1/10 of primary space.
    - **record_format**: record format of dataset. Options: FB (default), F, FBA, FBS, U, VB, VBA, VBS.  Block size varies on record format: FBA=32718, FB=32720, VBA=32743, VB/U=32760.
    - **record_length**: logical record length, expressed in bytes. Defaults vary on format. F/FB/FBS=80, FBA=133, VB/VBA/VBS=137, U=0.
    - **block_size**:  block size of dataset. Default varies on record format: FBA=32718, FB=32720, VBA=32743, VB/U=32760.


### Define member operation settings

``` yaml
  - pattern: <selection pattern>
    spec: "{{ env.spec.<dataset specification identifier> }}"
    dataset: "{{ env.spec.app_hlq }}.<target library>"
    dataset_backup: "{{ env.spec.app_hlq }}.<backup name>.<target library>"
    # LOAD, NCAL, DBRM, etc
    is_load: <True | False>
    # or
    is_binary: <True | False>
    # for text files
    encoding:
      from: <code table>
      to: <code table>
    # text substitution
    text_subs:
      - pattern: <substring search expression>
        replace: <replacement string>

```

- **pattern**: regex to match your artifacts in your package. Make sure your regex gives the appropriate result and matches the source folder and the artifacts of your needs. Consider that for a single extension, eg.: .jcl or .sql, you could have multiple target datasets.

    You can use regex validator tools to test your expression.  One tool can be accessed at https://regex101.com/ .

    - **spec**: name of the dataset definiton
    - **dataset**: the target dataset
    - **dataset_backup**: the target dataset of your backup

- **Additional parameters:**
    - **is_load**: the members are LOAD modules. True or False
    - **is_binary**: the members are binary data, like DBRMs. True or False
    - **encoding**: if this key exist encoding operation will be performed using `iconv` command
        - **from**: from code table
        - **to**: to code table
    - **text_subs**: if this key exist test substitution operation will be performed. You can define a set of patern and replacement string pairs.
        - **pattern**: regex to define the search pattern to find strings to replace
        - **replace**: string to replace the text found by the regex pattern

## Samples

deploy.yml

``` yaml
common_pds_load_spec:
  # Library for LOAD modules
  # dataset type is LIBRARY
  type: LIBRARY
  # 10 cylinder primary space reservated
  primary_space: 10CYL
  # 20 cylinder secondary space reservated
  secondary_space: 20CYL
  # record format is U
  record_format: U
  # record length is 0 (not defined)
  record_length: 0

# Library for DBRM and binary modules
common_pds_binary_spec:
  type: LIBRARY
  primary_space: 10CYL
  secondary_space: 20CYL
  record_format: FB
  record_length: 80

# Library for text type members
common_pds_txt_spec:
  type: LIBRARY
  primary_space: 10CYL
  secondary_space: 20CYL
  record_format: FB
  record_length: 80

# Library for data
data_library_def:
  type: LIBRARY
  primary_space: 100CYL
  secondary_space: 200CYL
  record_format: FB
  record_length: 333


types:
  # select *.load files from the package's LOAD directory
  - pattern: .*.LOAD.*\.(LOAD)\/.*\.(LOAD)$
    # use the common_pds_load_spec dataset specification
    spec: "{{ env.spec.common_pds_load_spec }}"
    # define ACME.LOAD as target library for LOAD modules
    dataset: "{{ env.spec.app_hlq }}.LOAD"
    # define ACME.BK.LOAD as target backup library for LOAD modules
    dataset_backup: "{{ env.spec.app_hlq }}.BK.LOAD"
    # files are LOAD type
    is_load: True

  - pattern: .*\.(DBRM)\/.*\.(DBRM)$
    spec: "{{ env.spec.common_pds_binary_spec }}"
    dataset: "{{ env.spec.app_hlq }}.DBRM"
    dataset_backup: "{{ env.spec.app_hlq }}.BK.DBRM"
    is_binary: True

  - pattern: .*\.(JCL)\/.*\.(JCL)$
    dataset: "{{ env.spec.app_hlq }}.JCL"
    dataset_backup: "{{ env.spec.app_hlq }}.BK.JCL"
    spec: "{{ env.spec.common_pds_txt_spec }}"
    # file encoding
    # encode the source file from UTF-8 to IBM-1047 before copying to the target library
    encoding:
      from: UTF-8
      to: IBM-1047
    # text substitution
    # before copy, replace substrings defined by regexp patters with the given string
    text_subs:
      # regexp pattern which selects the word: .DEV.
      - pattern: (?<=\.)DEV(?=\.)
      # and replace it with: .TEST.
        replace: "TEST"
    rename_subs:
       # regexp pattern which selects the word: CDD
      - pattern: "CDD"
      # and replace it with: CDT
        replace: "CDT"
```

deployment plan

``` yaml
  - name: COPY/ARCHIVE/RESTORE/DELETE ARTIFACTS TO A PDS ACTIVITIES
    short_name: CRAD_PDS_MEMBERS
    description: |
      sample pds activities
    actions:
      - name: CRAD_PDS_MEMBERS_ACTIONS
        short_name: CRAD_PDS_MBR
        description: |
          sample pds actions
        states:
          - UNDEFINED
        steps:
          - name: TEST STEP FOR MEMBER_ARCHIVE
            short_name: TEST_ARCHIVE
            description: |
                sample member archive
            properties:
              - key: template
                value: member_archive
            tags:
              always
            plan_tags:
              always
          - name: TEST STEP FOR MEMBER_COPY
            short_name: TEST_COPY
            description: |
                sample member copy
            properties:
              - key: template
                value: member_copy
            tags:
              always
            plan_tags:
              always
          - name: TEST STEP FOR MEMBER_DELETE
            short_name: TEST_DELETE
            description: |
                sample member delete
            properties:
              - key: template
                value: member_delete
            tags:
              always
            plan_tags:
              always
          - name: TEST STEP FOR MEMBER_RESTORE
            short_name: TEST_RESTORE
            description: |
                sample member restore
            properties:
              - key: template
                value: member_restore
            tags:
              always
            plan_tags:
              always
        tags:
          always
        plan_tags:
              always
    types:
      - name: 'LOAD'
      - name: 'DBRM'
      - name: 'CICSLOAD'
      - name: 'JCL'
      - name: 'SQL'
    is_artifact: True
    tags:
      always
    plan_tags:
      always

```