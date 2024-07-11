# File Transfers

## Desription
There are artifacts which don't require a build (compile, linkedit or any other actions). They are simply copied into the package. For example: JCL, SQL, CLIST, REXX, etc. Assign your artifacts to the Transfer script usin search patterns and define the copy mode.

## Configuration

1. Use `file.properties` file to define which type of artifacts from your commit are assigned to the Transfer process in the build phase.

    ```
    dbb.scriptMapping = Transfer.groovy :: <search_pattern>
    ```

    example:
    ```
    dbb.scriptMapping = Transfer.groovy :: **/*.jcl, **/*.cpy, **/*.incl
    ```

    - **dbb.scriptMapping**: define the 'language' script, in this case the `Transfer.groovy`, then add a comma separated list of file selection patterns.
        - **search_pattern**: search pattern to select artifacts for transfer

2. Edit `packageBuildOutputs.properties` and define the Copy Mode for each target library

    ```
    copyModeMap = ["<last_qualifier>": "<type>", "<last_qualifier>": "<type>", ...]
    ```

    example:

    ```
    copyModeMap = ["DBRM": "BINARY", "LOAD": "LOAD", "JCL": "TEXT", ...]
    ```

    - **copyModeMap**: mapping between the copy types and the package libraries

        -**type**: the copy type, options: TEXT, LOAD, BINARY
