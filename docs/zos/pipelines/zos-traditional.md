# z/OS Traditional Language Pipeline
{%
include-markdown "../../includes/zos/be-sure-github.md"
%}

Now that you have set up your Github repository and migrated your legacy code, you are ready to configure your application to work with CIO CI/CD.

## Step 1: Install the GitHub Application

The Github application enables the Pipeline to read and write the files necessary to run the Pipeline.  To connect a pipeline to your application, the Github Application should be installed in your repository. **Note:** this must be done at least 1 day before encrypting your private key.

{%
  include-markdown "../../includes/github-app-installation.md"
%}

## Step 2: Make Code Changes

You may find it helpful to reference our [test repo](https://github.ibm.com/cio-ci-cd-test/zos-application){target=\_blank} which should have a working set up for a test application.

{%
  include-markdown "../../includes/zos/encode-encrypt.md"
%}

1. Create a **build.yml** file in project root folder with the following structure and merge it into the **default** branch. This branch is also referred to as **main** and sometimes **master**.  Review the [z/OS Pipeline Workflow](../pipelines/workflow.md) page for details.

    <!-- prettier-ignore -->
    ```yaml
    apiVersion: automation.cio/v1alpha1
    kind: RepositoryConfig
    ealImapNumber: EAL-xxxxxx
    build:
      strategy: native-zos
      pipeline: zos-application
      version: v3
      config:
        hlq: CICD1.GENAPP # <CICDUSER>.<reponame>
        deployment-tool: wazi
    release:
      defaultEnvironment: test-env
      environments:
      - name: test-env
        sshHost: zh103tc1.dev.ibm.com # non-production branches hostname
        systemType: dev
        sshUser: cicd1
        sshPrivateKey: 'encrypted:v1:AAAxxxxx==' # private key from encryption above
      - name: production-env
        sshHost: zh103tc1.prod.ibm.com # production branches hostname
        systemType: production
        sshUser: cicd1
        sshPrivateKey: 'encrypted:v1:AAAxxxxx==' # private key from encryption above
    ```
{%
   include-markdown "../../includes/optional_jira.md"
%}

1. For the hlq field, use this convention: `hlq: <CICDUSER>.<reponame>`. The `reponame` should not exceed eight characters, it should be in all caps, and must start with a letter. The `<CICDUSER>` should match the `sshUser` field for the dev environment under the release section as well, if multiple IDs are being used for release to different environments.

1. In the root of your repository directory, create a directory named `deploy`, and sub directory for each `environment type`. A file called `deploy.yml` needs to be created in each of the sub directories.
    ```
    deploy
    ├── dev
    │   ├── deploy.yml
    └── production
        └── deploy.yml
    ```
??? "OPTIONAL: Environment name deploy path"
    You can also create sub directories under each `environment type`. Please ensure that these sub directories are labeled the same name as the corresponding `environment name`. A file called `deploy.yml` needs to be created in each of the sub directories.
    ```
    deploy
    ├── dev
    │   └── test-env
    │   │   ├── deploy.yml
    │   └── deploy.yml
    └── production
        └── production-env
        │   ├── deploy.yml
        └── deploy.yml
    ```

1. Copy the contents of this example `deploy.yml` file and fill in the respective data. Customize the pieces under spec based on your application deployment. See comments in each section denoted by the `#` sign
    ```yaml
    #*******************************************************************************
    # Licensed Materials - Property of IBM
    # (c) Copyright IBM Corp. 2021. All Rights Reserved.
    #
    # Note to U.S. Government Users Restricted Rights:
    # Use, duplication or disclosure restricted by GSA ADP Schedule
    # Contract with IBM Corp.
    #*******************************************************************************
    ---
    apiVersion: plum.ibm.com/v1alpha1
    kind: Environment
    metadata:
      name: "tivlp02.svl.ibm.com" # any name is OK, recommend host
      version: 1.0.0
    spec:
      # Config for CICS Region
      cics.cicsplex: CICS01
      cics.cmciurl: http://127.0.0.1:1490
      # Config for DB2
      bind.pkg.maxrc: 4
      bind.plan.maxrc: 4
      bind.user: CICD1
      bind.package: GENASA1
      bind.plan: GENAONE
      bind.qualifier: GENAPP
      bind.subsys: DBC1
      db2.sdsnload: DSN.V12R1M0.SDSNLOAD
      db2.bind.pkg.jobcard: "BINDPKG  JOB 'WD-PKGBIND',MSGLEVEL=(1,1),MSGCLASS=R,NOTIFY=&SYSUID"
      db2.bind.plan.jobcard: "BINDPLA  JOB 'WD-PLANBIND',MSGLEVEL=(1,1),MSGCLASS=R,NOTIFY=&SYSUID"
      # PDS config
      pds.mappings:
        - pattern: .*.LOAD # REGEX pattern found in Artifact we want to deploy
          dataset: CICD1.GENAPP.LOADLIB # dataset to deploy to
          dataset_backup: CICD1.GENAPP.BACKJBY.LOADLIB
          spec: common_pds_load_spec
        - pattern: .*.DBRM
          dataset: CICD1.GENAPP.DBRM
          dataset_backup: CICD1.GENAPP.BACKJBY.DBRM
          spec: common_pds_binary_spec
        - pattern: .*.JCL
          dataset: CICD1.GENAPP.PLUM.JCL
          dataset_backup: CICD1.GENAPP.BACKJBY.JCL
          spec: common_pds_txt_spec
      common_pds_load_spec:
        type: LIBRARY
        # space type = tracks
        space_primary: 150
        space_secondary: 300
        record_format: U
        record_length: 0
      common_pds_binary_spec:
        type: LIBRARY
        # space type = tracks
        space_primary: 150
        space_secondary: 300
        record_format: FB
        record_length: 80
      common_pds_txt_spec:
        type: LIBRARY
        # space type = tracks
        space_primary: 150
        space_secondary: 300
        record_format: FB
        record_length: 80
      csd_group: GENASAP
      cmci_default_action: 'NEWCOPY'
      # JCL SUBS
      job_name: "JENKINS"
      job_param:  "SYSAFF=BMS2"
      jcl_subs:
        - regex: "&JOBNAME"
          replace: "{{ environment['job_name'] }}"
        - regex: "&JOBPARM"
          replace: "{{ environment['job_param'] }}"
    ```

1. {%
       include-markdown "../../includes/detect_secrets.md"
    %}

## Push Code to GitHub
Navigate in your terminal to the base directory of your source code.

Add the files to push to Github:
```
git add .
```

Commit the files:
```
git commit -m "code changes for pipeline configuration"
```

Push the changes to Github:
```
git push origin main
```

Once you pushed all the files above into GitHub, you'll be ready to start using the service. Next, refer to the [z/OS Pipeline Workflow](../pipelines/workflow.md) page.
