{%
    include-markdown "./includes/nodejs-deprecation-warning.md"
%}

See the [Pipeline Upgrading page](pipeline-upgrading.md) for more information on upgrading your pipelines.

# Guide: Building and Deploying a Node.js application to Cirrus

By the end of this guide, you should be able to deploy your Node.js application to Cirrus. Your repository should be set up such that every commit may trigger a build and deploy. {% include-markdown "./includes/contact_us.md" %}


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

1.  {%
        include-markdown "./includes/package_lock.md"
    %}

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
        include-markdown "./includes/nodejs-container.md"
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

    See the following example of the `nodejs-v18-npm-container-image` pipeline:

    ```yaml
    apiVersion: automation.cio/v1alpha1
    kind: RepositoryConfig
    ealImapNumber: xxxxxx
    build:
      strategy: cirrus-deployment
      pipeline: nodejs-v18-npm-container-image
      version: v2
      config:
        cirrus-project-name: cio-ci-cd
        cirrus-pipeline-name: nodejs-microservice
        distribution-directory: .
        service-port: '8080'
        cirrus-region: us-south2
        cirrus-api-username: 'encrypted:v1:xxxxxx'
        cirrus-api-password: 'encrypted:v1:xxxxxx'
        deploy-verification-memory-request: q256mb
        private-dependencies: 'encrypted:v1:xxxxxx'
    ```
     See the following example of the `nodejs-v20-npm-container-image` pipeline:

    ```yaml
    apiVersion: automation.cio/v1alpha1
    kind: RepositoryConfig
    ealImapNumber: xxxxxx
    build:
      strategy: cirrus-deployment
      pipeline: nodejs-v20-npm-container-image
      version: v1
      config:
        cirrus-project-name: cio-ci-cd
        cirrus-pipeline-name: nodejs-microservice
        distribution-directory: .
        service-port: '8080'
        cirrus-region: us-south2
        cirrus-api-username: 'encrypted:v1:xxxxxx'
        cirrus-api-password: 'encrypted:v1:xxxxxx'
        deploy-verification-memory-request: q256mb
        private-dependencies: 'encrypted:v1:xxxxxx'
    ```
1.  {%
       include-markdown "./includes/nodejs-packagejson.md"
    %}

1.  {%
       include-markdown "./includes/deployment-testing.md"
    %}

1.  {%
       include-markdown "./includes/health-check.md"
    %}

1.  Follow the steps outlined in [Cirrus Deployment Customization](cirrus-deployment-customization.md).

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

<!-- prettier-ignore -->
If you need to _publish_ a npm library, [see this page for guidance](./npm-library-pipelines.md).

1.  {%
       include-markdown "./includes/artifactory-private-dependencies.md"
    %}

1.  Using a scope in `package.json` for your package is required. The Artifactory repository is required to be used as the package scope. See the following [link](https://docs.npmjs.com/cli/v7/using-npm/scope) for more information.

    ```
    npm install @my-artificatory-org/my-published-package
    ```

{%
    include-markdown "./includes/node_scope.md"
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
%} An [example](https://github.ibm.com/cio-ci-cd-test/nodejs-ts/blob/main/sonar-project.properties) on how to setup sonar-project properties. 

## Optional - Set Up Slack Notifications

{%
    include-markdown "./includes/slack-notifications.md"
%}

## Optional - SonarQube Access and Boarding

See the [SonarQube](./sonarqube.md) page for detailed information.

## Notes

- Need to configure secrets or environment variables for your application? See the [Environment Variables](cirrus-deployment-customization.md#environment-variables) and [Secrets](cirrus-deployment-customization.md#secrets) documentation to learn more.
