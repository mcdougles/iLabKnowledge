# Perform BIND Commands

## Description
The BIND module executes BIND commands for each DBRM module in your package generated in the Build phase. DBRMs are created by the compiler you configured when Db2 statements are detected.

The module uses two configuration files where you set your BIND command parameters. The default BIND PACKAGE and BIND PLAN parameters are set in the `deploy.yml` file. You create a bind instruction file,`bindinstr.yml by default`, where you define the BIND parameters that differ from the defaults set in `deploy.yml` for each of your programs.
For a program, the module reads the bind instruction file, if the program is found, the BIND parameters are taken from there and merged with the defaults set in the `deploy.yml`. If no defaults are set, then the parameter is skipped. If the program is not in the instruction file, then default parameters are used. If `nobind` is given as parameter for the `BIND` key, then the program will be not bound.

The module creates a BIND JOB using your parameters and submits it. You will need a BIND JOB template and a STEP template.

![Alt text](../../images/zos/bind-parm-merge.png)

## Configuration

!!! note
    Make sure, your pipline ID has the proper Db2 access!

1. Set the BIND module parameters

    In the `deploy.yml` you will find sections for the BIND module parameters, one for PLANS and one for PACKAGES. Define your Db2 system STEPLIB libraries, the JCL templates for the BIND JOB  and the BIND instruction file which defines the DBRM specific parameters.

    ``` yaml
      db2.bind.steplib.load: <hlq>.SDSNLOAD
      db2.bind.steplib.util: <hlq>.RUNLIB.LOAD
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
      db2.bind.steplib.load: SYS2.DSNDSA1.SDSNLOAD
      db2.bind.steplib.util: SYS2.DSNDSA1.RUNLIB.LOAD
      db2.bind.jcl.template: dev/acme-dev-2/bind/bind-job.template
      db2.bind.step.template: dev/acme-dev-2/bind/bind-job-step.template
      db2.bind.instructions: dev/acme-dev-2/bind/bind-parms.yml
    ```

2. Set your default BIND parameters

    In the `deploy.yml` find the `db2.bind.plan.parms` and `db2.bind.package.parms`. Define the **default** BIND parameters for your BIND commands according to  your environment (dev, test or prod). Define all the BIND parameters which are the same for most your BIND commands.

    ``` yaml
     db2.bind.plan.parms:
        SUBSYSTEM: DB2X
        PLAN:
        OWNER: ACMEDBA
        QUALIFIER:
        ENABLE:
        DISABLE:
        DLIBATCH:
        ...

    db2.bind.package.parms:
        SUBSYSTEM: DB2X
        LOCATION: DB2ACME1
        COLLECTION: ACME
        PACKAGE:
        OWNER: ACMEDBA
        QUALIFIER: ACMEDBA
        ENABLE:
        ...
    ```

3. Create your instruction file

    Create a new YAML file in your `deploy/<environment>/bind` directory. You can use any filename. Finaly set the `db2.bind.instructions` parameter to point to your BIND instruction file.
    In the file create a section for each program in your application and define the application specific parameters that are different from the default parameters defined in the `deploy.yml`.

    ``` yaml
    ACME1:                        <- program name (DBRM module name)
      - BIND: package             <- BIND type package or plan
        PACKAGE: DB2ACME1.ACME     <- your custom parameters
        PARM2: value
        PARM3: value
        ...
      - BIND: plan
        PLAN: ACMEPLAN1
        PKLIST: |-
           DB2ACME1.ACME.ACME08M -
           DB2ACME1.ACME.ACME09M -
           DB2ACME1.ACME.ACME12M -
           DB2ACME1.ACME.ACME15M
        PARM2: value
        PARM3: value
        ...
    ACMENBND:
        - BIND: nobind
    DEFAULT:
        - BIND: package
        - BIND: plan

    ```

    If you run the same BIND command for all programs, then you can add `DEFAULT:` as a program. If you set `nobind` for a program then it will be skipped.


4. Create or update your BIND JOB templates

    You will need a JOB card template and a JOB step template.
    Create a file for the JOB card template and for STEP template in your `deploy/<environment>/bind` directory. You can use any filename, but don't use `. jcl` as the extension (eg.: .`tmpl`). You will find the default templates in the same folder.  Be sure to match the filenames defined in the `db2.bind.jcl.template` and `db2.bind.step.template` parameters.
    Use one of your existing BIND JOB and alter the sample template accordingly.

    !!! warning
        Don't change the tokens (`@@token@@`)!

    JOB card template:
    ```
    //@@JOBNAME@@ JOB (<accounting>),<description>,
    //       MSGCLASS=<msgclas>,CLASS=<class>,REGION=<region>
    // EXPORT SYMLIST=*
    //********************************************************************
    @@BINDSTEP@@
    ```
    JOB step template:
    ```
    /@@STEPNAME@@  EXEC PGM=IKJEFT01,COND=(4,LT)
    //STEPLIB  DD  DISP=SHR,DSN=@@DB2LOAD@@
    //         DD  DISP=SHR,DSN=@@DB2UTIL@@
    //SYSPRINT DD  SYSOUT=<sysprint>
    //SYSTSPRT DD  SYSOUT=<systprt>
    //SYSUDUMP DD  SYSOUT=<sysudump>
    //SYSTSIN  DD  *,SYMBOLS=JCLONLY
    @@BINDCMD@@
    /*
    //************************************
    ```
    We suggest to create a JOB on your system using the above templates and fix it until it's running successfuly

5. Add your BIND module to your deployment

    Edit the `dbb_static_package.properties` file and add the BIND module and set the deployment type to `DBRM`, as you defined in your deployment types configuration.

    Please follow the instructions in [Deployment Flow](./deployment-flow.md)

    ``` yaml
    # BIND #############################################################
    - name: BIND
        description: |
          This activity is dedicated to perform BIND actions for DBRMs
        actions:
         name: ADD
           description: |
             This action is applicable when we add new artifacts .
           states:
             - NEW
             - UNDEFINED
           steps:
             - name: CIO_DB2_BIND
        types:
          - name: 'DBRM'
        isArtifact: 'true'
    ```