#Member Substitution and Copy

## Description
This module is used to alter the contents of your package artifacts and then copy to the target library. If you need to change the content of your source file before deployment to match the target environment, configure and add this module to your deployment.
For example, if your applications HLQ is refering to the environment like: `ACMED.xxxxx` is development and `ACMET.xxxxx` is test and `ACMEP.xxxxx` is production, you need to change `ACMED` to `ACMET` or `ACMEP`.

## Configuration

1. Define the target for your artifacts.

{%
    include-markdown "../../includes/zos/target-pds-def.md"
%}

2. Target PDS parameters.

{%
    include-markdown "../../includes/zos/target-pds-dsorg.md"
%}

3. Create a dictionary for your substitutuions

    In your `deploy.yml`, add a section called `text_subs` with pattern value pairs under the key: `spec`

    ``` yml
    text_subs:
      - pattern: <regexp>
        replace: <string>
    ```
    *text_subs*: list your pattern - replace pairs
    *pattern*: regular expresison to define the pattern to replace
    *replace*: string to replace the pattern


    **Examples**:
    ``` yml
    text_subs:
      - pattern: "ACMEX"
        replace: "ACMED"
      - pattern: "DBDEVADM"
        replace: "DBTSTADM"
      - pattern: "DEV"
        replace: "TEST"
    # replace the substring LOCAL if it is in a word between dots, like MYAPP.XLOCALV1.JCL
      - pattern: \.\b(.*?)LOCAL(.*?)\b\.
        replace: ".DEV."
    ```

    ``` yml
      # 1st replacement
      - pattern: "DEV"
        replace: "TEST"
      # 2nd replacement
      - pattern: "DBDEVADM"
        replace: "DBTSTADM"
    ```
    The string replacements are happening in the order as you defined. Make sure one of the earlier replecement doesn't conflict with a later one. In the second example, the first replacement will change DEV to TEST, DBDEVADM will be changed to DBTESTADM and so, the third replament will not happen.