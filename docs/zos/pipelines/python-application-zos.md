# z/OS Python Application Pipeline

By the end of this guide, you should be able to build and publish (to Artifactory) your Python Application on z/OS. Your repository should be set up such that every push will trigger a build.

{% include-markdown "../../includes/contact_us.md" %}

## Step 1: Complete Checklist

Make sure the following are true before proceeding:

- Your python code can be built for python version 3.9
- Have a z/OS mainframe to be able to run builds on.

## Step 2: Install the GitHub Application

{%
    include-markdown "../../includes/github-app-cronjob.md"
%}

{%
    include-markdown "../../includes/github-app-installation.md"
%}

## Step 3: Generate GitHub Access Token

{%
    include-markdown "../../includes/github_access_token.md"
%}

## Step 4: Make Code Changes

1.  Ensure you commit a **requirements.txt** file. CIO CI/CD uses pip to install the packages as listed in these files. The CI/CD team recommends running `pip freeze` in order to ensure the versions of these packages do not change unexpectedly. [See more here](https://pip.pypa.io/en/stable/cli/pip_freeze/).
    You may find it helpful to reference our [test repo](https://github.ibm.com/cio-ci-cd-test/zos-python-library) which should have a working setup for a test application.

1.  {%
      include-markdown "../../includes/zos/encode-encrypt.md"
    %}

1. Create a **build.yml** file in project root folder with the following structure and merge it into the **default** branch. This branch is also referred to as **main** and sometimes **master**.  Review the [z/OS Pipeline Workflow](../pipelines/workflow.md) page for details.

    <!-- prettier-ignore -->
    ```yaml
    apiVersion: automation.cio/v1alpha1
    kind: RepositoryConfig
    ealImapNumber: EAL-xxxxxx
    build:
      strategy: native-zos
      pipeline: zos-python-application
      version: v1
      config:
        destination-path: </path-to-deploy-python-app-on-system> # The absolute path to deploy your application to. (This path needs to exist before the pipeline is triggered)
    release:
      defaultEnvironment: test-env
      environments:
      - name: test-env
        sshHost: <z/os-host> # non-production branches hostname
        systemType: dev
        sshUser: <user-id> # the user name that begins with the 3 character application prefix, followed by letter 'd' for dev, followed by '001'
        sshPrivateKey: 'encrypted:v1:AAAxxxxx==' # private key from encryption above
      - name: production
        sshHost: <z/os-host> # production hostname
        systemType: production
        sshUser: <user-id> # the user name that begins with the 3 character application prefix, followed by letter 'p' for production, followed by '001'
        sshPrivateKey: 'encrypted:v1:AAAxxxxx==' # private key from encryption above
    ```
    {%
      include-markdown "../../includes/optional_jira.md"
    %}

1.  {%
       include-markdown "../../includes/detect_secrets.md"
    %}

1.  Review the `build.yml` file.

1.  Create a `pyproject.toml` file to store your project metadata. At a minimum, you should provide the name and version in this file. More information about the `pyproject.toml` file can be found at: [PEP 621 -- Storing project metadata in pyproject.toml](https://www.python.org/dev/peps/pep-0621/)

See the following example of a minimal `pyproject.toml`:

```toml
[project]
name = "cio-ci-cd-zos-python-application"
version = "0.0.1"
dependencies = [
]

[build-system]
requires = ["setuptools>=61.0"]
build-backend = "setuptools.build_meta"
```

!!! info "Package Dependencies"
    The python dependencies that are available to use on this pipeline are located on the `sys-zos-ecosystem-tools-team-build-inventory-pypi-virtual` Artifactory repository. This Artifactory repository is managed by the IBM Z Systems Open Source. If you have any questions or comments regarding certain packages, please contact the [IBM Z System Open Source Team](https://self-service.taas.cloud.ibm.com/teams/sys-zos-ecosystem-tools-team).

Optional:

{%
  include-markdown "../../includes/zos/unit-test-and-lint.md"
%}
## Step 5: Push Code and Wait for Build

{%
  include-markdown "../../includes/push-code-wait.md"
%}

## Step 6: Release Management

Review the [z/OS Pipeline Workflow](../pipelines/workflow.md) guide for instructions on how to deploy a release to production.

## Optional - Installing the Published Artifacts

1. This pipeline publishes to the `txo-cio-dx-team-pypi-local` Artifactory repository. Configure pip to resolve from this repository with the following command:

```bash
$ pip3 config --user set \
  global.extra-index-url \
  https://:<ARTIFACTORY-TOKEN>@na.artifactory.swg-devops.com/artifactory/api/pypi/txo-cio-dx-team-pypi-local/simple
```

The Artifacts can now be installed:

```bash
$ pip3 install cio-ci-cd-zos-python-application==0.0.38
Looking in indexes: https://pypi.org/simple, https://:****@na.artifactory.swg-devops.com/artifactory/api/pypi/txo-cio-dx-team-pypi-local/simple
Collecting cio-ci-cd-zos-python-application
  Downloading https://na.artifactory.swg-devops.com/artifactory/api/pypi/txo-cio-dx-team-pypi-local/cio-ci-cd-zos-python-aplication/0.0.38/cio_ci_cd_zos_python_application-0.0.38-py3-none-any.whl (1.7 kB)
Installing collected packages: cio-ci-cd-zos-python-application
Successfully installed cio-ci-cd-zos-python-application-0.0.38
```

{%
  include-markdown "../../includes/contact_us.md"
%}
