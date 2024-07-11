{%
       include-markdown "./includes/community-supported.md"
%}

!!! info

    Dedicated Slack channel for Ruby pipeline support: [#cio-cicd-dotnet-pipeline](https://ibm.enterprise.slack.com/archives/C0633LK7F39)

See the [Pipeline Upgrading page](pipeline-upgrading.md) for more information on upgrading your pipelines.

## Guide: Building and Deploying a DotNET Cronjob to Cirrus

By the end of this guide, you should be able to deploy your DotNET cron job to Cirrus. Your repository should be set up such that every commit may trigger a build and deploy.

## Step 1: Complete Checklist

Make sure the following are true before proceeding:

- When your cronjob is built, the image will be deployed to Cirrus as a Cirrus Job.
- Make sure you have an IBM Bluepay consumer account: <a href="https://w3.ibm.com/w3publisher/cio-hybrid-cloud-services/get-started/create-accounts" target="_blank">register for an IBM Bluepay account</a>, if you have not. Your manager may have already set one up for you.
- If you're deploying a production application, <a href="https://w3.ibm.com/enterprise-application-library" target="_blank">register for an Enterprise Application Library (EAL) account</a>. You will need the created **IMAP number** later (EAL-XXXXXX).

## Step 2: Install the GitHub Application

{%
    include-markdown "./includes/github-app-cronjob.md"
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

1.  {%
       include-markdown "./includes/cirrus_api_key.md"
    %}

## Step 5: Make Code Changes

Before following the process outlined on this page, **make sure you have followed the steps for [Cirrus Configuration](cirrus-configuration.md)**.

!!! note
    The **DotNET** pipelines are community-supported. Support on language or platform-specific tools is the responsibility of the community authors, and may not be available from the CIO CI/CD team.

    See the dedicated Slack channel for DotNET pipeline support:[#cio-cicd-dotnet-pipeline](https://ibm-cio.slack.com/archives/C0633LK7F39)

1.  Choose a pipeline and fill into the **pipeline** field.

    - `dotnet-v60-cronjob-image` - DotNET V6.0 cronjob built with dotnet SDK v6.0
    - `dotnet-v70-cronjob-image` - DotNET V7.0 cronjob built with dotnet SDK v7.0


1.  Create a `build.yml` file in project root with the following structure. Some of the values will be filled out later.

    ```yaml
    apiVersion: automation.cio/v1alpha1
    kind: RepositoryConfig
    ealImapNumber: xxxxxx
    build:
      strategy: cirrus-deployment
      pipeline: <dotnet-v60-cronjob-image | dotnet-v70-cronjob-image>
      version: v1
      config:
        project-path: XXX
        project-name: XXX
        cirrus-project-name: xxx
        cirrus-pipeline-name: xxx
        cirrus-region: <us-south2 | us-east1>
        cirrus-api-username: xxx
        cirrus-api-password: xxx
    ```
    {%
    include-markdown "./includes/optional_jira.md"
    %}

1.  {%
          include-markdown "./includes/apm-configuration.md"
    %}

1.  Fill in **cirrus-project-name** and **cirrus-pipeline-name** with the corresponding name of your in Cirrus, from Step 4 above.

1.  {%
       include-markdown "./includes/cirrus_api_key.md"
    %}

1.  Encrypt the Cirrus API Username and Password.

    {%
      include-markdown "./includes/encryption.md"
    %}

1.  {%
       include-markdown "./includes/detect_secrets.md"
    %}

1.  Review the `build.yml` file.

    See the following example of the build.yml file for a DotNET pipeline:

    ```yaml
    apiVersion: automation.cio/v1alpha1
    kind: RepositoryConfig
    ealImapNumber: xxxxxx
    build:
      strategy: cirrus-deployment
      pipeline: dotnet-v60-cronjob-image
      version: v1
      config:
        project-path: DotNetProjectFolderName
        project-name: DotNetProject
        cirrus-region: us-south2
        cirrus-project-name: name-of-project-on-cirrus
        cirrus-pipeline-name: name-of-pipeline-on-cirrus
        cirrus-api-username: 'encrypted:v1:xxx'
        cirrus-api-password: 'encrypted:v1:xxx'
    ```

1. Expected Dot NET project structure contains solution (.sln) file in root folder and contains separate folders for Main Project and Unit-Tests Project having corresponding C-Sharp Project (.csproj) files.

```
├─── DotNetProject.sln
│
├─── DotNetProjectFolderName
│    ├─── <other source files>
│    └─── DotNetProject.csproj
│
└─── DotNetProject.Tests
     ├─── <other source files>
     └─── DotNetProject.Tests.csproj
```
1.  {%
       include-markdown "./includes/cron-jobs/cron-job-yml.md"
    %}

## Step 6: Push Code and Wait for Build

{%
  include-markdown "./includes/push-code-wait.md"
%}


{%
  include-markdown "./includes/contact_us.md"
%}
