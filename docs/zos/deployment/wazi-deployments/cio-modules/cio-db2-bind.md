# Perform BIND Commands

## Description

The BIND module executes BIND commands for each DBRM module in your package generated in the Build phase. DBRMs are created by the compiler you configured when Db2 statements are detected.

The module uses two configuration files where you set your BIND command parameters. The default BIND PACKAGE and BIND PLAN parameters are set in the `deploy.yml` file. You create a bind instruction file,`bindinstr.yml` by default, where you define the BIND parameters that differ from the defaults set in `deploy.yml` for each of your programs.
For a program, the module reads the bind instruction file, if the program is found, the BIND parameters are taken from there and merged with the defaults set in the `deploy.yml`. If no defaults are set, then the parameter is skipped. If the program is not in the instruction file, then default parameters are used. If `nobind` is given as parameter for the `BIND` key, then the program will be not bound.

The module creates a BIND JOB using your parameters and submits it. You will need a BIND JOB template and a STEP template.

![Alt text](../../images/zos/bind-parm-merge.png)

## Configuration

!!! note
    Make sure, your pipline ID has the proper Db2 access!

1. Set the BIND module parameters

    In the `deploy.yml` you will find sections for the BIND module parameters, one for PLANS and one for PACKAGES. Define your Db2 system STEPLIB libraries, the JCL templates for the BIND JOB  and the BIND instruction file which defines the DBRM specific parameters.

    ``` yaml
      # DB2 libraries
      db2.bind.steplib.load: <SDSNLOAD library>
      db2.bind.steplib.util: <RUNLIB.LOAD library>

       templates and instructions
      db2.bind.jcl.template: <path>/<template>
      db2.bind.step.template: <path>/<template>
      db2.bind.instructions: <path>/<bind>.yml
    ```

    - **db2.bind.steplib.load**: STEPLIB parameter for the BIND JOB, pointing to SDSNLOAD
    - **db2.bind.steplib.util**: STEPLIB parameter for the BIND JOB, pointing to RUNLIB.LOAD
    - **db2.bind.jcl.template**: the location of your BIND JOB tempate, default: `deploy/<eny-type>/<env-name>/bind/bind_job.template`
    - **db2.bind.step.template**: the location of your BIND JOB STEP tempate, default: `deploy/<eny-type>/<env-name>/bind/bind_job_step.template`
    - **db2.bind.instructions**: the location of the BIND instruction file, default: `deploy/<eny-type>/<env-name>/bind/bind-parms.yml`

    **Examples**:

    ``` yaml
      default_db2_sdsnload: ACME.V12R1M0.SDSNLOAD
      default_db2_runlibload: ACME.V12R1M0.RUNLIB.LOAD

      # DB2 libraries
      db2.bind.steplib.load: "{{env.spec.default_db2_sdsnload}}"
      db2.bind.steplib.util: "{{env.spec.default_db2_runlibload}}"

      # templates and instructions
      db2.bind.jcl.template: dev/acme-dev/cio_bind/bind-job.template
      db2.bind.step.template: dev/acme-dev/cio_bind/bind-job-step.template
      db2.bind.instructions: dev/acme-dev/cio_bind/bind-parms.yml
    ```

2. Set your default BIND parameters

    In the `deploy.yml` find the `db2.bind.plan.parms` and `db2.bind.package.parms`. Define the **default** BIND parameters for your BIND commands according to  your environment (dev, test or prod). Define all the BIND parameters which are the same for most your BIND commands.

    ``` yaml
    # BIND PLAN
    db2.bind.plan.parms:
      SUBSYSTEM: DB2X
      PLAN:
      OWNER: ACMEDBA
      QUALIFIER:
      ENABLE:
      DISABLE:
      DLIBATCH:
      CICS:
      IMSBMP:
      IMSMPP:
      PKLIST:
      NODEFER:
      DEFER:
      ACQUIRE: USE
      ACTION: REPLACE
      RETAIN:
      CACHESIZE:
      CURRENTDATA: 'YES'
      CURRENTSERVER:
      DBPROTOCOL: DRDA
      DEGREE: ANY
      DISCONNECT:
      DYNAMICRULES: R
      ENCODING: EBCDIC
      EXPLAIN: 'NO'
      FLAG: I
      IMMEDWRITE:
      ISOLATION: CS
      KEEPDYNAMIC:
      REOPT:
      OPTHINT:
      PATH:
      RELEASE: C
      ROUNDING:
      SQLRULES:
      VALIDATE: BIND
      CONCURRENTACCESSRESOLUTION:
      PROGAUTH:

    # BIND PACKAGE
    db2.bind.package.parms:
      SUBSYSTEM: DB2X
      LOCATION: DB2XZTF
      COLLECTION: ACME
      PACKAGE:
      OWNER: ACMEDBA
      QUALIFIER: ACMEDBA
      ENABLE:
      DISABLE:
      DLIBATCH:
      CICS:
      IMSBMP:
      IMSMPP:
      MEMBER:
      LIBRARY: "'ACME.DBRM'"
      COPY:
      COPYVER:
      DEPLOY:
      COPYVER:
      DEFER:
      NODEFER:
      ACTION: REPLACE
      REPLVER:
      CURRENTDATA: 'NO'
      DBPROTOCOL: DRDA
      DEGREE:
      DESCSTAT:
      QUERYACCELERATION:
      GETACCELARCHIVE:
      ACCELERATOR:
      DYNAMICRULES: R
      ENCODING: EBCDIC
      EXPLAIN: 'NO'
      FLAG: I
      IMMEDWRITE:
      ISOLATION: CS
      KEEPDYNAMIC:
      REOPT:
      OPTHINT:
      PATH:
      ROUNDING:
      RELEASE:
      SQLERROR:
      VALIDATE: BIND
      EXTENDEDINDICATOR:
      CONCURRENTACCESSRESOLUTION:
      APREUSE:
      APCOMPARE:
      GENERIC:
      BUSTIMESENSITIVE:
      SYSTIMESENSITIVE:
      ARCHIVESENSITIVE:
      APPLCOMPAT:
    ```

3. Create your instruction file

    Create a new YAML file in your `deploy/<environment>/bind` directory. You can use any filename. Finaly set the `db2.bind.instructions` parameter to point to your BIND instruction file.
    In the file create a section for each program in your application and define the application specific parameters that are different from the default parameters defined in the `deploy.yml`.

    ``` yaml
    ACMDBRM0:                         <- program name (DBRM module name)
      - BIND: package                 <- BIND type package or plan
        SUBSYSTEM: DB2X
        LOCATION: DB2XACME            <- your custom parameters
        COLLECTION: ACMECOLL
        OWNER: DB2XADM
        QUALIFIER: DB2XADM
        PACKAGE: DB2X.ACME
        CICS: 'YES'

      - BIND: plan
        PLAN: ACMEPLAN
        PKLIST: |-
          DB2X.ACME.ACMDBRM1 -
          DB2X.ACME.ACMDBRM2 -
          DB2X.ACME.ACMDBRM3 -
          DB2X.ACME.ACMDBRM4
        ACTION: ADD
        ENCODING: UNICODE
    ```

    If you run the same BIND command for all programs, then you can add `DEFAULT:` as a program. If you set `nobind` for a program then it will be skipped.


4. Create or update your BIND JOB templates

    You will need a JOB card template and a JOB step template.
    Create a file for the JOB card template and for STEP template in your `deploy/<environment>/bind` directory. You can use any filename, but don't use `. jcl` as the extension (eg.: .`tmpl`). You will find the default templates in the same folder.  Be sure to match the filenames defined in the `db2.bind.jcl.template` and `db2.bind.step.template` parameters.
    Use one of your existing BIND JOB and alter the sample template accordingly.

    We use Jinja2 tempalates to render the BIND JCL. If you want to learn more about Jinja2 templates go to [Udemy](https://ibm-learning.udemy.com/organization/search/?src=ukw&q=python+jinja)

    !!! warning
        Don't change the j2 tokens (`{{pkg_commands}}`)!

    JOB card template:
    ``` Jinja
    //PPLBIND  JOB (JCL,XXXX),'PIPELINE DB2 BIND ',
    //       MSGCLASS=H,CLASS=A,REGION=100M
    //**********************************************************
    //* DB2 BIND JCL
    //**********************************************************
    // EXPORT SYMLIST=*
    //********************************************************************
    {{pkg_commands}}
    //**********************************************************************
    {{plan_commands}}
    ```

    JOB step template:
    ``` Jinja
    //{{stepname}}  EXEC PGM=IKJEFT01,COND=(4,LT)
    //STEPLIB  DD  DISP=SHR,DSN={{db2load}}
    //         DD  DISP=SHR,DSN={{db2util}}
    //SYSPRINT DD  SYSOUT=*
    //SYSTSPRT DD  SYSOUT=*
    //SYSUDUMP DD  SYSOUT=*
    //SYSTSIN  DD  *,SYMBOLS=JCLONLY
      {% for cmd in bndcmd['cmd_list'] -%}
      {% for key, value in cmd.items() -%}
      {{ key }}: {{ value }}
      {% endfor %}
      {% endfor %}
    /*
    ```
    We suggest to create a JOB on your system using the above templates and fix it until it's running successfuly

5. Add your BIND module to your deployment

    Edit the `dbb_static_package.properties` file and add the BIND module and set the deployment type to `DBRM`, as you defined in your deployment types configuration.

    Please follow the instructions in [Deployment Flow](./deployment-flow.md)

    ``` yaml
    - name: CIO DB2 BIND USING INSTRUCTION FILE ACTIVITIES
      short_name: CIO_DB2_BIND_INSTUCTIONS
      description: |
        sample db2 bind plan and package using instruction file activities
      actions:
        - name: CIO DB2 BIND INSTRUCTIONS ACTIONS
          short_name: CIO_DB2_BIND_INSTUCTIONS
          description: |
            sample db2 bind plan and package using instruction file actions
          states:
             - UNDEFINED
          steps:
            - name: CIO DB2 BIND PLAN PACKAGE
              short_name: CIO_DB2_BIND
              description: |
                sample db2 bind plan package
              properties:
              - key: template
                value: cio_db2_bind
              tags:
                - always
              plan_tags:
                - always
          tags:
            - always
          plan_tags:
            - always
      types:
        - name: 'dbrm'
        - name: 'DBRM'
      is_artifact: True
      tags:
        - always
      plan_tags:
        - always
    ```