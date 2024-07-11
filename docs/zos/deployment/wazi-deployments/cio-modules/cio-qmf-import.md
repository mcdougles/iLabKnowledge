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
    db2.qmf.import.job.maxrc: <rc value>
    db2.qmf.import.schema: <db2 schema>

    db2.qmf.import.template_folder: <folder path>
    db2.qmf.import.job.template: <template>.j2
    db2.qmf.import.step.template: <template>.j2
    db2.qmf.import.instructions: <qmf instruction file path>

    db2.qmf.import.types:
      - <qmf form type>
      - <qmf proc type>
      - <qmf query type>
    ```

    - **db2.qmf.import.job.maxrc**: the maximum allowed return code for the QMF import JOB, usually: `4`
    - **db2.qmf.import.schema**: the target Db2 schema name where you want to import
    - **db2.qmf.import.template_folder**: the path of the folder where QMF JOB templates are stored
    - **db2.qmf.import.job.template**: the file name of your QMF import JOB tempate
    - **db2.qmf.import.step.template**: the file name of the QMF import STEP tempate
    - **db2.qmf.import.instructions**: path to your QMF instruction file, relative to the package directory
    - **db2.qmf.import.types**: the list of your QMF deployment types, as you defined in your `file.properties`


    **Example**:
    ``` yml
    db2.qmf.import.job.maxrc: 4
    db2.qmf.import.schema: ACMEDBA

    db2.qmf.import.template_folder: deploy/dev/acme-dev/cio_qmf/
    db2.qmf.import.job.template: qmfjcl.j2
    db2.qmf.import.step.template: qmfcmd.j2
    db2.qmf.import.instructions: deploy/dev/acme-dev/cio_qmf/qmf.yml

    db2.qmf.import.types:
      - QMFFORM
      - QMFPROC
      - QMFQUERY
      ```

2. Configure the target PDS for QMF members

    For the QMF IMPORT command to work, you will need to have your QMF artifacts as a PDS member on your system. Configure the [**Member Copy**](./cio-member-copy.md) or [**Text Substitution**](./cio-text-sub.md) modules to copy these artifacts into the `<apphlq>.QMFFORM`, `<apphlq>.QMFPROC`, `<apphlq>.QMFQUERY` libraries. You will not need those file for your application to run, you only need them on you MVS when the QMF IMPORT command is executed. You can copy them into a temporary library.

    **Example**:
    ``` yml
    spec:
      # Application HQLQ
      app_hlq: ACME
      app_uss_root: /u/acme

    # QMF
    - pattern: .*\.(QMFFORM)\/.*\.(qmfform)$
      dataset: "{{ env.spec.app_hlq }}.TEMP.QMFFORM"
      dataset_backup: "{{ env.spec.app_hlq }}.TEMP.BK.QMFFORM"
      spec: "{{ env.spec.common_pds_txt_spec }}"

    - pattern: .*\.(QMFPROC)\/.*\.(qmfproc)$
      dataset: "{{ env.spec.app_hlq }}.TEMP.QMFPROC"
      dataset_backup: "{{ env.spec.app_hlq }}.TEMP.QMFPROC"
      spec: "{{ env.spec.common_pds_txt_spec }}"

    - pattern: .*\.(QMFQUERY)\/.*\.(qmfquery)$
      dataset: "{{ env.spec.app_hlq }}.TEMP.QMFQUERY"
      dataset_backup: "{{ env.spec.app_hlq }}.TEMP.BK.QMFQUERY"
      spec: "{{ env.spec.common_pds_txt_spec }}"
    ```

3. Create your instruction file

    The next step is to create or update the QMF import instruction file (use default: `deploy/<environment>/qmf/qmfcmd.yml` or create a new YAML file in your `deploy/<environment>/qmf` directory, you can use any filename). Create a section for each of your QMF objects under the appropriate main category.
    Finally set the `db2.qmf.import.instructions` parameter in the `deploy.yml` to point to your instruction file,
    See the below example:

    ``` yml
    QMFFORM:
      QMFFORM1:
        type: FORM
        member: QMFFORM1
        name: QMF_FORM_1
      QMFFORM2:
        type: FORM
        member: QMFFORM2
        name: QMF_FORM_2
      QMFFORM3:
        type: FORM
        member: QMFFORM3
        name: QMF_FORM_3

    QMFPROC:
      QMFPROC1:
        type: PROC
        member: QMFPROC1
        name: QMF_PROC_1
      QMFPROC2:
        type: PROC
        member: QMFPROC2
        name: QMF_PROC_2
      QMFPROC3:
        type: PROC
        member: QMFPROC3
        name: QMF_PROC_3

    QMFQUERY:
      QMFQUERY1:
        type: QUERY
        member: QMFQRY1
        name: QMF_QUERY_1
      QMFQRY2:
        type: QUERY
        member: QMFQRY2
        name: QMF_QUERY_2
      QMFQRY3:
        type: QUERY
        member: QMFQRY3
        name: QMF_QUERY_3

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
        Don't change the j2 template tokens, like (`{{qmf_commands}}`)!

    We use Jinja2 tempalates to render the BIND JCL. If you want to learn more about Jinja2 templates go to [Udemy](https://ibm-learning.udemy.com/organization/search/?src=ukw&q=python+jinja)

    JOB card template:
        ``` Jinja
        //PPLQIMP  JOB (JCL,XXXX),'PIPELINE QMF IMPORT',
        //*       SCHENV='CDRD0000',
        //*       TYPRUN=SCAN,
        //       MSGCLASS=H,CLASS=A,REGION=100M
        //**********************************************************
        //* QMF BATCH COMMAND RUN JCL
        //**********************************************************
        // EXPORT SYMLIST=*
        // SET DB2SSID=DDA0
        //COPYRX  EXEC PGM=IEBGENER
        //SYSPRINT DD SYSOUT=*
        //SYSIN    DD DUMMY
        //SYSUT1   DD DATA,DLM=@@
         /* REXX RAVINDRA VISHNOI */
         TRACE
         ARG INPARMS
         SSID= ''
         SQLID=''
         CMD = ''
         PARSE VALUE INPARMS WITH 'SYS' '(' SSID ')'
         DROP CMD.;
         "EXECIO * DISKR COMMANDS(STEM CMD. FINIS";
         IF CMD.0 = 0 THEN DO;
           SAY "INPUT COMMANDS FILE IS EMPTY."
           EXIT 4;
         END
          CALL DSQCIX "START (DSQSMODE=BATCH,DSQSSUBS="SSID""
          SAY "DSQ_RETURN_CODE :" DSQ_RETURN_CODE
          SAY "DSQ_MESSAGE_TEXT:" DSQ_MESSAGE_TEXT
         ADDRESS QRW
             SAY LEFT('COMMAND TEXT(FIRST BYTES)',54) "DSQ_RETURN_CODE"
             SAY RIGHT('',54,'-')  "---------------"
          DO I = 1 TO CMD.0;
             CMD.I = STRIP(CMD.I)
             CMD = CMD || CMD.I;
             LEN = LENGTH(CMD)
             IF LASTPOS(' -', CMD) = LEN - 1 |,
                LASTPOS(' +', CMD) = LEN - 1  THEN DO;
                CMD = SUBSTR(CMD,1, (LEN -1));
                ITERATE;
             END;
             IF WORDPOS('IMPORT', CMD) = 1 | WORDPOS('EXPORT', CMD) = 1,
                | WORDPOS('ERASE', CMD) = 1
              THEN DO ;
                 PARSE VALUE CMD WITH VERB TYP OWN '.' NAM X
                 IF SQLID <> OWN THEN DO;
                   CMDS = "RUN QUERY QMFUSER.SET_SQLID(&OWNER='"||OWN||"'"
                   CALL RUNCMD(CMDS)
                   CMDS = ''
                 END;
              END;
             CALL RUNCMD(CMD)
             CMD = '';
          END;
          SAY LEFT('-',70,'-')
         CALL DSQCIX 'EXIT'
         SAY "PROGRAM COMPLETED : " DATE() TIME();
         EXIT;
         RUNCMD: PROCEDURE;
            ARG INCMD;
            ADDRESS QRW INCMD
            IF DSQ_RETURN_CODE <> 0 THEN DO;
              SAY "DSQ_RETURN_CODE :" DSQ_RETURN_CODE
              SAY "DSQ_MESSAGE_TEXT:" DSQ_MESSAGE_TEXT
              EXIT DSQ_RETURN_CODE;
            END;
            SAY SUBSTR(INCMD,1,54)  DSQ_RETURN_CODE
         RETURN;
        @@
        //SYSUT2   DD DISP=(NEW,PASS),DSN=&&TEMP(BATQMF),
        // DCB=(LRECL=80,RECFM=FB,DSORG=PO),UNIT=SYSDA,
        // SPACE=(TRK,(1,10,10))
        //******************************************************************
        //*PROFILE COPY
        //*******************************************************************
        //STEP0   EXEC PGM=IEBCOPY,REGION=2048K
        //SYSPRINT DD DUMMY
        //SYSUT1   DD DSN=&SYSUID..TEMP.ISPPROF,DISP=SHR
        //SYSUT2   DD DSN=&PROF1,DISP=(NEW,PASS),
        //            DCB=(RECFM=FB,BLKSIZE=6160,LRECL=80),
        //            SPACE=(TRK,(5,5,5)),UNIT=SYSDA
        //SYSUT3   DD SPACE=(CYL,(1,1)),UNIT=SYSDA
        //SYSIN    DD *
        COPYOPER   COPY   OUTDD=SYSUT2
                          INDD=((SYSUT1,R))
        /*
        //*******************************************************************
        //* RUN THE BATCH QMF COMMANDS
        //*******************************************************************
        //ISPBATCH EXEC PGM=IKJEFT01,DYNAMNBR=35,REGION=4000K
        //STEPLIB  DD  DISP=SHR,DSN=SYSM.DB2V12.SDSNLOAD
        //         DD  DISP=SHR,DSN=SYSM.DB2V12.SDSNLOD2
        //SYSPROC  DD  DISP=SHR,DSN=&&TEMP(BATQMF)
        //*        DD  DISP=SHR,DSN=SYS1.LCLGSD.CLIST
        //*        DD  DISP=SHR,DSN=SYS1.CMDPROC
        //*        DD  DISP=SHR,DSN=SYS1.MAIN.CLIST
        //*        DD  DISP=SHR,DSN=SYS1.XGROUP.EXEC.VGS
        //*        DD  DISP=SHR,DSN=SYS1.XGROUP.CLIST
        //         DD  DISP=SHR,DSN=ISP.SISPEXEC
        //         DD  DISP=SHR,DSN=ISP.SISPCLIB
        //         DD  DISP=SHR,DSN=SYSM.DB2V12.QMF.SDSQCLTE
        //         DD  DISP=SHR,DSN=SYSM.DB2V12.QMF.SDSQEXCE
        //SYSEXEC  DD  DISP=SHR,DSN=SYSM.DB2V12.QMF.SDSQEXCE
        //ISPLLIB  DD  DISP=SHR,DSN=SYSM.DB2V12.QMF.SDSQLOAD
        //*SPPLIB  DD  DISP=SHR,DSN=SYS1.LCLGSD.PANELS
        //*        DD  DISP=SHR,DSN=SYS1.MAIN.PANELS
        //*        DD  DISP=SHR,DSN=SYS1.MAINPENU
        //ISPPLIB  DD  DISP=SHR,DSN=ISP.SISPPENU
        //         DD  DISP=SHR,DSN=SYS1.SBPXPENU
        //         DD  DISP=SHR,DSN=SYSM.DB2V12.QMF.SDSQPLBE
        //ISPMLIB  DD  DISP=SHR,DSN=ISP.SISPMENU
        //*        DD  DISP=SHR,DSN=SYS1.LCLGSD.MSGS
        //*        DD  DISP=SHR,DSN=SYS1.MAIN.MSGS
        //         DD  DISP=SHR,DSN=SYS1.SBPXMENU
        //         DD  DISP=SHR,DSN=SYSM.DB2V12.QMF.SDSQMLBE
        //*        DD  DISP=SHR,DSN=SYSM.DB2V12.SDSNSPFM
        //ISPSLIB  DD  DISP=SHR,DSN=ISP.SISPSENU
        //         DD  DISP=SHR,DSN=SYSM.DB2V12.QMF.SDSQSLBE
        //ISPTLIB  DD  DISP=SHR,DSN=ISP.SISPTENU
        //ISPPROF  DD  DSN=&PROF1,DISP=OLD
        //ISPLOG   DD  SYSOUT=*,DCB=(LRECL=80,RECFM=FB,BLKSIZE=3200)
        //DSQPRINT DD  SYSOUT=*,DCB=(RECFM=FBA,LRECL=133,BLKSIZE=3990)
        //DSQDEBUG DD  SYSOUT=*,DCB=(RECFM=FBA,LRECL=121,BLKSIZE=3630)
        //DSQEDIT  DD  UNIT=SYSDA,DCB=(RECFM=FBA,LRECL=133,BLKSIZE=1330),
        //  SPACE=(CYL,(1,1))
        //DSQSPILL DD  DSN=&&SPILL,DISP=(,DELETE),
        //  UNIT=(SYSDA,8),SPACE=(CYL,(50,50)),
        //  DCB=(RECFM=F,LRECL=4096,BLKSIZE=4096)
        //ADMGGMAP DD  DISP=SHR,DSN=SYSM.DB2V12.QMF.SDSQMAPE
        //SYSTSPRT DD  SYSOUT=*
        //SYSTSIN  DD  *,SYMBOLS=JCLONLY
        ISPSTART CMD(%BATQMF SYS(&DB2SSID.) )
        /*
        //COMMANDS DD  *
        {{qmf_commands}}
        /*
        //ISPWRK1  DD DISP=NEW,UNIT=VIO,SPACE=(CYL,(1,1)),
        //            DCB=(LRECL=256,BLKSIZE=2560,RECFM=FB)
        //*ISPWRK2  DD DISP=NEW,UNIT=VIO,SPACE=(CYL,(1,1)),
        //*            DCB=(LRECL=256,BLKSIZE=2560,RECFM=FB)
        ```

    JOB step template:
        ``` Jinja
        SET PROFILE  {% raw %}({% endraw %}CONFIRM=NO
        {% for cmd in qmfcmd -%}
        IMPORT {{cmd['type']}} {{schema}}.{{cmd['name']}} +
        FROM '{{cmd['dataset']}}' +
        {% raw %}({% endraw %}MEMBER={{cmd['member']}},SHARE=YES
        {% endfor -%}
        SET PROFILE {% raw %}({% endraw %}CONFIRM=YES
        ```
    We suggest to create a JOB on your system using the above templates and fix it until it's running successfuly


5. Add your QMF IMPORT module to your deployment

    Edit the `dbb_static_package.properties` file and add the QMF IMPORT module and set the deployment type to `SQL` and `FRM` as you defined in your deployment types configuration. Add the Text Substitution module if needed.

    Please follow the instructions in [**Deployment Flow**](./deployment-flow.md)

    **Example**:

    ``` yaml
    - name: CIO QMF OBJECT IMPORT QUERY, PROCEDURE, FORM ACTIVITIES
      short_name: CIO_QMF_OBJECT_IMPORT
      description: |
        sample qmf objet import query procedure form activities
      actions:
        - name: CIO QMF OBJECT IMPORT ACTIONS
          short_name: CIO QMF OBJECT IMPORT
          description: |
            sample qmf objet import query procedure form actions
          states:
             - UNDEFINED
          steps:
            - name: CIO QMF OBJECT IMPORT
              short_name: CIO_QMF_IMPORT
              description: |
                sample qmf import
              properties:
              - key: template
                value: cio_qmf_import
              tags:
                - always
              plan_tags:
                - always
          tags:
            - always
          plan_tags:
            - always
      types:
        - name: 'QMFFORM'
        - name: 'QMFQUERY'
        - name: 'QMFPROC'
      is_artifact: True
      tags:
        - always
      plan_tags:
        - always
    ```


6. QMF Export

    Both QMF Workstation and QMF Workstation for Windows has export functionality. If you export on your mainframe system, QMF will create a PDS and members for each objects. Then you can import them into your repository using the same method described in the [Source Migration](../migration/source-migration.md) chapter under [**Migrating Your Source from PDS (Partitioned Data Set) to Github**](../migration/migrate-pds.md) section.
    If you use QMF on Windows or Mac you can directly export to your local repository folder.


