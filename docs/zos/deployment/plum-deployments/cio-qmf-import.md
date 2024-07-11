# QMF Imports

## Description

You can export your QMF Forms, Queries and Procedures from QMF into files and put them in your Github repository as source files. The **QMF Import** module will then import them into QMF. You can edit your QMF files directly in your code editor or using QMF Workstation and then export the new version and replace in the repository.

## Configuration

!!! note
    Make sure, your pipline ID has the proper Db2 access!

To configure your QMF Import module you will have to add configuration parameters to your `deploy.yml`, create an instruction file, `qmfcmd.yml` (by default), and a JCL template for the QMF Import JOB. The module will create a JOB containing `QMF IMPORT` commands and submit it.
In the `deploy.yml`, you define the generic parameters for this module. In the instruction file, you will define the `QMF IMPORT` command parameters for each of your QMF objects.

1. Create build and packaging configuration for QMF artifacts

    In the `file.properties` define the parameters for QMF deployment types (SQL for queries and procedures and FRM for forms). Please check this chapter: [Deployment Types](../build/deployment-types.md)

    **Exapmle:**

    ***file.properties***

    ```
    dbb.scriptMapping = Transfer.groovy :: **/*.jcl,  **/*.frm, **/*.sql,...
    ...
    transfer_qmfformPDS=${hlq}.<QMFFORM>
    transfer_qmfprocPDS=${hlq}.<QMFPROC>
    transfer_qmfqueryPDS=${hlq}.<QMFQUERY>
    ...
    transfer_datasetMapping = transfer_qmfformPDS :: **/<qmfform>/*.frm
    transfer_datasetMapping = transfer_qmfprocPDS :: **/<qmfproc>/*.sql
    transfer_datasetMapping = transfer_qmfqueryPDS :: **/<qmfquery>/*.sql
    ...
    transfer_deployType = QMFFRM :: **/<qmfform>/*.frm
    transfer_deployType = QMFPRC :: **/<qmfproc>/*.sql
    transfer_deployType = QMFQRY :: **/<qmfquery>/*.sql
    ```

    ***Transfer.properties***
    ```
    transfer_dsOptions=cyl space(1,1) lrecl(80) dsorg(PO) recfm(F,B) dsntype(library) :: transfer_jclPDS, transfer_qmfformPDS, transfer_qmfprocPDS, transfer_qmfqueryPDS
    transfer_dsOptions=cyl space(1,1) lrecl(255) dsorg(PO) recfm(F,B) dsntype(library) :: transfer_qmfformPDS
    ...
    ```


1. Set your default QMF Import parameters

    In the `deploy.yml`, you will find a section for QMF Imports. Define the default parameters for your QMF Import module according to your environment (dev, test or prod).

    ``` yml
    # DB2 parameters for QMF imports
    db2.qmf.import.job.maxrc: <value>
    db2.qmf.import.schema: <schema_name>
    db2.qmf.import.jcltemplate: <path>/<template>
    db2.qmf.import.instructions: <path>/<template>
    db2.qmf.sub: <yes|no>
    db2.qmf.mappings:
    # QMFFORM
      - pattern: <regexp>
        dataset: <apphlq>.<library>.<qmf_form_library>
    # QMFPROC
      - pattern: <regexp>
        dataset: <apphlq>.<library>.<qmf_proc_library>
    # QMFQUERY
      - pattern: <regexp>
        dataset: <apphlq>.<library>.<qmf_query_library>
    ```

    - **db2.qmf.import.job.maxrc**: the maximum allowed return code for the QMF import JOB, usually: `4`
    - **db2.qmf.import.schema**: the target Db2 schema name where you want to import
    - **db2.qmf.import.jcltemplate**: the location of your QMF import JOB tempate, default: `deploy/<eny-type>/<env-name>/qmf/t_qmfimport.template`
    - **db2.qmf.import.instructions**: the location of the QMF import instruction file, default: `deploy/<eny-type>/<env-name>/qmf/qmfcmd.yml`
    - **db2.qmf.sub**: indicate if you want text substitution before import to apply for the target environment, `yes` or `no`
    - **db2.qmf.mappings**: the source-target mapping for your QMF artifacts, create a block for Forms, Queries and Procedures
      - **pattern**: regular expression to select QMF artifacts
      - **dataset**: target dataset name


    **Example**:
    ``` yml
    # DB2 parameters for QMF imports
      db2.qmf.import.job.maxrc: 4
      db2.qmf.import.schema: ACMEDBA
      db2.qmf.import.jcltemplate: deploy/test/acme-test-qa/qmf/qmfimport_job.template
      db2.qmf.import.instructions: deploy/test/acme-test-qa/qmf/qmf_instructions.yml
      db2.qmf.sub: yes
      db2.qmf.mappings:
      # QMFFORM
        - pattern: .*\.(QMFFORM)\/.*\.(frm)$
          dataset: ACME001.PPL.TEMP.QMFFORM
      # QMFPROC
        - pattern: .*\.(QMFPROC)\/.*\.(sql)$
          dataset: ACME001.PPL.TEMP.QMFPROC
      # QMFQUERY
        - pattern: .*\.(QMFQUERY)\/.*\.(sql)$
          dataset: ACME001.PPL.TEMP.QMFQUERY
    ```

2. Configure the target PDS for QMF members

    For the QMF IMPORT command to work, you will need to have your QMF artifacts as a PDS member on your system. Configure the [**Member Copy**](./cio-member-copy.md) or [**Text Substitution**](./cio-text-sub.md) modules to copy these artifacts into the `<apphlq>.QMFFORM`, `<apphlq>.QMFPROC`, `<apphlq>.QMFQUERY` libraries. You will not need those file for your application to run, you only need them on you MVS when the QMF IMPORT command is executed. You can copy them into a temporary library.

    **Example**:
    ``` yml
    # QMFFORM
      - pattern: .*\.(QMFFORM)\/.*\.(frm)$
        dataset: ACME001.PPL.TEMP.QMFFORM
        dataset_backup: ACME001.BKUP.QMFFORM
        spec: common_pds_frm_spec
    # QMFPROC
      - pattern: .*\.(QMFPROC)\/.*\.(sql)$
        dataset: ACME001.PPL.TEMP.QMFPROC
        dataset_backup: ACME001.BKUP.QMFPROC
        spec: common_pds_txt_spec
        actions:
          substitute: yes
          copytomvs: yes
    # QMFQUERY
      - pattern: .*\.(QMFQUERY)\/.*\.(sql)$
        dataset: ACME001.PPL.TEMP.QMFQUERY
        dataset_backup: ACME001.BKUP.QMFQUERY
        spec: common_pds_txt_spec
        actions:
          substitute: yes
          copytomvs: yes
    ```

3. Create your instruction file

    The next step is to create or update the QMF import instruction file (use default: `deploy/<environment>/qmf/qmfcmd.yml` or create a new YAML file in your `deploy/<environment>/qmf` directory, you can use any filename). Create a section for each of your QMF objects under the appropriate main category.
    Finally set the `db2.qmf.import.instructions` parameter in the `deploy.yml` to point to your instruction file,
    See the below example:

    ``` yml
    QMFPROC:
      ACMEPROC:
        type: PROC
        member: ACMEPROC
        name: ACME_PROC_ONINSERT
      ACMEPROC2:
        type: PROC
        member: ACMEPROC2
        name: ACME_PROC_ONINSERT2
     ...

    QMFQUERY:
      ACMEQRY:
        type: QUERY
        member: ACMEQRY
        name: ACME_QUERY_PAYXMENTS

    QMFFORM:
      ACMEFRM:
        type: FORM
        member: ACMEFRM
        name: ACME_FORM_PAYMET_REPORT

    ```

    - **type**: type of the QMF object, QUERY, PROC or FORM
    - **member**: the member name of the QMF object
    - **name**: the object in QMF, this name will appear in the QMF catalog

    This will generate the following QMF IMPORT command:

    `IMPORT QUERY ACME_QUERY_PAYXMENTS FROM 'ACME.QMFQUERY'(MEMBER=ACMEQRY,SHARE=YES`

4. Update the QMF import JOB template

    You can find the default QMF import JOB template in the `deploy/<environment>/qmf/` directory for each environment. Use this file to create a JCL member on your system and try to run it first. The template uses REXX code and ISPF functions which might not run on your system at first. Test and fix JCL errors and runtime errors. If the JOB runs successfully, you can do the same changes in the template also.
    Use the `db2.qmf.import.jcltemplate` parameter in the `deploy.yml` to set your template location.

    !!! warning
        Don't change the tokens (`@@token@@`)!

5. Add your QMF IMPORT module to your deployment

    Edit the `dbb_static_package.properties` file and add the QMF IMPORT module and set the deployment type to `SQL` and `FRM` as you defined in your deployment types configuration. Add the Text Substitution module if needed.

    Please follow the instructions in [**Deployment Flow**](./deployment-flow.md)

    **Example**:

    ``` yml
    - name: QMF_IMPORT
    description: |
      This activity is dedicated to update QMF objects using SQL and FRM files
    actions:
        - name: ADD
          description: |
            This action is applicable when we add new artifacts .
          states:
             - NEW
             - UNDEFINED
          steps:
          - name: CIO_TEXT_SUB
          - name: CIO_QMF_IMPORT
    types:
      - name: 'QMFQRY'
      - name: 'QMFPRC'
      - name: 'QMFFRM'
    isArtifact: 'true'
    ```


6. QMF Export

    Both QMF Workstation and QMF Workstation for Windows has export functionality. If you export on your mainframe system, QMF will create a PDS and members for each objects. Then you can import them into your repository using the same method described in the [Source Migration](../migration/source-migration.md) chapter under [**Migrating Your Source from PDS (Partitioned Data Set) to Github**](../migration/migrate-pds.md) section.
    If you use QMF on Windows or Mac you can directly export to your local repository folder.


