# Working with Datasets (Libraries)

This Wazi-Deploy modules enabling you to create and delete dataset libraries. This module is not uses artifacts.

## Available Modules

**[ds_create](https://www.ibm.com/docs/en/SSQ2R2_16.0.0/com.ibm.wazi.deploy.doc/wd_templ_ds_create.html)**

This building block creates z/OS® data sets. It is not related to artifacts.

**[ds_delete](https://www.ibm.com/docs/en/SSQ2R2_16.0.0/com.ibm.wazi.deploy.doc/wd_templ_ds_delete.html)**

This building block deletes all the z/OS data set types (KSDS, ESDS, RRDS, LDS, SEQ, PDS, PDSE, LIBRARY, BASIC, LARGE, MEMBER, HFS, ZFS), except the GDG and ALIAS types. It is not related to artifacts.


## Configuration

In your `deploy.yml` create a new variable section for your datasets to create or delete under `spec`. This module doesn't uses artifacts, you should define each datasets you want to create or delete. After they are created or delete remove the definitions.

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
    - **record_format**: record format of dataset. Options: FB (default), F, FBA, FBS, U, VB, VBA, VBS.  Block size varies on record format: FBA=32718,     FB=32720, VBA=32743, VB/U=32760.
    - **record_length**: logical record length, expressed in bytes. Defaults vary on format. F/FB/FBS=80, FBA=133, VB/VBA/VBS=137, U=0.
    - **block_size**:  block size of dataset. Default varies on record format: FBA=32718, FB=32720, VBA=32743, VB/U=32760.


### Define dataset libraries

``` yaml
  <dataset identifier>: # use as reference in dbb_static_package
    - name: '{{ env.spec.app_hlq  }}.<dataset name>'
      spec: '{{ <env.spec.<dataset specification identifier>}}'

  ds_delete_lsds_default: # use this identifier as default
    - name: '{{ env.spec.app_hlq  }}.<dataset name>'
      spec: '{{env.spec.<dataset specification identifier>}}'
```

- **dataset identifier**: the name of your dataset definitions. This will be passed to the module in the deployment plan.
    - **name**: name of the dataset you want to create
    - **spec**: name of the dataset definiton

If you do not indicate the `<dataset identifier>` variable in the step properties of the deployment method, then the `ds_create` building block uses the default `ds_create_lsds_default` variable.

## Samples

deploy.yml

``` yaml

# Library for data
data_library_def:
  type: LIBRARY
  primary_space: 100CYL
  secondary_space: 200CYL
  record_format: FB
  record_length: 333


# create a new library named DATA based on the dataset definition: ds_create_data_library_def. see in dbb_static_package, properties/key/value for TEST_DS_CREATE
  ds_create_data_library:
    # name of the dataset to create
    - name: '{{ env.spec.app_hlq }}.DATA'
    # dataset dsorg specification name
      spec: '{{env.spec.data_library_def}}'

  # create a new sequential dataset named LIST based on the dataset definition: ds_create_data_list_def. see in dbb_static_package, properties/key/value for TEST_DS_CREATE
  ds_create_data_list:
      # name of the dataset to create
    - name: '{{ env.spec.app_hlq }}.LIST'
      # dataset dsorg specification name
      spec: '{{env.spec.data_list_def}}'

  # delete datasets LIB amd DATA. see in dbb_static_package
  ds_delete:
    - name: '{{ env.spec.app_hlq }}.DATA'
    - name: '{{ env.spec.app_hlq }}.LIST'
```

deployment plan

``` yaml
  - name: CREATE/DELETE ZOS DATASETS ACTIVITIES
    short_name: CRAD_DATASETS
    description: |
      sample dataset activities
    actions:
      - name: CREATE/DELETE ZOS DATASETS ACTIONS
        short_name: CRAD_DATASET
        description: |
          sample dataset actions
        states:
          - UNDEFINED
        steps:
        - name: TEST STEP FOR DS_CREATE_1
          short_name: TEST_DS_CREATE_1
          description: |
            sample dataset creation 1
          properties:
            - key: template
              value: ds_create
            - key: var_ds_create
              value: ds_create_data_library # from deploy_sample.yml
        - name: TEST STEP FOR DS_CREATE_2
          short_name: TEST_DS_CREATE_2
          description: |
            sample dataset creation 2
          properties:
            - key: template
              value: ds_create
            - key: var_ds_create
              value: ds_create_data_list # from deploy_sample.yml
          tags:
            - always
          plan_tags:
            - always
        - name: TEST STEP FOR DS_DELETE
          short_name: TEST_DS_DELETE
          description: |
            sample dataset deletion
          properties:
            - key: template
              value: ds_delete
            - key: var_ds_delete
              value: ds_delete # from deploy_sample.yml
          tags:
            - always
          plan_tags:
            - always
        is_artifact: False
        tags:
          - always
        plan_tags:
          - always

```