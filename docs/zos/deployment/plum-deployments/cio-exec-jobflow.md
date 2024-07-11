# Execute JOB Flows

## Desctiption
This module can be used to execute JOB Flows on your system from your Github repository. You can write JCL JOBs and create the JOB flow by defining the order of execution and the conditions.



## Configuration

1.  Create build and packaging configuration for JOB Flow artifacts

    In the `file.properties` define the parameters for JCL and FLOW deployment types. Please check this chapter: [Deployment Types](../build/deployment-types.md)

    **Example:**

    ***file.properties***

    ```
    dbb.scriptMapping = Transfer.groovy :: **/*.jcl,  **/*.flow, ...
    ...
    transfer_flowPDS=${hlq}.FLOW
    transfer_changePDS=${hlq}.EXECJCL.CHG
    ...
    transfer_datasetMapping = transfer_flowPDS :: **/flow/*.flow
    transfer_datasetMapping = transfer_changePDS :: **/execjcl/chg*/*.jcl
    ...
    transfer_deployType = FLOW :: **/*.flow
    transfer_deployType = CJCL :: **/execjcl/chg*/*.jcl
    ```

    ***transfer.properties***

    ```
    transfer_dsOptions=cyl space(1,1) lrecl(80) dsorg(PO) recfm(F,B) dsntype(library) :: transfer_jclPDS, transfer_flowPDS, transfer_changePDS ...
    ```


2. Creating folder structure

    Create a directory for your JOB flows in your git repository under `src/main`. You can use your code editor to create the directory. Then create a sub direectory each time you want to execute a flow. It is recommended to name this directory to refer your actual change.

    **Example:**

    ```
    src
      |-main
         |-jcl
         |-execjcl
            |-chg00001
            |-chg00002
               |-jcl2exec1.jcl
               |-jcl2exec2.jcl
               |-jcl2exec3.jcl
            ...
            |-chg0000n

    ```
3. Create you flow definition file

    In another directory in `src/main`, eg.: `flows` create a yaml type file and define your JOB flow execution. The file name should refer to the actual change, so you can follow which definition file belongs to a change.

    ``` yml
    flow:
      - job: <name>
        jcl: <filename>
        conditions:
          rc: <condition>
      - job: <name>
        jcl: <filename>
        conditions:
          rc: <condition>
      - job: <name>
        jcl: <filename>
        conditions:
          rc: <condition>
    ```
    - **job**: the name of the JOB
    - **jcl**: the jcl file name of the JOB
    - **conditions**: JOB execution conditions
      - **rc**: the return code condition for the JOB. It works the same way as the COND parameter.

      Use python syntax like:

        GT : `>`	Greater than
        GE : `>=` Greater than or equal to
        EQ : `==` Equal to
        LT : `<` Less than
        LE : `<=` Less than or equal to
        NE : `!=` Not equal to

      Example: `COND(4, GE)` : `4 >=`.

    **Example**:
    Create a file `my-change-flow.flow` under `src/main/flows` and add the following content to execute the three JOBs `JCL2EXEC1`, `JCL2EXEC2`, `JCL2EXEC2`

    ``` yml
    ---
    flow:
      - job: JCL2EXEC1
        jcl: jcl2exec1.jcl
        conditions:
          rc: 4>= # COND(4, GE), 4 >= RC
      - job: JCL2EXEC2
        jcl: jcl2exec2.jcl
        conditions:
          rc: 500== # COND(500, EQ), 500 == RC
      - job: JCL2EXEC3
        jcl: jcl2exec3.jcl
        conditions:
          rc: 16< # COND(16, LT), 16 < RC

    ```

    - **job**: the name of your JOB
        - **jcl**: the jcl file name for your JOB
        -  **conditions**: define the conditions for the JOB
            - **rc**: define the expected return code for your JOB, example: `4>=` this is similar to  `COND(4, GE)`, which is: `4 >= RC`

4. Add your JOB Flow module to your deployment

    Edit the `dbb_static_package.properties` file and add the CIO_EXEC_JOBFLOW module and set the deployment type to `FLOW`

    Please follow the instructions in [Deployment Flow](./deployment-flow.md)

    ```yml
    - name: JOB_FLOW
      description: |
        This activity is dedicated to update DB2 objects using DDL files
      actions:
          - name: ADD
            description: |
              This action is applicable when we add new artifacts .
            states:
               - NEW
               - UNDEFINED
            steps:
            - name: CIO_EXEC_JOBFLOW
      types:
        - name: 'FLOW'
      isArtifact: 'true'
    ```