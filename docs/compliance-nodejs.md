{%
       include-markdown "./includes/nodejs-deprecation-warning.md"
%}

See the [Pipeline Upgrading page](pipeline-upgrading.md) for more information on upgrading your pipelines.

# Compliance - Node.js

By the end of this guide, you should be able to run compliance checks for your Node.js application. Your repository should be set up such that every commit may trigger a build and deploy. {% include-markdown "./includes/contact_us.md" %}

## Install the GitHub Application

{%
   include-markdown "./includes/github-app-pipeline.md"
%}

{%
   include-markdown "./includes/github-app-installation.md"
%}

## Make Code Changes

   <!-- prettier-ignore -->
 

!!! note
    The **Yarn** pipelines are community-supported. Support on language or plaform-specific tools is the responsibility of the community authors, and may not be available from the CIO CI/CD team. 
    
    See the dedicated Slack channel for Yarn pipeline support:  [#cio-cicd-yarn-pipeline](https://my.slack.com/archives/C04PLQK8STW) 

1.  Ensure you have a `package.json` file at the root directory of the project.
1.  Ensure you commit the `package-lock.json` file. We will only install versions of packages noted in the lock file for security purposes.
1.  Create a `build.yml` file in project root with the following structure. Some of the values will be filled out later.
      <!-- prettier-ignore -->

    ```yaml
    apiVersion: automation.cio/v1alpha1
    kind: RepositoryConfig
    ealImapNumber: xxxxxx
    build:
      strategy: simple-pipeline
      pipeline: nodejs-v18-npm-compliance
      version: v1
    ```
  {%
    include-markdown "./includes/optional_jira.md"
  %}

1.  Choose a pipeline and fill into the pipeline field
    {%include-markdown "./includes/nodejs-npm-compliance.md"%}

1.  {%
       include-markdown "./includes/detect_secrets.md"
    %}

{%
       include-markdown "./includes/node_scope.md"
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
