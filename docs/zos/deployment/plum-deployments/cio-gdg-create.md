# Create GDGs

## Description
Using this module, you can create GDGs on your system. The module can create the GDG base and the MODEL dataset. You can set the parameters for each of your GDGs in a yaml file.

## Configuration

1. Define your GDG module parameters in your `deploy.yml` file.

    ``` yml
    gdg.jcltemplate: <path>/<template>
    gdg.base.steptemplate: <path>/<template>
    gdg.model.steptemplate: <path>/<template>
    gdg.sub: <yes|no>
    ```
    - **gdg.jcltemplate**: the path of the JCL template for the GDG JOB, defaults to: `deploy/<eny-type>/<env-name>/gdg/gdg_job.template`
    - **gdg.base.steptemplate**: the path of the JCL template for the GDG base step, defaults to: `deploy/<eny-type>/<env-name>/gdg/gdg_job_base.template`
    - **gdg.model.steptemplate**: the path of the JCL template for the GDG model step, defaults to: `deploy/<eny-type>/<env-name>/gdg/t_gdg_job_model.template`
    - **gdg.sub**: indicate if you require text substitution of the GDG definition file, `yes` or `no`

    **Example**:

    ``` yml
    gdg.jcltemplate: deploy/prod/acme-prod/gdg/gdg_job.template
    gdg.base.steptemplate: deploy/prod/acme-prod/gdg/gdg_job_base.template
    gdg.model.steptemplate: deploy/prod/acme-prod/gdg/<env>/gdg_job_model.template
    gdg.sub: yes
    ```

2. Create a GDG definition file

    In your repository, create a new forder for the GDG definition files under `src/main`, e.g., `gdg`.

    Then create a file for each of the GDGs you require for your applications, e.g., `acctdata.gdg`, this will create the `ACMED.DATA.ACCNTVAL`.

    **GDG base parameters**:

    ``` yml
    base:
        name: <hlq>.<library><gdgname>
        limit: <value>
        catalog: <calatog_name>
        owner: <owner_id>
        empty: <EMTPY|NOEMPTY>
        extended: <EXTENDED|NOEXTENDED>
        fifolifo: <LIFO|FIFO>
        purge: <PURGE|NOPURGE>
        scratch: <SCRATCH|NOSCRATCH>
    ```

    - **name**: specify the name of the GDG you want to create.
    - **limit**: define the maximum number of GDS generations
    - **catalog**: set the catalog in which your GDG is to be defined
    - **empty**: specify what action is to be taken when the number of generations is equal to the LIMIT value. `EMPTY` or `NOEMPTY`
    - **extend**: define whether the GDG is to be in extended format or not. `EXTENDED` or `NOEXTENDED`
    - **fifolifo**: defines the order in which the GDS list is returned for data set allocation. `FIFO` or `LIFO`
    - **owner**: identify the generation data set's owner.
    - **purge**: specify whether to override expiration dates when a generation data set (GDS) is rolled off and the SCRATCH parameter is set. `PURGE` or `NOPURGE`
    - **scratch**: set what action is to be taken for GDG when the data set is uncataloged from the GDG base as a result of EMPTY/NOEMPTY processing. `SCRATCH` or `NOSCRATCH`

    **Example**:

    ``` yml
    base:
      name: ACMED.DATA.ACCNTVAL
      limit: 15
      catalog: VCAT
      owner: ACME001
      empty: NOEMTPY
      extended: EXTENDED
      fifolifo: LIFO
      purge: NOPURGE
      scratch: NOSCRATCH
    ```

    **GDG model parameters**

    ``` yml
    model:
      dsn: <hlq>.<library><modelname>
      unit: <unit_name>
      space: <space_parameters>
      dcb:
        lrecl: <lrecl_value>
        recfm: <recfm>
        blksize: <blksize_value>
        dsorg: <dsorg>
    ```

    - **dsn**: define the DSN name for your model dataset
      - **unit**: SYSDA
      - **space**: space reservation parameters
      - **dcb**: specify the DCB parameter values here
        - **lrecl**: logical record length
        - **recfm**: blocking type, FB, VB, ...
        - **blksize**: block size
        - **dsorg**: dataset organization parameters, PS, PO, VS, ...

    **Example**:

    ``` yml
    model:
      dsn: ACMED.DATA.ACCNTVAL.MODEL
      unit: SYSDA
      space: TRK,5
      dcb:
        lrecl: 80
        recfm: FB
        blksize: 800
        dsorg: PS
    ```
3. Create build and packaging configuration for GDG artifacts

    In the `file.properties` define the parameters for GDG deployment type. Please check this chapter: [Deployment Types](../build/deployment-types.md)

    **Exapmle:**

    ***file.properties***

    ```
    dbb.scriptMapping = Transfer.groovy :: **/*.jcl,  **/*.gdg, ...
    ...
    transfer_gdgPDS=${hlq}.GDG
    ...
    transfer_datasetMapping = transfer_gdgPDS :: **/*.gdg
    ...
    transfer_deployType = GDG :: **/*.gdg
    ```

    ***transfer.properties***

    ```
    transfer_dsOptions=cyl space(1,1) lrecl(80) dsorg(PO) recfm(F,B) dsntype(library) :: transfer_jclPDS, transfer_gdgPDS, ...
    ```

3. Configure the target library for your GDG files

    To apply the changes in your GDG files, you don't have to copy the GDG files onto your mainframe, but the deployment module needs a PDS mapping sections in your `deploy.yml` to work properly.

    ``` yml
    - pattern: .*\.(GDG)\/.*\.(gdg)$
      dataset: ACME001.PPL.TEMP.GDG
      dataset_backup: ACME001.PPL.BKUP.GDG
      spec: common_pds_txt_spec

    ```

4. Add you GDG module to your deployment

    Edit the `dbb_static_package.properties` file and add the GDG CREATE module and set the deployment type to `GDG`, as you defined in your deployment types configuration.

    Please follow the instructions in [Deployment Flow](./deployment-flow.md)

    ``` yml
    - name: CREATE_GDG
      description: |
        This activity is dedicated to update DB2 objects using DDL files
      actions:
          - name: ADD
            description: |
              This action is applicable when we add new artifacts .
            states:
               - NEW
               - UNDEFINED
            steps:
            - name: CIO_GDG_CREATE
      types:
        - name: 'GDG'
      isArtifact: 'true'
    ```