# USS files

Use these modules if you want to copy, archive, delete files on unix subsystem.

## Available Modules

**[uss_copy](https://www.ibm.com/docs/en/SSQ2R2_16.0.0/com.ibm.wazi.deploy.doc/wd_templ_uss_archive.html)**
This building block copies artifacts to a z/OS UNIX System Services directory. It creates the directory if it does not exist.

**[uss_archive](https://www.ibm.com/docs/en/SSQ2R2_16.0.0/com.ibm.wazi.deploy.doc/wd_templ_uss_copy.html)**
This building block copies artifacts from a z/OS UNIX System Services directory to a backup z/OS UNIX System Services directory. It creates the backup directory if it does not exist.

**[uss_delete](https://www.ibm.com/docs/en/SSQ2R2_16.0.0/com.ibm.wazi.deploy.doc/wd_templ_uss_delete.html)**
This building block deletes artifacts from a z/OS UNIX System Services directory.

## Configuration

In your `deploy.yml` create a new section for your unix file mappings under `spec/types`. Define your target and backup directories, encoding and substitution settings.

### Define file operation settings

``` yaml
  - pattern: <selection pattern>
    directory: "{{ env.spec.app_uss_root }}/<target directory>"
    directory_backup: "/{{ env.spec.app_uss_root }}/<backup directory>/<target directory>/"
    dest_mode: <directory access>
    artifact_mode: <file access>
    is_binary: True
    # for text
    encoding:
      from: UTF-8
      to: IBM-1047
```

- **pattern**: regex to match your artifacts in your package. Make sure your regex gives the appropriate result and matches the source folder and the artifacts of your needs.

    You can use regex validator tools to test your expression.  One tool can be accessed at https://regex101.com/ .

- **directory**: the path to your target directory
- **directory_backup**: the path to your target backup directory
- **dest_mode**: the permission on the destination directory
- **artifact_mode**: specify the permission on the artifacts
- **is_binary**: Trueset if file contains binary data. True or False
- **encoding**: if this key exist encoding operation will be performed using `iconv` command
    - **from**: from code table
    - **to**: to code table



## Samples

deploy.yml

``` yaml
# select *.SO files from the package's LIB directory. Define SO deployment type and configure it to be added to the package.
  - pattern: .*\.(LIB)\/.*\.(SO)$
    # define /u/acme as target folder for the unix files, the file path will be /u/acme/bin/myprogm.so if the file name in github was myprogm.so
    directory: "{{ env.spec.app_uss_root }}/bin/"
    # define /u/acme/back/bin as target folder for the unix files backup
    directory_backup: "/{{ env.spec.app_uss_root }}/back/bin/"
    # set the file permission to the given value 0777 (chmod 777 <filename>) in the given target directory
    dest_mode: 0777
    # set the file permission of the source artifact to the given value 0755 (chmod 755 <filename>)
    artifact_mode: 0755
    # set the file to binary type
    is_binary: True

  - pattern: .*\.(DOC)\/.*\.(TXT)$
    directory: "{{ env.spec.app_uss_root }}/txt/"
    directory_backup: "{{ env.spec.app_uss_root }}/back/txt/"
    # encode the source file from UTF-8 to IBM-1047 before copying to the target library
    encoding:
      from: UTF-8
      to: IBM-1047
    dest_mode: 0777
    artifact_mode: 0755
```

deployment plan

``` yaml
  - name: COPY/ARCHIVE/DELETE ARTIFACTS TO A USS DIRECTORY ACTIVITIES
    short_name: CRAD_UNIX_FILE
    description: |
      sample unix file activities
    actions:
      - name: COPY/ARCHIVE/DELETE ARTIFACTS TO A USS DIRECTORY ACTIONS
        short_name: CRAD_UNIX_FILE
        description: |
          sample unix file actions
        states:
          - UNDEFINED
        steps:
          - name: TEST STEP FOR USS_COPY
            short_name: TEST_USS_COPY
            description: |
              sample unix file copy
            properties:
              - key: template
                value: uss_copy
            tags:
              - always
            plan_tags:
              - always
          - name: TEST STEP FOR USS_ARCHIVE
            short_name: TEST_USS_ARCHIVE
            description: |
              sample unix file archive
            properties:
              - key: template
                value: uss_archive
            tags:
              - always
            plan_tags:
              - always
          - name: TEST STEP FOR USS_DELETE
            short_name: TEST_USS_DELETE
            description: |
              sample unix file delete
            properties:
              - key: template
                value: uss_delete
            tags:
              - always
            plan_tags:
              - always
        types:
          - name: 'BIN'
          - name: 'TXT'
          - name: 'SH'
        is_artifact: True
        tags:
          - always
        plan_tags:
          - always

```