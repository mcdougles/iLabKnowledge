# Guide: Building and Deploying a Java application to Cirrus

By the end of this guide, you should be able to deploy your Java application to Cirrus. Your repository will be set up such that every commit may trigger a build and deploy. {% include-markdown "./includes/contact_us.md" %}


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
    The **Maven JAR** pipelines are community-supported. Support on language or platform-specific tools is the responsibility of the community authors, and may not be available from the CIO CI/CD team.

    See the dedicated Slack channel for Maven JAR pipeline support:[#cio-cicd-mvn-jar-pipeline](https://my.slack.com/archives/C04Q81E7SF2)

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

    - `java-v17-mvn-ol-container-image` - Java 17 microservice built on Maven and served with Open Liberty
    - `java-v11-mvn-ol-container-image` - Java 11 microservice built on Maven and served with Open Liberty
    - `java-v8-mvn-ol-container-image` - Java 8 microservice built on Maven and served with Open Liberty
    - `java-v11-mvn-jdk-container-image` - Java 11 application built on Maven to run a JAR artifact with the JRE
    - `java-v8-mvn-jdk-container-image` - Java 8 application built on Maven to run a JAR artifact with the JRE
    - `java-v17-mvn-springboot-layertools-container-image` - Java 17 microservice built on Maven and served with Spring Boot layertools

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

    See the following example of a java-v17-mvn-ol-container-image pipeline:

    ```yaml
    apiVersion: automation.cio/v1alpha1
    kind: RepositoryConfig
    ealImapNumber: xxxxxx
    build:
      strategy: cirrus-deployment
      pipeline: java-v17-mvn-ol-container-image
      version: v1
      config:
        service-port: '9080'
        cirrus-project-name: rre-cirrus
        cirrus-pipeline-name: java11-test-pipeline
        cirrus-region: us-south2
        cirrus-api-username: encrypted:v1:AAABJiD8bVgAWvpdhN7QFh0XXdPX3z7wVW+RMqag4IJFvI/CAO3RH9vxQdw/HQ6vPmruAPIhjOwAg75MuYti7fosB7eIp7m4cWu4Shtp3xVHbSoqZhUA+Wwnvo3NP3NO7yzU62LZjK23fJIM4S8Cf4HNgJ1zdJxsE//WjLzPEMyYI9yGGuHr3CGefce9txV7rKRyPFGk
        cirrus-api-password: encrypted:v1:AAABJiD8bVja4tUpy6sfRxGeifRJ1DB82sUS0Q1mtYrfpUemHokP/OcHJ7Dfpaa/SHczaua7KOJRcrdltexzDkUQqtuA6XCLJJV0MlArV9shJBGQmPg8oNosjI4J7rrNZsD5pUS1aUgZKAAmXEhBTKHrnQxFZU+9Ll0T9t13Llcoh9XVhP25Ltc9vabDVP0Rx++ELgXNDJUer9SHpGC9qkXMvU2HBDr+AqI0GZMaTw==
    ```
    !!! note
        Notice the `service-port` param **must be included** when using the **container-release** strategy.

    The following is an example of the java-v17-mvn-ol-container-image pipeline with private dependencies and deploying application to apps folder in OpenLiberty Websphere:

    ```yaml
    apiVersion: automation.cio/v1alpha1
    kind: RepositoryConfig
    ealImapNumber: xxxxxx
    build:
      strategy: cirrus-deployment
      pipeline: java-v17-mvn-ol-container-image
      version: v1
      config:
        deploy-to-apps: 'true'
        service-port: '9080'
        mvn-config: settings.xml
        mvn-repo-username: encrypted:v1:AAABJiD8bVhw5mK2JK3cFiAEd7AKBY3JoeeFWNkM4+P89e+9wMov5fbfqa+oAeAMZnMkiNHfyFC4XWSI6AS+D76drwzlcRQNqqp6+vTZqXxvXtlIwORU3Vij4PXZM2702/1Q/vV1Dh1AoX2zOf6vVlUnLa5FotnZCY2GgROp
        mvn-repo-password: encrypted:v1:AAABJiD8bVimEekvXAa4reZLR61Oiht/LSnatcJlXpc1SFp/IGoRoQP1LBjqB+iJU/kVWIAfJ6EdZoF3PtTYplvV6yK+MchaDa5uoSsKTR2TJ8I5AZGlaoISb9ozE3rX1bGiAj1/3af47l8nSSroUtdOewSzlSqZEKHBouBEi8AK3hdfhxBZ8IsPaENiwH+SqvxfCFGbQi7YSBPu1r6Dgu3PkgnLwezYtdWb4/4hdb4a
        cirrus-project-name: rre-cirrus
        cirrus-pipeline-name: java11-test-pipeline
        cirrus-region: us-south2
        cirrus-api-username: encrypted:v1:AAABJiD8bVgAWvpdhN7QFh0XXdPX3z7wVW+RMqag4IJFvI/CAO3RH9vxQdw/HQ6vPmruAPIhjOwAg75MuYti7fosB7eIp7m4cWu4Shtp3xVHbSoqZhUA+Wwnvo3NP3NO7yzU62LZjK23fJIM4S8Cf4HNgJ1zdJxsE//WjLzPEMyYI9yGGuHr3CGefce9txV7rKRyPFGk
        cirrus-api-password: encrypted:v1:AAABJiD8bVja4tUpy6sfRxGeifRJ1DB82sUS0Q1mtYrfpUemHokP/OcHJ7Dfpaa/SHczaua7KOJRcrdltexzDkUQqtuA6XCLJJV0MlArV9shJBGQmPg8oNosjI4J7rrNZsD5pUS1aUgZKAAmXEhBTKHrnQxFZU+9Ll0T9t13Llcoh9XVhP25Ltc9vabDVP0Rx++ELgXNDJUer9SHpGC9qkXMvU2HBDr+AqI0GZMaTw==
        deploy-verification-memory-request: q1gb
    ```

    If your code uses private dependencies for your application, then you can add your customized settings.xml file and add other configurations mentioned in the build example above. You can add private dependencies credentials by using `mvn-repo-username` and `mvn-repo-password` and add `mvn-config: settings.xml` to your build.yml.

    By default, your war/ear applications will be packaged and placed in the dropins folder. In case you have specific settings which require your application to be deployed in the apps folder, then you can add one parameter in the `config` section of the `build.yml` file: `deploy-to-apps : "true" `.
  {%
    include-markdown "./includes/optional_jira.md"
  %}

1.  To run Integration Tests, the pipeline executes **mvn verify** command. The following is a sample configuration defined within `pom.xml`that uses Maven's Failsafe plugin:

    ```xml
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-failsafe-plugin</artifactId>
      <version>2.22.2</version>
    <executions>
      <execution>
        <id>integration</id>
        <phase>integration-test</phase>
        <goals>
          <goal>integration-test</goal>
        </goals>
        </execution>
        <execution>
          <id>verify</id>
          <phase>verify</phase>
          <goals>
          <goal>verify</goal>
        </goals>
        </execution>
      </executions>
    ```

1.  {%
       include-markdown "./includes/deployment-testing.md"
    %}

1.  {%
       include-markdown "./includes/health-check.md"
    %}

1.  Follow the steps outlined in [Cirrus Deployment Customization](cirrus-deployment-customization.md).

### Private Dependencies

{%
  include-markdown "./includes/maven-private-dependencies.md"
%}

### Local Maven Dependencies

{%
  include-markdown "./includes/local-maven-private-dependencies.md"
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

### Open Liberty Optional Configurations

{%
  include-markdown "./includes/openliberty-optional-configuration.md"
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

The following are reasons for not using Maven versioning (Snapshots and Release):

- **Immutability**: In an automated CI/CD, every build is a potential release and should be immutable. Maven's concept of snapshot artifact is not immutable by definition.
- **Traceability**: There is no reference to change (git commits) included within snapshot artifact.
- **Reliability**: Managing snapshots through pom.xml is error prone because branching without updating the pom.xml file causes overriding of snapshot artifacts.
- **Overhead**: The Maven release runs 3 build and test cycles, manipulating the POM two times and creating three Git revisions.
- **Atomicity**: The Maven release is not an atomic process. In case of errors during a release step (like artifact upload), we end up with an invalid state. Manual intervention is required to clean up Git tags, and to update the appropriate version within the pom file.
