# Member Copy
## Description
The Member Copy module copies the artifacts from your package to the target PDS you defined. It can be used for DBRM, LOAD and other text type artifacts. Example: files from `/src/main/jcl` to `ACMED.JCL` on the development environment.


## Configuration

1. Define the target for your artifacts.

{%
    include-markdown "../../includes/zos/target-pds-def.md"
%}




2. Target PDS parameters.

{%
    include-markdown "../../includes/zos/target-pds-dsorg.md"
%}