## Migrating Your Source from PDS (Partitioned Data Set) to Github

{%
include-markdown "../../includes/zos/be-sure-shell.md"
%}

The following explains the necessary code changes required to organize your source code on GitHub for the z/OS Pipeline. At the bottom of the page, you can find a step-by-step example of commands on how it was setup with a sample application.

#### Verify toolkit setup

Migrating PDS to Github requires the [Dependency Based Build toolkit](https://www.ibm.com/docs/en/dbb/1.0.0?topic=migrating-data-sets-git){target=\_blank}. Follow these steps to verify proper installation:

{%
   include-markdown "../../includes/zos/user-note.md"
%}

1. Using your shell, log into USS.
1. Type the following command:
```bash
echo $DBB_HOME
```
1. You should see a path leading to a dbb folder. It will look something like:
```bash
/usr/share/pipeline_tools/dbb
```

There is one item to emphasize related to the above commands:

- `$DBB_HOME` is an environment variable.  Small programs ("scripts") in Unix rely on these variables to be set up correctly.  Their purpose is to allow scripts to be installed anywhere on the machine but to reference their location only once.

If your DBB_HOME is not correct, stop here and send a message in the Slack support channel: [#cio-zos-ci-cd-support](https://my.slack.com/archives/C034Y7URH97){target=\_blank}


#### How to Migrate your Source Code

Assuming you have identified all Partitioned Data Sets (PDS) that are used to store your application's source code, migrate the code using the [Dependency Based Build toolkit's migration script](https://www.ibm.com/docs/en/dbb/1.0.0?topic=migrating-data-sets-git){target=\_blank}.


```bash
$DBB_HOME/migration/bin/migrate.sh -r <PATH_TO_YOUR_REPOSITORY> -m MappingRule[hlq:<app_hlq>,pdsMapping:false,toLower:true,targetDir:src/main/<target_dir>,extension:cbl] "<source_lib>"
```

The following is an example of a command using this script to execute the script for COBOL code. It copies the source from `SAMPLE.HLQ.COBOL` to the `/src/main/cobol` directory in your repository that you set up in the [Repository Setup](./repository-setup.md#clone-your-repo-to-zos-unix-systems-services) guide. Cloned into: `/u/user123/source/`

```bash
$DBB_HOME/migration/bin/migrate.sh -r /u/user123/source/acme-app-repo -m MappingRule[hlq:SAMPLE.HLQ,pdsMapping:false,toLower:true,targetDir:src/main/cobol,extension:cbl] "COBOL"

$DBB_HOME/migration/bin/migrate.sh -r /u/user123/source/acme-app-repo -m MappingRule[hlq:SAMPLE.HLQ,pdsMapping:false,toLower:true,targetDir:src/main/jcl,extension:jcl] "JCL"
```

To run the script, you must paste as one line into your USS terminal.  It is most likely not possible to see the entire command above because it's rendered on the page to force it on one line.  If your move your cursor towards the right, you will see it in its entirety.

Copy this command and change it as follows:

1. Copy it to Notepad or TextEdit and make sure the editor is in raw text mode.
1. Make the necessary changes
1. Copy and paste the command into your USS terminal window and press enter.  It does not matter what folder you are in on your USS machine because $DBB_HOME is set correctly.

This is an example of the command:

`$DBB_HOME/migration/bin/migrate.sh -r /u/u8j5393/GIT/ECMVS/ECMVSTEST -m MappingRule[hlq:RECS.SHARED.ECMVS,pdsMapping:false,toLower:true,targetDir:src/main/asm,extension:asm] "ASM"`

- The `PATH_TO_YOUR_REPOSITORY` is replaced with the user's real path: `/u/u8j5393/GIT/ECMVS/ECMVSTEST`
- The high-level qualifier is `RECS.SHARED.ECMVS`
- The low-level qualifier is `ASM`. This command is not designed to combine the high through to the low qualifiers like `RECS.SHARED.ECMVS.ASM`.  The LLQ must be separate.
- The target directory is `src/main/asm`
- This example is for a **different language**.  The extension has been changed from `extension:cbl` to `extension:asm`.

The following language naming conventions should be used:

| Language | Repo Directory | File Extension |
| -------- | -------------- | -------------- |
| Assembly | asm/           | .asm           |
| BMS      | bms/           | .bms           |
| COBOL    | cobol/         | .cbl           |
| CopyBook | copy/          | .cpy           |
| JCL      | jcl/           | .jcl           |
| PLI      | pli/           | .pli           |
| REXX     | rexx/          | .rexx          |

There are some helpful variations to this command:

1. To import **more than one dataset at a time**, Run it using a mapping file per these instructions: [Running migration using a mapping file](https://www.ibm.com/docs/en/dbb/1.0.0?topic=migrating-data-sets-git#running-migration-using-a-mapping-file){target=\_blank}

1. You can migrate a subset based on a member naming convention by modifying the `LLQ`.  In this example the script only migrates PDS members whose names **start with** `ABC`:

   `$DBB_HOME/migration/bin/migrate.sh -r <PATH_TO_YOUR_REPOSITORY> -m MappingRule[hlq:SAMPLE.HLQ,pdsMapping:false,toLower:true,targetDir:src/main/cobol,extension:cbl] "LLQ(ABC*)"`

