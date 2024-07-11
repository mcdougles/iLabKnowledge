{%
    include-markdown "./includes/build-approved-images-context.md"
%}

# Guide: Deploying a Custom Image to Cirrus

By the end of this guide, you should be able to deploy an application based off an approved custom image to Cirrus. Your repository should be set up such that every commit may trigger a deployment. {% include-markdown "./includes/contact_us.md" %}

## Step 1: Complete Checklist

Make sure the following are true before proceeding:

- The custom image has been approved by the CIO CI/CD team.
    - **If the image has not yet been approved, you must follow the [Build Approved Image](./build-approved-images.md) process before proceeding with this guide.**
- The result of the deployment should be a single application in Cirrus. This pipeline does not support deploying multiple applications at once.
- Make sure you have an IBM Bluepay consumer account: <a href="https://w3.ibm.com/w3publisher/cio-hybrid-cloud-services/get-started/create-accounts" target="_blank">register for an IBM Bluepay account</a>, if you have not. Your manager may have already set one up for you.
- <a href="https://w3.ibm.com/enterprise-application-library" target="_blank">Register for an Enterprise Application Library (EAL) account</a>. You will need the created **IMAP number** later (EAL-XXXXXX).


## Step 2: Install the GitHub Application

{%
    include-markdown "./includes/github-app-pipeline.md"
%}

{%
    include-markdown "./includes/github-app-installation.md"
%}

## Step 3: Create your Project in Cirrus

1. Go to [Cirrus](https://cirrus.ibm.com/).

1. Create a project in Cirrus. Give the project a name that makes sense. Choose **Containers** under Flavors.
   **Note**: If you already have a project and pipelines, you can also use your existing project and pipelines with CI/CD.
   ![Cirrus Screen Capture](../images/cirrus-project-pipeline/create-new-project.png)

## Step 4: Make Code Changes

1.  Create a `build.yml` file in project root with the following structure. Some of the values will be filled out later.
    - The `cirrus-project-name` parameter is the target Cirrus project to deploy to. 
    -  The `cirrus-image` parameter is a reference to the approved image which will be deployed as an application to the Cirrus project. The format is `cio-ci-cd-image-catalog/<cirrus-pipeline-name>:<version-tag>`.
        -  The value for `cirrus-pipeline-name` can be found in the build.yml file of the approved image's GitHub repository.
        -  The value for `version-tag` should be a semantic version found in the `git-version` task of the approved image's default branch commit history.
        -  *For a full list of available images, see the [Image Catalog](./decision.md#image-catalog).*
    ```yaml
    apiVersion: automation.cio/v1alpha1
    kind: RepositoryConfig
    ealImapNumber: xxxxxx
    build:
        strategy: cirrus-deployment
        pipeline: deploy-prebuilt-image
        version: v1
        config:
            cirrus-project-name: xxxxxx
            cirrus-image: cio-ci-cd-image-catalog/<cirrus-pipeline-name>:<version-tag>
            cirrus-region: <us-south2 | us-east1 | ash-cpc>
            cirrus-api-username: encrypted:v1:xxx
            cirrus-api-password: encrypted:v1:xxx
    ```

  {%
    include-markdown "./includes/optional_jira.md"
  %}

1.  Encrypt the Cirrus API Username and Password.

    {%
      include-markdown "./includes/encryption.md"
    %}

    {%
      include-markdown "./includes/encrypt-each-repo.md"
    %}

2.  {%
       include-markdown "./includes/detect_secrets.md"
    %}

3.  Follow the steps outlined in [Cirrus Deployment Customization](cirrus-deployment-customization.md).

### Container Release

{%
    include-markdown "./includes/optional-container-build.md.md"
%}

## Step 5: Push Code and Wait for Build

{%
    include-markdown "./includes/push-code-wait.md"
%}

{%
    include-markdown "./includes/container-release-api.md"
%}

{%
    include-markdown "./includes/contact_us.md"
%}

## Example

See the Superset [README](https://github.ibm.com/cio-ci-cd-image-catalog/superset/blob/main/README.md) for example usage.

## Optional - Set Up Slack Notifications

{%
    include-markdown "./includes/slack-notifications.md"
%}
