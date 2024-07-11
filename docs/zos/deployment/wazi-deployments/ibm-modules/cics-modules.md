# CICS Modules

This modules are performing common tasks in CICS using CMCI REST API by initiating PUT requests.

## Available Modules

**[cics_cmci_prog_create](https://www.ibm.com/docs/en/SSQ2R2_16.0.0/com.ibm.wazi.deploy.doc/wd_templ_shell_command.html)**

This building block defines and installs new load modules in a CICS environment.

**[cics_cmci_prog_delete](https://www.ibm.com/docs/en/SSQ2R2_16.0.0/com.ibm.wazi.deploy.doc/wd_templ_cics_delete.html)**

This building block deletes load modules from a CICS environment.

**[cics_cmci_prog_update](https://www.ibm.com/docs/en/SSQ2R2_16.0.0/com.ibm.wazi.deploy.doc/wd_templ_cics_update.html)**

This building block updates load modules in a CICS environment.


## Configuration

Create a new variable under `spec` in your deployment configuration file for your CICS definition parameters

### CICS system settings

Add the following CICS parameters to your dataset mappings for CICS LOAD modules

``` yaml
  types:
    ...
    aliases: <True | False>
    cics_systems:
     - <cics system definition name>
```
- **aliases**: indicate if you use ALIAS-es for your LOAD modules
- **cics_systems**: liat your CICS system names, use the variable names you gave as below


Define your CICS system settings

``` yaml
  <cics system definition>:
    cicsplex: <cics plex name>
    cmci_url: <cmci url>:<cmci port>
    cmci_user: <cmci user>
    cmci_password: <cmci password>
    csd_group: <csd group>
    cmci_action: "{{env.spec.default_cics_cmci_action}}"

  default_cics_cmci_action: <cics action, eg NEWCOPY, PHASEIN >
```

- **cics system definition**: the name of your CICS system definition variable. use this name in your LOAD dataset mapping's `cics_systems` variable,
    - **cicsplex**: the name of your CICS Plex
    - **cmci_url**: the url for the CMCI Rest API service
    - **cmci_user**: username for the CMCI connection
    - **cmci_password**: password of the cmci_user
    - **csd_group**: CSD group name
    - **cmci_action**: the CMCI action, e.g.: NEWCOPY or PHASEIN

- **default_cics_cmci_action**: the CMCI action name, e.g.: NEWCOPY or PHASEIN


## Samples

deploy.yml

``` yaml
  # select *.load files from the package's LOAD directory
    - pattern: .*.LOAD.*\.(LOAD)\/.*\.(LOAD)$
      spec: "{{ env.spec.common_pds_load_spec }}"
      dataset: "{{ env.spec.app_hlq }}.LOAD"
      dataset_backup: "{{ env.spec.app_hlq }}.BK.LOAD"
      is_load: True
      # CICS parameters
      aliases: True
      cics_systems:
        - "{{ env.spec.my_cics_sys_def }}"

    default_cics_cmci_action: "PHASEIN"

    my_cics_sys_def:
      cicsplex: ACME_PLEX
      cmci_url: "http://cmci.acme.ibm.com>:1490"
      cmci_user: ACMECMCI
      cmci_password: "ACMEPWD"
      csd_group: "ACMECSD"
      cmci_action: "{{env.spec.default_cics_cmci_action}}"
```

deployment plan

``` yaml
  - name: INSTALL/UPDATE/DELETE PROGRAMS IN CICS ACTIVITIES
    short_name: IUD_CICS_PROGRAMS
    description: |
      sample cics activities
    actions:
      - name: INSTALL UPDATE DELETE PROGRAMS IN CICS ACTIONS
        short_name: IUD_CICS_PROGRAMS
        description: |
          sample cics actions
        states:
           - UNDEFINED
           # for delete use:
           #- DELETED
        steps:
          - name: TEST STEP FOR PROG_CREATE
            short_name: TEST_CREATE
            description: |
              install a new load mocule in cics
            properties:
            - key: template
              value: cics_cmci_prog_create
            tags:
              - always
            plan_tags:
              - always
          - name: TEST STEP FOR PROG_UPDATE
            short_name: TEST_UPDATE
            description: |
              update a load mocule in cics
            properties:
              - key: template
                value: cics_cmci_prog_update
              - key: var_cics_cmci_action
                value: my_var_cics_cmci_action
            tags:
              - always
            plan_tags:
              - always
          - name: TEST STEP FOR PROG_DELETE
            short_name: TEST_DELETE
            description: |
              remove a load mocule from cics
            properties:
              - key: template
                value: cics_cmci_prog_delete
            tags:
              - always
            plan_tags:
              - always
        tags:
          - always
        plan_tags:
          - always
    types:
      - name: 'CICSLOAD'
    is_artifact: True
    tags:
      - always
    plan_tags:
      - always

```