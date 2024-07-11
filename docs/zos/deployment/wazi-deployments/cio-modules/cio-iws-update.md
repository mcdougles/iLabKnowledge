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

    ``` yaml
    wsc.eqq.batl.template_folder: <path><tempates>
    wsc.eqq.batl.job.template: <job template>.j2
    wsc.eqq.batl.step.template: <step template>.j2
    wsc.eqq.job.maxrc: <rc value>
    wsc.eqq.batl.jobcard:
      name: <jobname>
      job: <accounting>
      title: <job title>
      class: <class>
      msgclass: <message class>
      eqqmlib: <eqqm library>
    ```

    - **wsc.eqq.batl.template_folder**: the path of the folder where IWS JOB templates are stored
    - **wsc.eqq.batl.job.template**: the name of your WSC Update JOB template
    - **wsc.eqq.batl.step.template** : the name of your WSC Update JOB step template
    - **wsc.eqq.job.maxrc**: the maximum allowed return code for the WSC Update JOB, usually: `4`
    - **wsc.eqq.batl.jobcard**: JOB CARD parameter values corresponding tto your `wsc.eqq.batl.job.template`. You can use `{{jobcard.<parameter name>}}` format to access them.


    **Examples**:

    ``` yaml
    wsc.eqq.batl.template_folder: deploy/dev/acme-dev/cio_iws/
    wsc.eqq.batl.job.template: iwsjcl.j2
    wsc.eqq.batl.step.template: iwscmd.j2
    wsc.eqq.job.maxrc: 4
    wsc.eqq.batl.jobcard:
      name: ACMIWUPD
      job: PPL,ACME
      title: "IzWS Update for ACME"
      class: A
      msgclass: H
      eqqmlib: SYS1.TWSZOS.SEQQMSG0
    ```

3. Configure the target library for your BATL files

    To apply the changes in your BATL files, you don't have to copy the BATL files onto your mainframe, but the deployment module needs a PDS mapping sections in your `deploy.yml` to work properly.

    **Example**:

    ``` yaml
    # BATL
    - pattern: .*\/(BATL)\/.*\.(batl)$
      dataset: ZSZABO.YBATL
      dataset_backup: ZSZABO.BKUP.YBATL
      spec: "{{ env.spec.common_pds_txt_spec }}"
      encoding:
        from: IBM-037
        to: IBM-1047
      text_subs:
        - pattern: CDRU
          replace: CDRX
    ```

4. Create or update the WSC Update JCL templates

    You can find the default JCL templates in the `deploy/<eny-type>/<env-name>/cio-iws/` folder. Please use these to create a JCL member on your system and try to run it. It might be that the JOB will not run at first try. Test and fix the issues. Then, if the JOB is running without issues, you can do the same changes in the template.

    We use Jinja2 tempalates to render the BIND JCL. If you want to learn more about Jinja2 templates go to [Udemy](https://ibm-learning.udemy.com/organization/search/?src=ukw&q=python+jinja)

    !!! warning
        Don't change the j2 tokens (`{{job.eqqmlib}}`)!

    JOB CARD template:
    ``` Jinja
    //{{job.name}}  JOB ({{job.job}}),'{{job.title}}',
    //         MSGCLASS={{job.msgclass}},
    //         CLASS={{job.class}},
    //         REGION=100M,
    //         TYPRUN=SCAN,
    //         NOTIFY=&SYSUID
    //         SET EQQMLIB={{job.eqqmlib}}
    //**********************************************************
    //* IWS UPDATE JCL USING BATL FILES
    //**********************************************************
    {{batl_commands}}
    ```

    JOB STEP template:
    ``` Jinja
    {% for cmd in batlcmd -%}
    //{{cmd['stepname']}} EXEC PGM=EQQYLTOP,REGION=0M
    //EQQMLOG  DD SYSOUT=*
    //EQQMLIB  DD DISP=SHR,DSN=&EQQMLIB
    //EQQDUMP  DD SYSOUT=*
    //SYSUDUMP DD SYSOUT=*
    //SYSIN    DD *
    {{cmd['batl']}}
    /*
    //********************************************************************
    {% endfor %}
    ```


5. Add your IWS Update module to your deployment

    Edit the `dbb_static_package.properties` file and add the CIO_IWS_UPDATE module and set the deployment type to `BATL`

    Please follow the instructions in [Deployment Flow](./deployment-flow.md)

    ``` yaml
    - name: CIO CREATE/UPDATE WORKLOAD SCHEDULER APPLICATION ACTIVITIES
    short_name: CIO_IWS_APPLICATION_UPDATE
    description: |
      sample iws application create upate activities
    actions:
      - name: CIO IWS APPLICATION ACTIONS
        short_name: CIO_IWS_APPLICATION_UPDATE
        description: |
          sample iws application create upate actions
        states:
           - UNDEFINED
        steps:
          - name: CIO IWS APPLICATION UPDATE
            short_name: CIO_IWS_UPDATE
            description: |
              sample iws update
            properties:
            - key: template
              value: cio_iws_update
            tags:
              - always
            plan_tags:
              - always
        tags:
          - always
        plan_tags:
          - always
    types:
      - name: 'BATL'
    is_artifact: True
    tags:
      - always
    plan_tags:
      - always

    ```

5. Exporting your applications

    A new tool will be available soon!!!


