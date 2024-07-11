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

    ``` yaml
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

    ``` yaml
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

4. Create dataset mapping for the FLOW deployment type

    In your deployment configuration, under `spec/types` add a new dataset mapping for the FLOW type.

    ``` yaml
    - pattern: .*\.(FLOW)\/.*\.(flow)$
      dataset: "{{ env.spec.app_hlq }}.TEMP.FLOW"
      dataset_backup: "{{ env.spec.app_hlq }}.TEMP.BK.FLOW"
      spec: "{{ env.spec.common_pds_txt_spec }}"
      encoding:
        from: UTF-8
        to: IBM-1047
      text_subs:
        - pattern: (?<=\.)DEV(?=\.)
          replace: "TEST"
      rename_subs:
        - pattern: "ACD"
          replace: "ACT"
    ```

5. Add your JOB Flow module to your deployment

    Edit the `dbb_static_package.properties` file and add the CIO_EXEC_JOBFLOW module and set the deployment type to `FLOW`

    Please follow the instructions in [Deployment Flow](./deployment-flow.md)

    ``` yaml
    - name: CIO EXECUTE JOB FLOWS ACTIVITIES
      short_name: CIO_CIO_EXECUTE_JOBFLOW
      description: |
        sample execution of job flows activities
      actions:
        - name: CIO EXECUTE JOB FLOWS ACTIONS
          short_name: CIO_CIO_EXECUTE_JOBFLOW
          description: |
            sample execution of job flows actions
          states:
             - UNDEFINED
          steps:
            - name: CIO EXECUTE JOB FLOW
              short_name: CIO_EXEC_JOBFLOW
              description: |
                sample job flow execution
              properties:
              - key: template
                value: cio_exec_jobflow
              tags:
                - always
              plan_tags:
                - always
          tags:
            - always
          plan_tags:
            - always
      types:
        - name: 'FLOW'
      is_artifact: True
      tags:
        - always
      plan_tags:
        - always
    ```