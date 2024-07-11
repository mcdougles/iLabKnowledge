# Packaging

## Description
You can configure how your artifacts are moved (transferred) into the deployment package after the build process. Define a relation between the target PDS libraries and the selected artifacts from you change set. You can select artifacts by defining search patterns.


## Configuration

Packaging configuration is done by editing the `file.properties` in the `application-conf` directory

1. Define a variable and set the target library

    ```
    transfer_<name>PDS=${hlq}.<library>
    ```

    - **transfer_<name\>PDS**: the variable name for the target library.
    - **${hlq}**: referes the `hlq` valule in your `build.yml`. We suggest to create the package under your pipeline users HLQ.
    - **<name\>**: can be any short text

    *Example*:

    ```
    transfer_execPDS = ${hlq}.EXECJCL
    transfer_jclPDS = ${hlq}.JCL
    transfer_opcPDS = ${hlq}.OPC.JCL
    transfer_sqlPDS = ${hlq}.SQL
    transfer_rexxPDS = ${hlq}.REXX
    ```

2. Create a mapping between the target package library and the source artifacts

    Define the mapping variable and assign a selection pattern to the target library

    ```
    transfer_datasetMapping = transfer_<name>PDS :: <pattern>
    ```

    - **transfer_datasetMapping**: the transfer mapping variable. **Don't change it!**
    - **transfer_<name\>PDS**: the target library definition variable you created above
    - **<pattern\>**: file selection pattern

    *Example*:

    ```
    transfer_datasetMapping = transfer_jclPDS :: **/jcl/*.jcl
    transfer_datasetMapping = transfer_execPDS :: **/execjcl/*.jcl
    transfer_datasetMapping = transfer_chgPDS :: **/db2chgs/chg*/*.sql
    ```
