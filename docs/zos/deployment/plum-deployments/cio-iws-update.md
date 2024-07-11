# IBM Workload Scheduler update

## Description

With this module you can use BATL (Batch Loader files) to create or update WSC applications (WSC, TWS, or OPC). The BATL files can be extracted from your WSC application into a BATL format file and loaded into your Github repository. Then, you can use your code editor to change the BATL file and commit it. Or, you can use your WSC and edit, then export again and overwrite in your repository.
For this module, you will find the general parameters in the `deploy.yml` file and there is a JCL template that the module will use to generate the update JOB.

## Configuration

!!! note
    Make sure, your pipline ID has the proper access!

1. Create build and packaging configuration for BATL artifacts

    In the `file.properties` define the parameters for BATL deployment type. Please check this chapter: [Deployment Types](../build/deployment-types.md)

    **Example:**

    ***file.properties***

    ```
    dbb.scriptMapping = Transfer.groovy :: **/*.jcl,  **/*.batl, ...
    ...
    transfer_batlPDS=${hlq}.BATL
    ...
    transfer_datasetMapping = transfer_batlPDS :: **/batl/*.batl
    ...
    transfer_deployType = BATL :: **/*.batl
    ```

    ***transfer.properties***

    ```
    transfer_dsOptions=cyl space(1,1) lrecl(80) dsorg(PO) recfm(F,B) dsntype(library) :: transfer_jclPDS, transfer_batlPDS, ...
    ```

2. Set the module parameters

    Edit the `deploy.yml` and find the section for WSC Update module. Set the allowed return code value, the JCL template location, the EQQMLIB library and if you need substitution or not.

    ```
    wsc.eqq.batl.jcltemplate: <path>/<template>
    wsc.eqq.batl.steptemplate : <path>/<template>
    wsc.eqq.batl.EQQMLIB: <hlq>.SEQQMSG0
    wsc.eqq.job.maxrc: <value>
    wsc.batl.sub: <yes|no>
    ```

    - **wsc.eqq.batl.jcltemplate**: the location of your WSC Update JOB template, default: `deploy/<eny-type>/<env-name>/tws/t_batl.template`
    - **wsc.eqq.batl.steptemplate** : the location of your WSC Update JOB step template, default: `deploy/<eny-type>/<env-name>/tws/t_batlstep.template`
    - **wsc.eqq.batl.EQQMLIB**: the EQQMLIB location, example `SYS1.TWSZOS.SEQQMSG0`
    - **wsc.eqq.job.maxrc**: the maximum allowed return code for the WSC Update JOB, usually: `4`
    - **wsc.batl.sub**: indicate if you want text substitution before update to apply for the target environment, `yes` or `no`

    **Examples**:

    ``` yml
    wsc.eqq.batl.jcltemplate: deploy/test/acme-test-qa/iws/batl_job.template
    wsc.eqq.batl.steptemplate : deploy/test/acme-test-qa/iws/batl_job_step.template
    wsc.eqq.batl.EQQMLIB: SIS1.TWS.SEQQMSG0
    wsc.eqq.job.maxrc: 4
    wsc.batl.sub: yes
    ```

3. Configure the target library for your BATL files

    To apply the changes in your BATL files, you don't have to copy the BATL files onto your mainframe, but the deployment module needs a PDS mapping sections in your `deploy.yml` to work properly.

    **Example**:
    ``` yml
    # BATL
    - pattern: .*\.(BATL)\/.*\.(batl)$
      dataset: ACME001.PPL.TEMP.BATL
      dataset_backup: ACME001.PPL.BKUP.BATL
      spec: common_pds_txt_spec
      actions:
          substitute: yes
          copytomvs: no
    ```

4. Create or update the WSC Update JCL templates

    You can find the default JCL templates in the `deploy/<eny-type>/<env-name>/iws/` folder. Please use these to create a JCL member on your system and try to run it. It might be that the JOB will not run at first try. Test and fix the issues. Then, if the JOB is running without issues, you can do the same changes in the template.

    !!! warning
        Don't change the tokens (`@@token@@`)!


5. Add your IWS Update module to your deployment

    Edit the `dbb_static_package.properties` file and add the CIO_IWS_UPDATE module and set the deployment type to `BATL`

    Please follow the instructions in [Deployment Flow](./deployment-flow.md)

    ``` yml
    - name: IWS_UPDATE
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
            - name: CIO_IWS_UPDATE
      types:
        - name: 'BATL'
      isArtifact: 'true'
    ```

5. Exporting your applications

    A new tool will be available soon!!!


