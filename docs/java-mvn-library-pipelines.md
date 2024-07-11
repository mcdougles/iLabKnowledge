# Guide: Building and Publish a Java Library

The library pipelines build your code from Github into an Java Jar package. It is then published to a Maven registry so that other applications can use it as a dependency.

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
    The **Java Maven Library** pipelines are community-supported. Support on language or plaform-specific tools is the responsibility of the community authors, and may not be available from the CIO CI/CD team.

1.  {%
include-markdown "./includes/maven-library-configuration.md"
%}

1.  In the pom.xml project file add the distribution management information, specify the URL you are publishing to. 
It is recommended to use [TaaS Artifactory](https://taas.cloud.ibm.com/tools/artifactory).
```xml
<distributionManagement>
        <repository>
            <id>default</id>
            <url>https://na.artifactory.swg-devops.com/artifactory/txo-cio-dx-team-maven-local/</url>
        </repository>
</distributionManagement>
```
1.  Create a `build.yml` file in project root with the following structure.
```yaml
apiVersion: automation.cio/v1alpha1
kind: RepositoryConfig
ealImapNumber: EAL-011824
build:
  strategy: simple-pipeline
  pipeline: java-v11-mvn-library
  version: v1
  config:
    mvn-config: settings.xml
    mvn-repo-username: encrypted:v1:AAABjdHOdDv+ofIASWx6Fp7WqL9p23d6fzHsb0jr1cJgwZJHhLOAd1fbkIvPVS95Dt4IaBXJBhUP+UZfg+6Ul8J6iPcwFh/ur3Kyszl0q5GnjtEd68/5xBgDrq/qFEZBavzV0TBQig8zsbFP6DkRqlkDpL52s3TogrehZOKEXTuvdPr5buM=
    mvn-repo-password: encrypted:v1:AAABjdHOdDs1Kafy93nTWZuxgYM++ku1UZYm+k0E4mwW/szjTqO7xrDPEMkPnK0Ig0hAANuaBwLDdazX5bwKsyF22JWMbCd3ytWwMaXlJcIWR7VHXqQ4kIfd61tCIkbOc5Rpz1ufdujEdXhmylhz2uDi7eUWKaCLGEOaaTCNJoaGOPuY6BOIIfZfCm924QOxAHfug+KzTm9bajIR09l8wJbPOGeTWZJL
```

1.  Choose a pipeline and fill into the **pipeline** field.

    - `java-v8-mvn-library` - artifact built using Java 8 and published to a private registry (Artifactory)
    - `java-v11-mvn-library` - artifact built using Java 11 and published to a private registry (Artifactory)
    - `java-v17-mvn-library` - artifact built using Java 17 and published to a private registry (Artifactory)
   
    
1.  
{%
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

## Troubleshooting

Ensure you can publish locally [using these instructions](https://taas.cloud.ibm.com/guides/npm-configuration-debugging.md).
