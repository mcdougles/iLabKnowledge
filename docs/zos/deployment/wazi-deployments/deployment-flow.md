# Deployment Method

## Description

Durinq the deployment phase, the pipeline will execute the deployment how you configured it. You should define your deployment activities and the actions executed under the activity in your prefered order and tell which type of artifacts the actual activity should work with. Wazi [Deployment Method](https://www.ibm.com/docs/en/developer-for-zos/16.0?topic=files-deployment-method){:target="_blank" rel="noopener"}.

**For example:**

 1.  deploy programs:

    1. backup load and dbrm
    2. copy load and dbrm
    3. execute binds


  2. deploy artifacts:
    1. backup members
    2. copy jcl, sql, incl, etc

  3. updates:
    1. import qmf procs
    2. update opc applications
    3. execute Db2 ddl

## Configuration

Your deployment flow is configured in the `dbb_static_package.yml` file that is located in `application-conf` folder.

??? "Optional: dbb_static_package.yml deployment paths"
    You can also place `dbb_static_package.yml` in the corresponding sub directories inside the `deploy` directory. Please ensure that these sub directories are labeled the same name as the corresponding `environment name`. A file called `dbb_static_package.yml` needs to be created in each of the sub directories.
    ```
    deploy
    ├── dev
    │   └── test-env
    |   |   ├── dbb_static_package.yml
    │   └── ...
    └── production
        └── production-env
        |   ├── dbb_static_package.yml
        └── ...
    ```


In this file, define your deployment activites and for each activity set the steps to execute. Steps are represented by the deployment modules and performing basic operations on the artifacts in your package.

Create a section for each deployment step under the `activities` key.

Deployment of LOAD, DBRM, SQL and JCL artifacts:
``` yaml
- name: COPY/ARCHIVE/RESTORE/DELETE ARTIFACTS TO A PDS ACTIVITIES
    short_name: CRAD_PDS_MEMBERS
    description: |
      sample pds activities
    actions:
      - name: CRAD_PDS_MEMBERS_ACTIONS
        short_name: CRAD_PDS_MBR
        description: |
          sample pds actions
        states:
          - UNDEFINED
        steps:
          - name: TEST STEP FOR MEMBER_ARCHIVE
            short_name: TEST_ARCHIVE
            description: |
                sample member archive
            properties:
              - key: template
                value: member_archive
            tags:
              always
            plan_tags:
              always
          - name: TEST STEP FOR MEMBER_COPY
            short_name: TEST_COPY
            description: |
                sample member copy
            properties:
              - key: template
                value: member_copy
            tags:
              always
            plan_tags:
              always
          - name: TEST STEP FOR MEMBER_DELETE
            short_name: TEST_DELETE
            description: |
                sample member delete
            properties:
              - key: template
                value: member_delete
            tags:
              always
            plan_tags:
              always
          - name: TEST STEP FOR MEMBER_RESTORE
            short_name: TEST_RESTORE
            description: |
                sample member restore
            properties:
              - key: template
                value: member_restore
            tags:
              always
            plan_tags:
              always
        tags:
          always
        plan_tags:
              always
    types:
      - name: 'LOAD'
      - name: 'DBRM'
      - name: 'JCL'
      - name: 'SQL'
    is_artifact: True
    tags:
      always
    plan_tags:
      always
```

The above action will work with .LOAD and .DBRM files. First, backup all the existing modules and the copy the files from the package into the target PDS.

``` yaml
- name: BIND
  description: |
    This activity is dedicated to execute DB2 BIND commands
  actions:
      - name: DEPLOY
        description: |
          This action is applicable for UNDEFINED
        states:
           - UNDEFINED
        steps:
        - name: CIO_DB2_BIND
          description:
            This step do the BIND of DBRMs
  types:
    - name: 'DBRM'
  isArtifact: 'true'
```

The next action execute the BIND commands for the DBRM modules

- **name**: the descriptive name of your activity
- **short_name**: The optional short name, whose length cannot exceed 30 characters
- **description**: activity description
- **actions**: list of your actions inside the activity
    - **name**: action name
        - **short_name**: The optional short name, whose length cannot exceed 30 characters
        - **description**: action description
        - **states**:
        - **steps**: modules called during the action
            - **name**: module name which you want to execute
            - **short_name**: The optional short name, whose length cannot exceed 30 characters
            - **description**: step description
            - **name**: module name which you want to execute
            - **description**: step description
- **types**: artifacts type selected for the activity
    - **name**: name of the type, defined in the `file.properties`
- **isArtifact**: indicate if the module is using artifacts, `true` or `false`

- **tags**: A string that can be associated with an **activity**, an **action**, or a **step**. It can be used in the The Wazi Deploy generation command to include or exclude activities, actions, or steps during the generation of the application .tar file and of the deployment plan. Values are `always`, `never` or any string value.

- **plan_tags**: A string that can be associated with an **activity**, an **action**, or a **step**. It can be used in the The Python deployment command or The Ansible deployment command to include or exclude activities, actions, or steps in the deployment stage. Values are `always`, `never` or any string value.



