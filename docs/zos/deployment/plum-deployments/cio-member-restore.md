# Member Restore
## Description

The **Member Restore** module copies the original artifacts from the backup PDS to your target PDS. It can be used for DBRM, LOAD and other text type artifacts. Example: the backup PDS is `ACMED.BKUP.JCL` and the target is `ACMED.JCL`, then mebers will be copied from `ACMED.BKUPJCL` to `ACMED.JCL`

!!! warning
    If you run your build or deploy multiple time already, then the backup dataset contains the previous version of the artifacts, not the original.


## Configuration

1. Define the target for your backup artifacts.

    Use the `dataset_backup` key to define the source for your restore.

{%
    include-markdown "../../includes/zos/target-pds-def.md"
%}




2. Target PDS parameters.

{%
    include-markdown "../../includes/zos/target-pds-dsorg.md"
%}