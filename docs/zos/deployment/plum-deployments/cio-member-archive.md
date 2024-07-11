# Member Archive
## Description
The **Member Archive** module copies the original artifacts from the target PDS you defined to a backup PDS. It can be used for DBRM, LOAD and other text type artifacts. Example: the target PDS is `ACMED.JCL` and the `dataset_backup` is defined as `ACMED.BKUP.JCL`, then the member which has the same name as the one in the package will be copied to `ACMED.BKUP.JCL`

!!! warning
    If you build or deploy the same Pull Request multiple times, the first archive will backup the original version of your artifact. But the second run will perform the backup again and overwrite the original with the new version in the backup. Make sure you save your originls after the first run!


## Configuration

1. Define the target for your backup artifacts.

    Use the `dataset_backup` key to define the target for your backup.

{%
    include-markdown "../../includes/zos/target-pds-def.md"
%}




2. Target PDS parameters.

{%
    include-markdown "../../includes/zos/target-pds-dsorg.md"
%}