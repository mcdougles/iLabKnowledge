# Sequential datasets

These modules could be used to copy, archive and delete sequential datasets.

## Available Modules

**[sequential_copy](https://www.ibm.com/docs/en/SSQ2R2_16.0.0/com.ibm.wazi.deploy.doc/wd_templ_seq_archive.html)**
This building block copies artifacts to a z/OS sequential data set. It creates the data set if it does not exist.

**[sequential_archive](https://www.ibm.com/docs/en/SSQ2R2_16.0.0/com.ibm.wazi.deploy.doc/wd_templ_seq_archive.html)**

This building block copies artifacts from a z/OS sequential data set to a sequential backup data set. It creates the sequential data set if it does not exist.

**[sequential_delete](https://www.ibm.com/docs/en/SSQ2R2_16.0.0/com.ibm.wazi.deploy.doc/wd_templ_seq_archive.html)**
This building block collects the artifacts and deletes the associated z/OS sequential data set.

## Configuration

In your `deploy.yml` create a new section for your sequental dataset mappings under `spec/types`. Define your target and backup datasets, encoding and substitution settings.

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
  # Sequential file for text data
  data_list_def:
    type: SEQ
    primary_space: 8TRK
    secondary_space: 4TRK
    record_format: FB
    record_length: 1024


types:
  # select *.SEQ files from the package's DATA directory. Define SEQ deployment type and configure it to be added to the package.
    - pattern: .*\.(DATA)\/.*\.(SEQ)$
      # use the seq_spec dataset specification
      spec: "{{env.spec.data_list_def}}"
      # define ACME.DATA. as target HLQ for the sequential dataset, the dataset name will be ACME.DATA.ACCDATA if the file name in github was accdata
      dataset_prefix: "{{ env.spec.app_hlq }}.DATA"
      # define ACME.DATA. as HLQ for the sequential backup dataset
      dataset_backup_prefix: "{{ env.spec.app_hlq }}.BACK.DATA"
      # encode the source file from UTF-8 to IBM-1047 before copying to the target library
      encoding:
        from: UTF-8
        to: IBM-1047
      # file is not binary data
      is_binary: False
      # the target dataset type is sequential
      is_sequential: True
```

deployment plan

``` yaml
  - name: COPY/ARCHIVE/DELETE ARTIFACTS TO A ZOS DATASET ACTIVITIES
    short_name: CRAD_SEQUENTIAL_DATA
    description: |
      sample sequential dataset activities
    actions:
      - name: COPY/ARCHIVE/DELETE ARTIFACTS TO A ZOS DATASET ACTIONS
        short_name: CRAD_SEQUENTIAL_DATA
        description: |
          sample sequential dataset actions
        states: UNDEFINED
        steps:
          - name: TEST STEP FOR SEQUENTIAL_COPY
            short_name: TEST_SEQ_COPY
            description: |
              sample dataset copy
            properties:
              - key: template
                value: sequential_copy
            tags:
              always
            plan_tags:
              always
          - name: TEST STEP FOR SEQUENTIAL_ARCHIVE
            short_name: TEST_SEQ_ARCHIVE
            description: |
              sample dataset archive
            properties:
              - key: template
                value: sequential_archive
            tags:
              always
            plan_tags:
              always
          - name: TEST STEP FOR SEQUENTIAL_DELETE
            short_name: TEST_SEQ_DELETE
            description: |
              sample dataset delete
            properties:
              - key: template
                value: sequential_delete
            tags:
              always
            plan_tags:
              always
        types:
          - name: 'seq'
        is_artifact: True
        tags:
          always
        plan_tags:
          always

```