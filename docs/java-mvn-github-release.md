# Guide: Building and Publish a Java Github Release

The GitHub release pipelines build your code from GitHub into a Java JAR or WAR package. The package is zipped and published to GitHub as a release asset.
Pipelines runs compliance steps such as Mend, Sonarqube and Detect Secret.

Pipeline create Release for commits on default branch and Pre-release on non default branch.

Note that on this pipeline, _**no code is deployed to Cirrus**_ and instead the packaged output is published to a GitHub release.

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
    The **Java Maven Github Release** pipelines are community-supported. Support on language or plaform-specific tools is the responsibility of the community authors, and may not be available from the CIO CI/CD team.

1. Make sure your project includes a [`pom.xml`](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html) file. The github release pipeline runs `mvn compile` to build your Java source code so that [SonarQube](sonarqube) can scan it. This file must be present for Maven to compile your code. 

!!! note
    In order to change **compile** as maven goal you can change the maven goal using build.yml config `mvn-goal` e.g. `mvn-goal: install`.


1. Create a `build.yml` file in project root directory with the following structure.
   
      ```yaml
      apiVersion: automation.cio/v1alpha1
      kind: RepositoryConfig
      ealImapNumber: xxxxxx
      build:
         strategy: simple-pipeline
         pipeline: java-v8-mvn-github-release
         version: v1
      ```
    There are few additional configuration for customization

    - `zip-build-files` : Pipeline supports zip files to be uploaded as github assets. In build output directory each file will be zipped by default. Default Value: `"true"`, if needed you can set it to `"false"` then asset to uploaded must be in zip format.

    - `release-branch` : This config will create new Github Release from specified branches only. Value is given in array format e.g. `["main", "staging"]`. By default it creates github release at each commit.

    - `build-output-directory`: This config specifies the location of final build artifacts. Default value: `"target"`

    - `mvn-goal`: This config specifies maven goal to be executed for compile and package steps. Value to specified in string e.g. `"install"`


1. Choose a pipeline and fill into the pipeline field:
   <!-- prettier-ignore -->
      - `java-v17-mvn-github-release`
      - `java-v11-mvn-github-release`
      - `java-v8-mvn-github-release`

1. {%
      include-markdown "./includes/detect_secrets.md"
   %}


## Step 4: Push Code and Wait for Build

{%
  include-markdown "./includes/push-code-wait.md"
%}

{%
  include-markdown "./includes/contact_us.md"
%}

## Optional - Set Up Slack Notifications

{%
    include-markdown "./includes/slack-notifications.md"
%}

### Private Dependencies

{%
  include-markdown "./includes/maven-private-dependencies.md"
%}

{%
  include-markdown "./includes/contact_us.md"
%}

### Local Maven Dependencies

{%
  include-markdown "./includes/local-maven-private-dependencies.md"
%}


## Notes

Please refer to the [Security Testing Page](./security-testing.md) for more on Sonarqube, and Mend.


## Troubleshooting

Ensure your build is successful locally and you have permissions to create release and publish artifacts to github repository.
