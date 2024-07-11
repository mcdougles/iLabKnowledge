{%
       include-markdown "./includes/nodejs-deprecation-warning.md"
%}

See the [Pipeline Upgrading page](pipeline-upgrading.md) for more information on upgrading your pipelines.

## Guide: Building and Publishing a Node.js Library

The library pipelines build your code from Github into an NPM package. It is then published to an NPM registry so that other applications can use it as a dependency.

Note that on this pipeline, _**no code is deployed to Cirrus**_ and instead the packaged output is published to a repository like [TaaS Artfactory](https://taas.cloud.ibm.com/tools/artifactory).

## Step 1: Install the GitHub Application

{%
    include-markdown "./includes/github-app-pipeline.md"
%}

{%
    include-markdown "./includes/github-app-installation.md"
%}

## Step 2: Generate a GitHub Access Token

{%
    include-markdown "./includes/github_access_token.md"
%}

## Step 3: Make Code Changes

!!! note
    The **Yarn** pipelines are community-supported. Support on language or plaform-specific tools is the responsibility of the community authors, and may not be available from the CIO CI/CD team. 
    
    See the dedicated Slack channel for Yarn pipeline support:  [#cio-cicd-yarn-pipeline](https://my.slack.com/archives/C04PLQK8STW) 

1.  Determine what files to publish and specify by adding a `files` section to your `package.json`.
    See the following example `files` section of the [package.json file](https://docs.npmjs.com/cli/v8/configuring-npm/package-json#files):

    ```json
    "files": [
      "build/**/*"
    ]
    ```

1.  Specify the URL you are publishing to. It is recommended to use [TaaS Artifactory](https://taas.cloud.ibm.com/tools/artifactory).
1.  This pipeline runs the `npm publish` command and in order to publish to the correct location, you should set the `publishConfig` in `package.json` to the `registry` you are publishing to.

    - See the following [link](https://docs.npmjs.com/cli/v7/configuring-npm/package-json#publishconfig) about the `publishConfig`.
    - See the following example of a `publishConfig`:

      ```json
      "publishConfig": {
            "registry": "https://na.artifactory.swg-devops.com/artifactory/api/npm/my-artifactory-org-npm-local"
      }
      ```

1.  {%
        include-markdown "./includes/package_lock.md"
    %}
1.  {%
        include-markdown "./includes/example-build-yml-private-config.md"
    %}
    {%
    include-markdown "./includes/optional_jira.md"
    %}

1.  Choose a pipeline and fill into the **pipeline** field.

    {%
        include-markdown "./includes/nodejs-npm-library.md"
    %}

1.  {%
       include-markdown "./includes/artifactory-private-dependencies.md"
    %}

1.  Add/Rename certain scripts in the package.json.
    The node.js library pipelines executes a `unit-test` and `lint` run command during the build. You should replace the values of these scripts here with the tools you're using for your project.
    <!-- language: json -->

            "scripts": {
              "unit-test": "nyc ava --no-color --tap"
              "lint": "eslint . --ext .js",
            }

    You may write a placeholder, such as `echo 'todo test'` if you do not currently write unit tests and lint.

        {%include-markdown "./includes/nodejs-npm-library.md"%}


1.  {%
       include-markdown "./includes/detect_secrets.md"
    %}

{%
       include-markdown "./includes/node_scope.md"
%}

## Step 4: Push Code and Wait for Build

{%
  include-markdown "./includes/push-code-wait.md"
%}

{%
  include-markdown "./includes/deployment-verification-pods.md"
%}

{%
  include-markdown "./includes/contact_us.md"
%}

## Example

[This repo is publishing using the npm library pipeline](https://github.ibm.com/cio-devex/common).

## Optional - Set Up Slack Notifications

{%
    include-markdown "./includes/slack-notifications.md"
%}

## Troubleshooting

Ensure you can publish locally [using these instructions](https://taas.cloud.ibm.com/guides/npm-configuration-debugging.md).
