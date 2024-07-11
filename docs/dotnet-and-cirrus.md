{%
       include-markdown "./includes/community-supported.md"
%}

!!! info

    Dedicated Slack channel for Ruby pipeline support: [#cio-cicd-dotnet-pipeline](https://ibm.enterprise.slack.com/archives/C0633LK7F39)


# Guide: Building and Deploying a DotNET application to Cirrus

By the end of this guide, you should be able to deploy your DotNET application to Cirrus. Your repository will be set up such that every commit may trigger a build and deploy. {% include-markdown "./includes/contact_us.md" %}


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

Before following the process outlined on this page, **make sure you have followed the steps for [Cirrus Configuration](cirrus-configuration.md)**.

!!! note
    The **DotNET** pipelines are community-supported. Support on language or platform-specific tools is the responsibility of the community authors, and may not be available from the CIO CI/CD team.

    See the dedicated Slack channel for DotNET pipeline support:[#cio-cicd-dotnet-pipeline](https://ibm-cio.slack.com/archives/C0633LK7F39)

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

    - `dotnet-v60-container-image` - DotNET V6.0 microservice built with dotnet SDK v6.0 (single file build)
	- `dotnet-v60-nsf-container-image` - DotNET V6.0 microservice built with dotnet SDK v6.0 (non-single file build)
    - `dotnet-v70-container-image` - DotNET V7.0 microservice built with dotnet SDK v7.0 (single file build)
	- `dotnet-v70-nsf-container-image` - DotNET V7.0 microservice built with dotnet SDK v7.0 (non-single file build)


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

1.  Review the build.yml file.

    See the following example of a dotnet-v60-container-image pipeline:

    ```yaml
    apiVersion: automation.cio/v1alpha1
    kind: RepositoryConfig
    ealImapNumber: xxxxxx
    build:
      strategy: cirrus-deployment
      pipeline: dotnet-v60-container-image
      version: v1
      config:
        project-path: DotNetProjectFolderName
        project-name: DotNetProject
        service-port: 8080
        cirrus-project-name: rre-cirrus
        cirrus-pipeline-name: DotNETv60-test-pipeline
        cirrus-region: us-south2
        cirrus-api-username: encrypted:v1:xxx
        cirrus-api-password: encrypted:v1:xxx
    ```

    The following is an example of a dotnet-v70-container-image pipeline that executes post deploy verification / integration tests:

    ```yaml
    apiVersion: automation.cio/v1alpha1
    kind: RepositoryConfig
    ealImapNumber: xxxxxx
    build:
      strategy: cirrus-deployment
      pipeline: dotnet-v70-container-image
      version: v1
      config:
        project-path: DotNetProjectFolderName
        project-name: DotNetProject
        verification-project-path: VerifyProjectFolderName
        verification-project-name: VerifyProject
        verification-job-command: VerifyJobCommand
        service-port: 8080
        cirrus-project-name: rre-cirrus
        cirrus-pipeline-name: DotNETv70-test-pipeline
        cirrus-region: us-south2
        cirrus-api-username: encrypted:v1:xxx
        cirrus-api-password: encrypted:v1:xxx
        deploy-verification-memory-request: q512mb
    ```
	
	In some cases it's needed to deploy the dotnet app as "non single file" (nsf). The default build is single-file. E.g. the IBM Data Server provider for .NET 7 can not handle single file builds.
	For those cases there is a separate pipeline to archive this. Example of a build.yaml:
	
	```yaml
    apiVersion: automation.cio/v1alpha1
    kind: RepositoryConfig
    ealImapNumber: xxxxxx
    build:
      strategy: cirrus-deployment
      pipeline: dotnet-v70-nsf-container-image
      version: v2
      config:
        project-path: DotNetProjectFolderName
        project-name: DotNetProject
        verification-project-path: VerifyProjectFolderName
        verification-project-name: VerifyProject
        verification-job-command: VerifyJobCommand
        service-port: 8080
        cirrus-project-name: rre-cirrus
        cirrus-pipeline-name: DotNETv70-nsf-test-pipeline
        cirrus-region: us-south2
        cirrus-api-username: encrypted:v1:xxx
        cirrus-api-password: encrypted:v1:xxx
        deploy-verification-memory-request: q512mb
		single-file: "false"
    ```
	
	Those non-single-file pipelines are also avalible for .NET 6. (```pipeline: dotnet-v70-nsf-container-image```)
	
1. Expected Dot NET project structure contains solution (.sln) file in root folder and contains separate folders for Main Project, Unit-Tests Project and Verification Tests Project having corresponding C-Sharp Project (.csproj) files.

```
├─── DotNetProject.sln
│
├─── DotNetProjectFolderName
│    ├─── <other source files>
│    └─── DotNetProject.csproj
│
├─── DotNetProject.Tests
│    ├─── <other source files>
│    └─── DotNetProject.Tests.csproj
│
└─── VerifyProjectFolderName      (Optional)
     ├─── <other source files>
     └─── VerifyProject.csproj
```

1.  To run Integration Tests, the pipeline executes command provided in **verification-job-command** parameter if verification project is provided. 

1.  {%
       include-markdown "./includes/deployment-testing.md"
    %}

1.  {%
       include-markdown "./includes/health-check.md"
    %}

1.  Follow the steps outlined in [Cirrus Deployment Customization](cirrus-deployment-customization.md).


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

See the [SonarQube](./sonarqube.md) page for detailed information.

## Notes

Please refer to the [Security Testing Page](./security-testing.md) for more on TwistLock, Sonarqube, and Mend.


