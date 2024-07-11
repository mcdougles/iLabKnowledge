# Compliance - Python

By the end of this guide, you should be able to run compliance checks for your Python application. Your repository should be set up such that every commit may trigger a build and deploy. {% include-markdown "./includes/contact_us.md" %}

## Install the GitHub Application

{%
   include-markdown "./includes/github-app-pipeline.md"
%}

{%
   include-markdown "./includes/github-app-installation.md"
%}

## Make Code Changes

1.  Create a `build.yml` file in project root with the following structure. Some of the values will be filled out later.
      <!-- prettier-ignore -->

    ```yaml
    apiVersion: automation.cio/v1alpha1
    kind: RepositoryConfig
    ealImapNumber: xxxxxx
    build:
      strategy: simple-pipeline
      pipeline: python-v38-compliance
      version: v1
    ```
  {%
    include-markdown "./includes/optional_jira.md"
  %}

1.  Choose a pipeline and fill into the pipeline field:

    - python-v38-compliance
    - python-v39-compliance

1.  {%
       include-markdown "./includes/detect_secrets.md"
    %}

## Push Code and Wait for Build

{%
  include-markdown "./includes/push-code-wait.md"
%}

{%
    include-markdown "./includes/deployment-verification-pods.md"
%}

{%
  include-markdown "./includes/contact_us.md"
%}
