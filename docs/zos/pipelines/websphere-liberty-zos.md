# z/OS Websphere Liberty Pipeline

The zos-java-mvn-websphere-liberty-v2 pipeline builds a Java WebSphere Liberty Application using Java 11 and Maven, signs and publishes the packages to a remote repository (Artifactory), and deploys it to a z/OS environment.

This pipeline is currently in BETA and interested teams must first contact the CIO CI/CD team for onboarding instructions in the [#dx-platform-support](https://my.slack.com/archives/C02AM16DR19)  Slack Channel.

## Step 1: Generate GitHub Access Token

{%
  include-markdown "../../includes/github_access_token.md"
%}

## Step 2: Encrypt Secrets

You may find it helpful to reference our [test repo](https://github.ibm.com/cio-ci-cd-test/open-liberty-spring-boot/tree/zos) which should have a working set up for a test application.

{%
  include-markdown "../../includes/zos/encode-encrypt.md"
%}

## Step 3: Make Code Changes

1. Create a **build.yml** file in project root folder with the following structure and merge it into the **default** branch. This branch is also referred to as **main** and sometimes **master**.  Review the [z/OS Pipeline Workflow](../pipelines/workflow.md) page for details.

    <!-- prettier-ignore -->
    ```yaml
    apiVersion: automation.cio/v1alpha1
    kind: RepositoryConfig
    ealImapNumber: EAL-xxxxxx
    build:
      strategy: native-zos
      pipeline: zos-java-mvn-websphere-liberty
      version: v2
      config:
        wsl-server-name: <server_name>
    release:
      defaultEnvironment: test-env
      environments:
      - name: test-env
        sshHost: <wsl-z-host> # non-production branches hostname
        systemType: dev
        sshUser: <user-id> # the user name that begins with the 3 character application prefix, followed by letter 'd' for dev, followed by '001'
        sshPrivateKey: 'encrypted:v1:AAAxxxxx==' # private key from encryption above
      - name: production
        sshHost: <wsl-z-host> # production hostname
        systemType: production
        sshUser: <user-id> # the user name that begins with the 3 character application prefix, followed by letter 'p' for production, followed by '001'
        sshPrivateKey: 'encrypted:v1:AAAxxxxx==' # private key from encryption above
    ```

1. Review all of the following configuration parameters for the pipeline that can be set under the `build.config` section.

    | Parameter                     | Type   | Description | Encrypted? | Required? | Default |
    | ----------------------------- | ------ | ----------- | ---------- | --------- | ------- |
    | `detect-secrets-baseline-file` | string | What is the relative path to the detect secrets baseline file? | No | No | `.secrets.baseline` |
    | `mvn-project-path`            | string | What is the directory location of pom.xml in the repo? | No | No | `.` |
    | `mvn-config`                   | string | What is the file location of settings.xml in the repo? | No | No | |
    | `mvn-repo-username`           | string | What is the username used to resolve maven dependencies? | **Yes** | No | |
    | `mvn-repo-password`           | string |  What is the password used to resolve maven dependencies? | **Yes** | No | |
    | `mvn-build-dir`               | string | What is the build directory where Maven package artifacts are output, relative to the project-path? | No | No | `target` |
    | `liberty-config-dir`           | string | What is the directory location where the Liberty configuration files are stored, relative to the project-path? | No | No | `src/main/liberty/config/` |
    | `deploy-to-apps`              | string | Do you want to deploy the package applications to apps folder? (true/false) | No | No | |
    | `wsl-server-name`             | string | What is the directory name for the application under the account's WSL `servers/` directory? | No | **Yes** | |


1. {%
       include-markdown "../../includes/detect_secrets.md"
    %}


Once you committed all the files above into GitHub, you'll be ready to start using the service.

### Private Dependencies

{%
  include-markdown "../../includes/maven-private-dependencies.md"
%}

## Step 4: Set up SSH Public Key Authentication

{%
  include-markdown "../../includes/zos/ssh-authentication.md"
%}

## Step 5: Release Management

Review the [z/OS Pipeline Workflow](../pipelines/workflow.md) guide for instructions on how to deploy a release to production.

## Optional - Set Up Slack Notifications

{%
  include-markdown "../../includes/slack-notifications.md"
%}
