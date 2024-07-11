# Templates

This module uses Jinja2 template files and variables to create new files, e.g.: jcl or shell scripts. These files can be executed or copied in later steps-

## Available Modules

**[template](https://www.ibm.com/docs/en/SSQ2R2_16.0.0/com.ibm.wazi.deploy.doc/wd_templ_template.html)**

With this building block, you can write templates to create shell scripts, JCLs, or any type of file. This building block is not related to artifacts.

## Configuration

In your `deploy.yml` create a new variable under `spec`. Define your template variables which will be passed to the `template` modules. Under this variables define your templete, output and templete variables.

### Define command settings

``` yaml

  <template variable>:
    src: <template source directory>/<template name>.j2"
    dest: <target directory>/<template name>.<ext>"
    mode: <file access>

```

- **template variable**: the name of your template variable. You should use this name when configuring the `template` module for execution.
    - **src**: the location of your Jinja template file
    - **dest**: target directory of the rendered file
    - **mode**: output file access rights

## Samples

deploy.yml

``` yaml
  my_template_jcl:
    src: "{{ env.spec.app_uss_root }}/source_templates/test01-jcl.j2"
    dest: "{{ env.spec.app_uss_root }}/rendered_files/test01.jcl"
    mode: 0777

  templateValues:
  # in J2 template use it like: {{environment['spec']['templateValues']['<variable name>']}}
    SYSIN: DUMMY
```
Jinja template

``` Jinja
//HELLOWRD JOB (ACCT),'Hello World job',CLASS=A,MSGCLASS=A,
//           MSGLEVEL=(2,2),NOTIFY=USERID
//STEPID01 EXEC PGM=IEBGENER
//SYSIN    DD {{environment['spec']['templateValues']['SYSIN']}}
//SYSPRINT DD SYSOUT=*
//SYSUT1   DD *
  Hello World!
/*
//SYSUT2   DD SYSOUT=*
```


deployment plan

``` yaml
  - name: CREATE FILES FROM A TEMPLATE ACTIVITIES
    short_name: CREATE_FILE_FROM_TEMPLATE
    description: |
      sample create file from j2 template activities
    actions:
      - name: CREATE FILES FROM A TEMPLATE ACTIONS
        short_name: CREATE_FILE_FROM_TEMPLATE
        description: |
          sample jcl from j2 template actions
        steps:
          - name: TEST STEP FOR TEMPLATE
            short_name: TEST_TEMPLATE
            description: |
              sample jcl creation from template
            properties:
              - key: template
                value: template
              - key: var_template_name
                value: my_template_jcl # from deploy_sample.yml
            tags:
              - always
            plan_tags:
              - always
    is_artifact: False
    tags:
      - always
    plan_tags:
      - always
```