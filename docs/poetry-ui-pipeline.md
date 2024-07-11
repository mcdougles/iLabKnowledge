## Guide: Deploying a Static Site generated with Python and Poetry to Cirrus

This pipeline uses [Poetry](https://python-poetry.org) and Python to generate a static HTML/CSS/JavaScript site that is served with Nginx. This pipeline is suitable for building user interfaces or documentation that can be hosted by a simple web server.

By the end of this guide, you should be able to deploy your static site to Cirrus. Your repository should be set up such that every push will trigger a build and deploy. {% include-markdown "./includes/contact_us.md" %}

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

1.  This pipeline supports Poetry projects, so a `pyproject.toml` file is required. You must also commit a **poetry.lock** file in the repo. The pipeline will only install versions of packages noted in the lock file for security reasons.

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

    {%
      include-markdown "./includes/poetry-ui-container.md"
    %}

1.  Fill in **cirrus-pipeline-name** with the name of your Pipeline in Cirrus.

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

    See the following example using the `python-v39-poetry-ui-container-image` pipeline:

    ```yaml
    apiVersion: automation.cio/v1alpha1
    kind: RepositoryConfig
    ealImapNumber: xxxxxx
    build:
      strategy: cirrus-deployment
      pipeline: python-v39-poetry-ui-container-image
      version: v1
      config:
        cirrus-project-name: cio-ci-cd
        cirrus-pipeline-name: poetry-static-site
        distribution-directory: site
        cirrus-region: us-south2
        cirrus-api-username: 'encrypted:v1:AAABJiD8bVgJb64QpnlTH4+9kugiiKi4QHBbpGOzng0GfOxi3Zn5XLspBMNBNZqkzuaKWFHg3v3qXNkzECZ4FSAnpN/km73bB3C/jWh/Wf0TCVjnXgyWh2aFZKgaeXhRJ5ZTAQhQ9a6oS8/4gwpBeocrutkHOVklA0O1SwtvQcqO9iMGWUGzxGG76kZkK0wFhQ=='
        cirrus-api-password: 'encrypted:v1:AAABJiD8bVhCQXY13u8/xa4B4zbyxbKa6Q5E9eorK2hr0WR1cg7Chnup8eawiOzyIcJh/7IXhEwal25WkAINtPF9tFoeQQCvEaHKPRGyHl517M6aeNf86RWBtsQILO5/9Ih1F04zcn+A1d8H3ijucSPU5nIG7x2GP3TDriOPiHBwmLzk/jtDEeVSqgkop9H7iw916iTalzmRahx77s060xvj4SyBIBU4IYWn0N/Qcg=='
        deploy-verification-memory-request: q256mb
    ```

1.  {%
        include-markdown "./includes/poetry-ui-pyproject-toml.md"
    %}

1.  After deployment to Cirrus, the CD pipeline executes end-to-end tests in order to verify whether a deployment is successful. Similar to the runtime script, a couple of options are supported:

    1. Provide a valid `deploy-test.sh` script with the command to execute to test your deployment to Cirrus.
    2. Provide a valid `deploy-test.py` Python file, which tests your deployment to Cirrus .

    Note: If both files are present, the above list ordering will be respected (e.g. deploy-test.sh being executed first). A test file is mandatory. You may write a placeholder, such as `echo 'todo test'` if you do not currently write functional tests.

    Simple `deploy-test.sh` script that verifies the deployed site responds with expected content:

    ```bash
    #!/usr/bin/env bash

    set -o pipefail

    echo "Checking that site is running on the ${APP_ENV} environment"
    curl -f "http://${APP_HOSTNAME}:${APP_PORT}/" | grep "My Static Site Content"
    ```

    {%
       include-markdown "./includes/deployment-verification-pods.md"
    %}

    {%
       include-markdown "./includes/deployment-testing.md"
    %}

1.  {%
       include-markdown "./includes/health-check.md"
    %}

    {%
      include-markdown "./includes/nginx-health-checks.md"
    %}

1.  Nginx Customization (optional):
    {%
      include-markdown "./includes/nginx-optional-customization.md"
    %}

1.  Review the steps outlined in [Cirrus Deployment Customization](cirrus-deployment-customization.md) for any additional customizations.

### Private Dependencies

If your code uses private dependencies, you must provide **encrypted** credentials in your `build.yml` file.


!!! info

    The following information is required for each credential:

    - **url**: The URL of the package repository. This should include the protocol. This **must** match the url listed in `[[tool.poetry.source]]` from **pyproject.toml**.
    - **repository name**: The short name of the repository. This **must** match the name listed in `[[tool.poetry.source]]` from **pyproject.toml**.
    - **username**: The username to use for authentication.
    - **token**: The token used for authentication in Artifactory.


1.  Assemble a credentials string containing these four fields, separated by `;;`.
    <!-- prettier-ignore -->
    1. The required order is URL, repository name, username, and token for accessing the package repository.
    1. If your code uses packages from multiple private repositories, use `--` to separate multiple credentials in the credentials string.
    1. Check that your credentials string is formatted as follows:

            https://na.artifactory.swg-devops.com/artifactory/api/pypi/my-project-pypi-virtual/simple;;my-private-repo;;username@ibm.com;;xxxxxx

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


{%
include-markdown "./includes/artifactory-token.md"
%}

## Step 6: Push Code and Wait for Build

{%
  include-markdown "./includes/push-code-wait.md"
%}

{%
  include-markdown "./includes/deployment-verification-pods.md"
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

- Need to configure secrets or environment variables for your application? See the [Environment Variables](cirrus-deployment-customization.md#environment-variables) and [Secrets](cirrus-deployment-customization.md#secrets) documentation to learn more.
