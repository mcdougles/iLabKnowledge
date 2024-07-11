    In your `deploy.yml`, add a section for each source-target relation under the key: `spec`

    ``` yml
    - pattern: <regexp>
      dataset: <target_dataset>
      dataset_backup: <target_backup_dataset>
      spec: <dataset_spefification_name>
    ```
    [dataset_specification](target-pds-dsorg.md/)

    **Examples**:

    ``` yml
    - pattern: .*\.(LOAD)\/.*\.(load)$
      dataset: ACMED.LOAD
      dataset_backup: ACMED.BKUP.LOAD
      spec: common_pds_txt_spec
    - pattern: .*\.(DBRM)\/.*\.(dbrm)$
      dataset: ACMED.DBRM
      dataset_backup: ACMED.BKUP.DBRM
      spec: common_pds_txt_spec
    - pattern: .*\.(JCL)\/.*\.(jcl)$
      dataset: ACMED.JCL
      dataset_backup: ACMED.BKUP.JCL
      spec: common_pds_txt_spec
    ```

    - **pattern**: regex to match your artifacts in your package. Make sure your regex gives the appropriate result and matches the source folder and the        artifacts of your needs. Consider that for a single extension, eg.: .jcl or .sql, you could have multiple target datasets.<br>
    You can use regex validator tools to test your expression.  One tool can be accessed at https://regex101.com/ .

    - **dataset**: the target dataset
    - **dataset_backup**: the target dataset of your backup
    - **spec**: parameters of the target dataset, must match one of the names of PDS definitions

    Additional parameters:

    ``` yml
      rename_subs:
        - pattern: <regexp>
          replace: <string>
      actions:
        substitute: <yes|no>
        copytomvs: <yes|no>
    ```

    - **rename_subs**: renaming the member before copy.
        - **pattern**: regex to define the pattern to replace
        - **replace**: string to replace the pattern
    - **actions**:
        - **substitute**: run text substitution on the member context, `yes` or `no`
        - **copytomvs**: copy the member into the target library, `yes` or `no`

    **Examples**:

    ``` yml
    rename_subs:
      - pattern: "*ACMED*"
        replace: "ACMET"
    actions:
      substitute: yes
      copytomvs: no
    ```