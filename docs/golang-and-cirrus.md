# Guide: Building and Deploying a Golang application to Cirrus

By the end of this guide, you should be able to deploy your Golang application to Cirrus. Your repository should be set up such that every commit may trigger a build and deploy. {% include-markdown "./includes/contact_us.md" %}

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
        include-markdown "./includes/golang-container-image.md"
    %}

2.  Fill in **cirrus-pipeline-name** with the name of your Pipeline in Cirrus.

3.  {%
       include-markdown "./includes/openshift-cli-access.md"
    %}

4.  {%
       include-markdown "./includes/cirrus_api_key.md"
    %}

5.  Encrypt the Cirrus API Username and Password.

    {%
      include-markdown "./includes/encryption.md"
    %}

    {%
       include-markdown "./includes/encrypt-each-repo.md"
    %}

6.  {%
       include-markdown "./includes/detect_secrets.md"
    %}

7.  Review the `build.yml` file.

    See the following example of the `golang-container-image` pipeline:

    ```yaml
    apiVersion: automation.cio/v1alpha1
    kind: RepositoryConfig
    ealImapNumber: xxxxxx
    build:
      strategy: cirrus-deployment
      pipeline: golang-container-image
      version: v1
      config:
        cirrus-project-name: cio-ci-cd
        cirrus-pipeline-name: golang-microservice
        distribution-directory: .
        service-port: '8080'
        cirrus-region: us-south2
        cirrus-api-username: 'encrypted:v1:AAABJiD8bVgJb64QpnlTH4+9kugiiKi4QHBbpGOzng0GfOxi3Zn5XLspBMNBNZqkzuaKWFHg3v3qXNkzECZ4FSAnpN/km73bB3C/jWh/Wf0TCVjnXgyWh2aFZKgaeXhRJ5ZTAQhQ9a6oS8/4gwpBeocrutkHOVklA0O1SwtvQcqO9iMGWUGzxGG76kZkK0wFhQ=='
        cirrus-api-password: 'encrypted:v1:AAABJiD8bVhCQXY13u8/xa4B4zbyxbKa6Q5E9eorK2hr0WR1cg7Chnup8eawiOzyIcJh/7IXhEwal25WkAINtPF9tFoeQQCvEaHKPRGyHl517M6aeNf86RWBtsQILO5/9Ih1F04zcn+A1d8H3ijucSPU5nIG7x2GP3TDriOPiHBwmLzk/jtDEeVSqgkop9H7iw916iTalzmRahx77s060xvj4SyBIBU4IYWn0N/Qcg=='
        deploy-verification-memory-request: q256mb
        private-dependencies: 'encrypted:v1:AAABgJiAewfxl9nd7SvYJtzfl/bdGCmJMi01PExSYuBCKCBTBwlcjXfunR14MdPCE8mbB7URd31S79ZaVQjbJQdW10alo1IUahP5frJh41YsgOqZ1eQ7C2o1eG/JDHjJqv38MuMAfpkOAnSgm4Nh0odCLWRgiW9IzxeMBCFwPf4KdbYfgLpZ6mAg5TgoCH9l0BRvCaSEEQ36XydSGCCthEVbklGdvn5DZY27at1mULNxLackHZ2nGWxfxq/B2oFVm8pR++TfzPMv4uWGVDB3Fdhkla/PRjv6IETU8pkwW/G6njcktU3Wp61K0ig4kEp94fpn4W2snCGxumzUMAKkMF6+LlNWnJFqJpH1lEG6oL2W3boZHC9TeDAy9XViA6/YueuLfigxHNTKU9uyer3mviu0'
    ```

8.  {%
       include-markdown "./includes/golang-files.md"
    %}

9.  {%
       include-markdown "./includes/deployment-testing.md"
    %}

10. {%
       include-markdown "./includes/health-check.md"
    %}

11. Follow the steps outlined in [Cirrus Deployment Customization](cirrus-deployment-customization.md).

### Private Dependencies

{%
    include-markdown "./includes/golang-private-dependencies.md"
%}

```

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
