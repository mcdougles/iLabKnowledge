# Deployment Type

## Description

The pipeline uses deployment types to differentiate artifacts and assign proper deployment actions to them. First, you define a new deployment type, then assign artifacts to it, finally you assign the artifact type to a deplyoment module.

For example:

- DBRM is a deployment type. You assign it to DBRM module files and you want to copy those to the appropriate library and then execute a BIND command using the BIND deployment module.
- JCL is another type. You only want to copy them to a library. But, there could another set of JCLs which you want to execute. So, you have to differentiate the two types.  Therefore, you create a new deployment type for these JCLs, e.g., EJCL. You can define a deployment type with any name.

**Deployment types are not the same as file extensions.**

## Configuration
1. Deployment type

    To configure your new deployment type you should edit the `file.properties` in the `application-conf` directory. Create a new deployment type and assign the selection pattern:

    ```
    transfer_deployType = <deploy_type_name> :: <search_pattern>, <search_pattern>, ...
    ```

    - **deploy_type**: the name of your deployment type, you can use any (meaningful) name
    - **search_pattern**: file selection pattern which will select the files you want to be assigned to the deploy type

    *Example*:
    ```
    transfer_deployType = EXECUTE :: **/execjcl/*.jcl
    transfer_deployType = COPY :: **/jcl/*.jcl
    ```

2. Assign deployment type to a deplyoment module

    Now, edit the the file `dbb_static_package.properties` in the `application-conf` directory.

    See: [Deployment Flow](../deployment/deployment-flow.md) configuration chapter

    *Example*:

    Use the Member Copy module for `COPY` type
      ``` yml
      - name: COPY_JCL
        description: |
          This activity is dedicated to JCL files
        actions:
            - name: ADD
              description: |
                This action is applicable when we add new artifacts .
              states:
                 - NEW
                 - UNDEFINED
              steps:
              - name: MEMBER_COPY (check available modules)
        types:
          - name: 'COPY'
        isArtifact: 'true'
      ```

    Execute JOBs assigned to `EXECUTE` type using the Execute Job module:

    ``` yml
      - name: EXECUTE_JOBS
         description: |
           This activity is dedicated to JCL files
         actions:
             - name: ADD
               description: |
                 This action is applicable when we add new artifacts .
               states:
                  - NEW
                  - UNDEFINED
               steps:
               - name: EXECUTE_JOB (check available modules)
         types:
           - name: 'EXECUTE'
         isArtifact: 'true'
    ```
