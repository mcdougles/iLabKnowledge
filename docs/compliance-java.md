# Compliance - Java

By the end of this guide, you should be able to run compliance checks for your Java application. Your repository should be set up such that every commit may trigger a build and deploy. {% include-markdown "./includes/contact_us.md" %}

## Install the GitHub Application

{%
   include-markdown "./includes/github-app-pipeline.md"
%}

{%
   include-markdown "./includes/github-app-installation.md"
%}

## Make Code Changes

1. Make sure your project includes a [`pom.xml` file](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html). The compliance pipeline runs `mvn compile` to build your Java source code so that [SonarQube](sonarqube) can scan it, so this file must be present for Maven to compile your code.

1. Create a `build.yml` file in project root with the following structure. Some of the values will be filled out later.
   <!-- prettier-ignore -->
      ```yaml
      apiVersion: automation.cio/v1alpha1
      kind: RepositoryConfig
      ealImapNumber: xxxxxx
      build:
         strategy: simple-pipeline
         pipeline: java-v17-mvn-compliance
         version: v2
      ```

   {%
      include-markdown "./includes/optional_jira.md"
   %}

1. Choose a pipeline and fill into the pipeline field:
   <!-- prettier-ignore -->
      - java-v17-mvn-compliance
      - java-v11-mvn-compliance
      - java-v8-mvn-compliance

1. {%
      include-markdown "./includes/detect_secrets.md"
   %}

## Push Code and Wait for Build

{%
  include-markdown "./includes/push-code-wait.md"
%}

### Private Dependencies

{%
  include-markdown "./includes/maven-private-dependencies.md"
%}

### Local Maven Dependencies

{%
  include-markdown "./includes/local-maven-private-dependencies.md"
%}

{%
  include-markdown "./includes/contact_us.md"
%}

## Notes

Please refer to the [Security Testing Page](./security-testing.md) for more on TwistLock, Sonarqube, and Mend.