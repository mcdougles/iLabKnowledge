# DB2 Modules

This module is itended to execute BIND PLAN and BIND PACKAGE commands for the DBRM module artifacts.

## Available Modules

**[db2_bind_package](https://www.ibm.com/docs/en/SSQ2R2_16.0.0/com.ibm.wazi.deploy.doc/wd_templ_db2_bind_package.html)**

This building block binds the DBRMs into a package by creating the bind package JCL and running it.

**[db2_bind_plan](https://www.ibm.com/docs/en/SSQ2R2_16.0.0/com.ibm.wazi.deploy.doc/wd_templ_db2_bind_plan.html)**

This building block binds the DBRMs into a plan by creating the bind plan JCL and running it.


## Configuration

Create a new variable `db2_sys_def` under `spec` in your deployment configuration file for your DB2 system parameters and for BIND PLAN and BIND PACKAGE JOBs.

### DB2 system settings

``` yaml
  db2_sys_def:
    user: <db2 user name>
    qualifier: <DB2 qualifier>
    subsys: <DB2 subsystem>
    sdsnload: <SDSNLOAD library>

    # BIND PACKAGE
    package: <package name>
    package_maxrc: <rc value>
    package_jobcard: <JOB card for the BIND PACKAGE>
    package_action: <BIND PACKAGE command ACTION parameter>

    # BIND PLAN
    plan: <plan name>
    plan_maxrc: <rc value>
    plan_jobcard: <JOB card for the BIND PACKAGE>
    plan_action: <BIND PACKAGE command ACTION parameter>
    plan_pklist: <BIND PKLIST name>
```

- **db2_sys_def**: the name of your DB2 system definition variable.
    - **user**: the owner name of the BIND
    - **qualifier**: BIND qualifier
    - **subsys**: Db2 subsystem name
    - **sdsnload**: SDSNLOAD library name

      Parameters for BIND PACKAGE:

    - **package**: the name of you BIND package
    - **package_maxrc**: highest accepted return code for the BIND JOB
    - **package_jobcard**: JOB CARD parameters for the BIND PACKAGE JOB
    - **package_action**: the ACTION parameter value for the BIND PACKAGE command

      Parameters for BIND PLAN:

    - **plan**: PLAN name for the BIND
    - **plan_maxrc**: highest accepted return code for the BIND JOB
    - **plan_jobcard**: JOB CARD parameters for the BIND PLAN JOB
    - **plan_action**: the ACTION parameter value for the BIND PLAN command
    - **plan_pklist**: List of the packages to be included, with commas as separators.


## Samples

deploy.yml

``` yaml
  default_db2_sdsnload: "ACME.V12R1M0.SDSNLOAD"
  default_package_jobcard: |
    BINDPKG JOB 'WD-PKGBIND'
    //        MSGLEVEL=(1,1),
    //        MSGCLASS=R,
    //        NOTIFY=&SYSUID
  default_plan_jobcard:
    BINDPLA JOB 'WD-PLANBIND'
    //        MSGLEVEL=(1,1),
    //        MSGCLASS=R,
    //        NOTIFY=&SYSUID
  default_db2_user: ACMEDBA
  default_db2_action: "REPLACE"

  db2_sys_def:
    user: "{{ env.spec.default_db2_user }}"
    qualifier: ACMEDB0
    subsys: ACMD
    sdsnload: "{{env.spec.default_db2_sdsnload}}"

    # BIND PACKAGE
    package: ACMEPKG
    package_maxrc: 4
    package_jobcard: "{{env.spec.default_package_jobcard}}"
    package_action: "{{env.spec.default_db2_action}}"

    # BIND PLAN
    plan: ACMEPLN
    plan_maxrc: 4
    plan_jobcard: "{{env.spec.default_plan_jobcard}}"
    plan_action: "{{env.spec.default_db2_action}}"
    plan_pklist: '*.ACMEPKG.*'
```

deployment plan

``` yaml
  - name: DB2 BIND PLAN/PACKAGE ACTIVITIES
    short_name: DB2_BIND_PLAN_PACKAGE
    description: |
      sample db2 bind plan and package activities
    actions:
      - name: BIND PLAN AND PACKAGE FOR DBRMS
        short_name: BIND_DBRMS
        description: |
          sample db2 bind plan and package actions
        states:
           - UNDEFINED
        steps:
          - name: TEST STEP FOR BIND_PACKAGE
            short_name: TEST_BIND_PACKAGE
            description: |
              db2 bind package
            properties:
              - key: template
                value: db2_bind_package
            tags:
              - always
            plan_tags:
              - always
          - name: TEST STEP FOR BIND_PLAN
            short_name: TEST_BIND_PLAN
            description: |
              db2 bind plan
            properties:
              - key: template
                value: db2_bind_plan
            tags:
              - always
            plan_tags:
              - alway
        tags:
          - always
        plan_tags:
          - always
    types:
      - name: 'DBRM'
    is_artifact: True
    tags:
      - always
    plan_tags:
      - always
```