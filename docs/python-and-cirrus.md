# Guide: Building and Deploying a Python application to Cirrus

By the end of this guide, you should be able to deploy your Python application to Cirrus. Your repository should be set up such that every push will trigger a build and deploy. {% include-markdown "./includes/contact_us.md" %}

{% include-markdown "./includes/cirrus-deployment-strategies.md" %}
## Step 1: Complete Checklist

{%
    include-markdown "./includes/cirrus-complete-checklist.md"
%}

## Step 2: Install the GitHub Application

{%
    include-markdown "./includes/github-app-pipeline.md"
%}

{%
    include-markdown "./includes/github-app-installation.md"
%}

## Step 3: Generate a GitHub Access Token

{%
    include-markdown "./includes/github_access_token.md"
%}

## Step 4: Create your Project and Pipeline on Cirrus

{%
    include-markdown "./includes/cirrus-project-pipeline.md"
%}

## Step 5: Make Code Changes

1.  Ensure you commit either a **Pipfile.lock**, **poetry.lock**, or **requirements.txt** file. CIO CI/CD uses micropipenv to install the packages as listed in these files. Packages cannot be installed with only a Pipfile.

1.  {%
       include-markdown "./includes/build_yml.md"
    %}
    {%
    include-markdown "./includes/optional_jira.md"
    %}

1.  {%
          include-markdown "./includes/apm-configuration.md"
    %}

1.  Choose a pipeline and fill into the **pipeline** field.

    - `python-v3.9-micropipenv-container-image` - microservice built on Python 3.9

1.  Fill in the **cirrus-pipeline-name** with the name of your Pipeline in Cirrus.

1.  {%
       include-markdown "./includes/openshift-cli-access.md"
    %}

1.  {%
       include-markdown "./includes/cirrus_api_key.md"
    %}

1.  Encrypt the Cirrus API Username and Password.

    {%
      include-markdown "./includes/encryption.md"
    %}

    {%
        include-markdown "./includes/encrypt-each-repo.md"
    %}

1.  {%
       include-markdown "./includes/detect_secrets.md"
    %}

1.  Review the `build.yml` file.

    See the following example of the build.yml file for a python pipeline:

    ```yaml
    apiVersion: automation.cio/v1alpha1
    kind: RepositoryConfig
    ealImapNumber: xxxxxx
    build:
      strategy: cirrus-deployment
      pipeline: python-v3.9-micropipenv-container-image
      version: v4
      config:
        cirrus-project-name: rre-cirrus
        cirrus-pipeline-name: rre-test-python-microservice
        distribution-directory: .
        service-port: '8000'
        cirrus-region: us-south2
        cirrus-api-username: 'encrypted:v1:AAABJiD8bVgJb64QpnlTH4+9kugiiKi4QHBbpGOzng0GfOxi3Zn5XLspBMNBNZqkzuaKWFHg3v3qXNkzECZ4FSAnpN/km73bB3C/jWh/Wf0TCVjnXgyWh2aFZKgaeXhRJ5ZTAQhQ9a6oS8/4gwpBeocrutkHOVklA0O1SwtvQcqO9iMGWUGzxGG76kZkK0wFhQ=='
        cirrus-api-password: 'encrypted:v1:AAABJiD8bVhCQXY13u8/xa4B4zbyxbKa6Q5E9eorK2hr0WR1cg7Chnup8eawiOzyIcJh/7IXhEwal25WkAINtPF9tFoeQQCvEaHKPRGyHl517M6aeNf86RWBtsQILO5/9Ih1F04zcn+A1d8H3ijucSPU5nIG7x2GP3TDriOPiHBwmLzk/jtDEeVSqgkop9H7iw916iTalzmRahx77s060xvj4SyBIBU4IYWn0N/Qcg=='
        deploy-verification-memory-request: q256mb
    ```

    {%
    include-markdown "./includes/optional_jira.md"
    %}

1.  Create a `pyproject.toml` file to store your project metadata. At a minimum, you should provide the name and version in this file. If your project uses poetry, then poetry creates this file automatically. More information about the `pyproject.toml` file can be found at: [PEP 621 -- Storing project metadata in pyproject.toml](https://www.python.org/dev/peps/pep-0621/)

    See the following example of a minimal `pyproject.toml`:

    ```toml
    [tool.poetry]
    name = "poetry-example"
    version = "0.1.0"
    description = "A poetry example project used for testing"
    authors = ["Michael Ramos <ramosm@us.ibm.com>"]
    ```

1.  In order to start the application at runtime, a couple of options are supported:

    1. Provide a valid `app.sh` script with the command to execute to start your application.
    2. Provide a valid `app.py` Python file which is automatically executed.

    Note: If both files are present, the above list ordering will be respected (e.g. app.sh being executed first).

1.  After deployment to Cirrus, the CD pipeline executes end-to-end tests\* in order to verify whether a deployment is successful. Similar to the runtime script, a couple of options are supported.
    
    \*CronJob and Job deployments do not run verification tests and therefore this script does not apply.

    1. Provide a valid `deploy-test.sh` script with the command to execute to test your deployment to Cirrus.
    2. Provide a valid `deploy-test.py` Python file, which tests your deployment to Cirrus .

    Note: If both files are present, the above list ordering will be respected (e.g. deploy-test.sh being executed first). A test file is mandatory. You may write a placeholder, such as `echo 'todo test'` if you do not currently write functional tests.

    {%
       include-markdown "./includes/deployment-verification-pods.md"
    %}

1.  {%
       include-markdown "./includes/deployment-testing.md"
    %}

1.  {%
       include-markdown "./includes/health-check.md"
    %}

1.  Follow the steps outlined in [Cirrus Deployment Customization](cirrus-deployment-customization.md).

Optional:

1. CIO CI/CD uses `pytest` to run unit-tests, you can configure pytest by adding configuration options to a `pytest.ini` file or to the `pyproject.toml`.
1. CIO CI/CD uses `flake8` for linting, you can configure `flake8` by adding a `.flake8` file to your repository.

### Jobs & CronJobs

**Job**

{%
    include-markdown "./includes/cirrus-job.md"
%}

**CronJob**

{%
    include-markdown "./includes/cirrus-cron-job.md"
%}

### Private Dependencies

If your code uses private dependencies, you must provide **encrypted** credentials in your `build.yml` file.

1.  Assemble a credentials string containing either four or six fields, separated by `;;`.
    <!-- prettier-ignore -->
    1. Fill out the first four fields, as they are required. They must be the URL, repository name, username, and token for accessing the package repository.
    1. If you use `Pipfile.lock` or `requirements.txt`, fill out a fifth and sixth field with the environment variable names to substitute in your lock file. These will default to `USERNAME` and `PASSWORD` if not provided and are not used with `poetry.lock`.
    1. If your code uses packages from multiple private repositories, use `--` to separate multiple credentials in the credentials string.
    1. Check that your credentials string is formatted as follows:

            https://na.artifactory.swg-devops.com/artifactory/api/pypi/my-project-pypi-virtual/simple;;my-private-repo;;username@ibm.com;;xxxxxx;;MY_USERNAME;;MY_PASSWORD

1.  Encrypt this string as the text in a POST request to the encryption service.

    {%
        include-markdown "./includes/credentials_encrypt.md"
    %}

1.  Store the response cipherText in **private-dependencies**, in your `build.yml` file, under **build.config**:

    ```yaml
    apiVersion: automation.cio/v1alpha1
    kind: RepositoryConfig
    build:
    ...
    config:
        ...
        private-dependencies: "encrypted:v1:AAAB...zz=="
    ```

    These credentials are used while installing dependencies.

    !!! info
    For security purposes, `requirements.txt` and `Pipfile.lock` must use environment variables for authentication credentials, rather than writing the credentials directly into the files. See the [pip reference](https://pip.pypa.io/en/stable/reference/requirements-file-format/#using-environment-variables) and the [Pipenv reference](https://pipenv.pypa.io/en/latest/advanced/#injecting-credentials-into-pipfile-via-environment-variables) for additional information on how to configure your lock file to adopt this practice.

    A `requirements.txt` file that specifies a private package from Artifactory may look like this:

    ```bash
    --extra-index-url=https://${MY_USERNAME}:${MY_PASSWORD}@na.artifactory.swg-devops.com/artifactory/api/pypi/my-project-pypi-virtual/simple
    my-private-package==1.0.0
    PyYAML==6.0
    ```

    A `poetry.lock` file that uses the same repository may contain a section like this:

    ```toml
    [[tool.poetry.source]]
    name = "my-private-repo"
    url = "https://na.artifactory.swg-devops.com/artifactory/api/pypi/my-project-pypi-virtual/simple"
    ```

    The following information is required for each credential:

    - **url**: The URL of the package repository. This should include the protocol. For Poetry users, this **must** match the url listed in `[[tool.poetry.source]]`.
    - **repository name**: The short name of the repository. For Poetry users, this **must** match the name listed in `[[tool.poetry.source]]`.
    - **username**: The username to use for authentication.
    - **token**: The token used for authentication in Artifactory.
    - **username-variable**: The name of the username variable in your lock file. Defaults to `USERNAME`. Poetry users may omit.
    - **token-variable**: The name of the token variable in your lock file. Defaults to `PASSWORD`. Poetry users may omit.

{%
include-markdown "./includes/artifactory-token.md"
%}

### Container Release
{%
    include-markdown "./includes/optional-container-build.md.md"
%}

## Step 6: Push Code and Wait for Build

{%
  include-markdown "./includes/push-code-wait.md"
%}

{%
  include-markdown "./includes/deployment-verification-pods.md"
%}

{%
    include-markdown "./includes/container-release-api.md"
%}

{%
  include-markdown "./includes/contact_us.md"
%}

## Sonarqube Integration

{%
  include-markdown "./includes/sonarqube.md"
%}

## Optional - Set Up Slack Notifications

{%
  include-markdown "./includes/slack-notifications.md"
%}

## Optional - SonarQube Access and Boarding

See the [Sonarqube](./sonarqube.md) page for detailed information.

## Notes

- For installation, make sure you have either a `requirements.txt`, `Pipfile.lock`, or `poetry.lock`. Other dependency managers are not supported at this time, but that could be a potential future improvement.
- The `pytest` is used to run unit-tests.
- Currently `flake8` is used to run linting, but the plan is to move to `Black` soon.
- The `pyproject.toml` file is used to manage metadata about the project including version.
- The Dockerfiles we use are [stored here](https://github.ibm.com/cio-ci-cd/pipeline-catalog/tree/main/catalog/official/tasks/buildah/dockerfiles/python).
- Need to configure secrets or environment variables for your application? See the [Environment Variables](cirrus-deployment-customization.md#environment-variables) and [Secrets](cirrus-deployment-customization.md#secrets) documentation to learn more.
