{%
       include-markdown "./includes/community-supported.md"
%}

!!! info

    Dedicated Slack channel for Gradle pipeline support: [#cio-cicd-gradle-pipeline](https://my.slack.com/archives/C04G0BY8212)

Guide: Building and Deploying a Gradle application to Cirrus

By the end of this guide, you should be able to deploy your Gradle application to Cirrus. Your repository will be set up such that every commit may trigger a build and deploy. {% include-markdown "./includes/contact_us.md" %}

## Step 1: Complete Checklist

{%
       include-markdown "./includes/cirrus-complete-checklist.md"
%}

## Step 2: Complete the gradle pipeline requirements

1. Update gradle to latest version. Without gradle updated to a version compatible with the newer version of gradle, your app will fail to install, because the pipeline only works with the newer version. Current gradle version used on this pipeline is 7.5.

2. Successfully run a code style analysis. At the pipeline we execute the task lint, so its required to have a task that validates your code style, we recommend to use the [Google's Java Style Checkstyle](https://checkstyle.sourceforge.io/google_style.html). <br/>In order to create a lint task in gradle, you can use the following example [codeQuality.gradle](https://github.ibm.com/Christian-Zuniga1/shared-configs/blob/master/codeQuality.gradle). Once you create the codeQuality.gradle you need to import the file to your build.gradle file, here is an example of how to do that:<img width="559" alt="image" src="https://media.github.ibm.com/user/210997/files/3d8b4500-3b3b-11ed-8185-505526040a2d"><br/>
   In this example we are also creating an extra file for the code style rules, called [google-checks.xml](https://github.ibm.com/Christian-Zuniga1/shared-configs/blob/master/google-checks.xml) you can use this example to use the same rules.<br/>
   At the end, in your gradle folder you should have this files:<br/><img width="300" alt="image" src="https://media.github.ibm.com/user/210997/files/f3f87581-0055-4886-a860-d31f7208a037">

3. Be able to run the integration test. If you don't currently have integrationTest task in your gradle project, what you can do is add the following lines to your build.gradle

```sh
plugins {
    .
    .
    .
    id 'org.unbroken-dome.test-sets' version '4.0.0'
}
.
.
.
testSets {
    integrationTest
}

```

4. {% include-markdown "./includes/detect_secrets.md" %}

1. {%
      include-markdown "./includes/health-check.md"
   %}

## Step 3: Install the GitHub Application

{%
    include-markdown "./includes/github-app-pipeline.md"
%}

{%
    include-markdown "./includes/github-app-installation.md"
%}

## Step 4: Generate a GitHub Access Token

{%
    include-markdown "./includes/github_access_token.md"
%}

## Step 5: Create your Project and Pipeline on Cirrus

{%
    include-markdown "./includes/cirrus-project-pipeline.md"
%}

## Step 6: Generate a Cirrus API key

Open cirrus using the following page [https://cirrus.ibm.com/user](https://cirrus.ibm.com//user) and generate a new API Key

![2023-01-20 13 35 01](https://media.github.ibm.com/user/210997/files/d66a6919-8535-463d-a812-ee3dc619d5fc)

Save the results as `cirrus-api-username` and `cirrus-api-password`, we will use these values on Step 7.

## Step 7: Encrypt your secrets

We need to encrypt the secrets you created in previous steps so that we can safely upload them as plain text to our repository:

<!-- prettier-ignore -->
In the following example, replace the contents of `<>` with your own values. eg.= `/v1/<YOUR_GITHUB_ORG>/<YOUR_REPO>/encrypt_all` becomes `/v1/my-git-org/my-repo/encrypt_all`
??? "Variables Details"
    - **YOUR_GITHUB_ORG**: Your GitHub organization name.
    - **YOUR_REPO**: Your GitHub repository name.
    - **YOUR_GITHUB_TOKEN**: The GitHub token you generated in the step above.
    - **YOUR_CIRRUS_API_USERNAME**: Your Cirrus API username
    - **YOUR_CIRRUS_API_PASSWORD**: Your Cirrus API password

```sh
curl --location --request POST 'https://adopter-service-prod.dal1a.cirrus.ibm.com/v1/<YOUR_GITHUB_ORG>/<YOUR_REPO>/encrypt_all' \
--header 'Content-Type: application/json' \
--data-raw '{
    "githubAccessToken": "<YOUR_GITHUB_TOKEN>",
    "plainTexts": {
        "cirrus-api-username": "<YOUR_CIRRUS_API_USERNAME>",
        "cirrus-api-password": "<YOUR_CIRRUS_API_PASSWORD>"
    }
}'
```

The result will give you 4 different encrypted tokens with the following format, you will use the tokens in the next step:

```json
{
  "cirrus-api-username": "<cirrus-api-username-token>",
  "cirrus-api-password": "<cirrus-api-password-token>"
}
```

{%
      include-markdown "./includes/encrypt-each-repo.md"
%}

## Step 8: Make Code Changes

Before following the process outlined on this page, **make sure you have followed the steps for [Cirrus Configuration](cirrus-configuration.md)**.

1. Create a `build.yml` file in project root with the following structure.

```yaml
apiVersion: automation.cio/v1alpha1
kind: RepositoryConfig
ealImapNumber: <your_app_imap>
build:
  strategy: cirrus-deployment
  pipeline: <pipeline>
  version: v1
  config:
    cirrus-pipeline-name: <cirrus-pipeline-name>
    service-port: '8080'
    cirrus-project-name: <cirrus-project-name>
    cirrus-api-username: '<cirrus-api-username-token>'
    cirrus-api-password: '<cirrus-api-password-token>'
    cirrus-region: <us-south2 | us-east1>
```

In this file you will replace the variables with the values you got on steps 1 to 7, here is a description of each of the values and below the table you can see an example:

| Variable                 | Description                                                                                                                                                                                                                                               |
| ------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| your_app_imap            | This is the identifier for your application, use the following values depending on your application                                                                                                                                                       |
| versions      | Currently exist 2 different versions of the Java Gradle pipelines, the difference is that v2 already includes Container Release, you can also review this PR for more details https://github.ibm.com/cio-ci-cd/pipeline-catalog/pull/1541|
| pipeline                 | Depending on the application that you are migrating you should use a different pipeline to be executed: <ul><li>For java 8 apps use: <b>java-v8-gradle-container-image</b></li><li>For liberty java 8 apps use: <b>java-v8-gradle-was-liberty</b></li><li>For java 11 apps use: <b>java-v11-gradle-container-image</b></li><li>For java 17 apps use: <b>java-v17-gradle-container-image</b></li><li>For quarkus java 17 apps use: <b>java-v17-quarkus-container-image</b></li></ul> |
| cirrus-pipeline-name     | This is the name of the pipeline you created, where your images will be pushed                                                                                                                                                                            |
| cirrus-project-name      | Is the name of your project on cirrus |
| cirrus-api-username      | This is the user name of your api key encrypted on step 6                                                                                                                                                                                                 |
| cirrus-api-password      | This is the password of your api key encrypted on step 6                                                                                                                                                                                                  |

You can compare the result with the following example of a java-v17-gradle-container-image pipeline:

```yaml
apiVersion: automation.cio/v1alpha1
kind: RepositoryConfig
ealImapNumber: BIL-000028
build:
  strategy: cirrus-deployment
  pipeline: java-v17-gradle-container-image
  version: v1
  config:
    cirrus-pipeline-name: dr-pr-db-ci-cd
    service-port: '8080'
    cirrus-project-name: esw-shared-tools
    cirrus-api-username: 'encrypted:v1:AAABg2geZtbCaMHvSRd05NYULrEFql2fsit2QWc7NE6jkDCexA/1KyBSq66EiEvZBQqY/LukqdZ1dr3PprDDi9rSK2pmZJO8Inc0jsSoEgk4E3BMw2CrA8JTrFrhdnH0HfAN9HYB0eal+cnKpYXgggCsiwPeuzkc0rVhgZFPsY0LfQq90nilzpHOqMkY2nakL9l4RSYd3d/5'
    cirrus-api-password: 'encrypted:v1:AAABg2geZtYCdgsRVPDgICnyq9dq/1031xDYnDnPNJBfEuKY9YObzQ51YDH0fX6bKlS+FNUlp75p3A1HECFCBUkiU6z2ytLbJ3bdq/ObIsAQhFaDx2kIpKuiLAvQClgy7eV8eTY2hviqaIP2DELTrKTuhxPFp1SPtpAGPmdmVdP6AOLDSM7Nc688prc7j2hVTF9bqVDj7Aa6hSuvz3ExlFO993vCZoEBntrgeWAu6Q=='
    cirrus-region: us-south2
```

{%
   include-markdown "./includes/optional_jira.md"
%}

## Step 9: Add environment variables

You can add environment variables by configuring a custom deployment following the [cirrus deployment customization guide](cirrus-deployment-customization.md).

## Step 10: Deployment Testing

{%
  include-markdown "./includes/deployment-testing.md"
%}

## Step 11: Push Code and Wait for Build

{%
  include-markdown "./includes/push-code-wait.md"
%}

1. Depending on the branch you execute the pipeline you will have different results, while default branch will deploy automatically to preprod environment and production environment, feature branches will only deploy to test environment, so you will have different steps executed, expand the following table to understand what steps you should expect to be executed.

<details>
    <summary>Details of the expected steps</summary>
    <div>
        <div class="md-typeset__table"><table>
        <thead>
        <tr>
        <th>Step Numer</th>
        <th>Step Name</th>
        <th>Executed in main branch?</th>
        <th>Description</th>
        </tr>
        </thead>
        <tbody>
        <tr>
        <td>1</td>
        <td>clone</td>
        <td>Yes</td>
        <td>The git-clone Task will clone a repo from the provided url into the
    output Workspace. By default the repo will be cloned into the root of
    your Workspace.</td>
        </tr>
        <tr>
        <td>1a</td>
        <td>detect-secrets</td>
        <td>Yes</td>
        <td>    This task updates a detect-secrets baseline file and runs a report against it. See https://w3.ibm.com/w3publisher/detect-secrets/developer-tool to learn more about detect-secrets. You can read more about report generation here: https://w3.ibm.com/w3publisher/detect-secrets/ci-cd.</td>
        </tr>
        <tr>
        <td>2</td>
        <td>compile</td>
        <td>Yes</td>
        <td> This task runs the gradle compile command. If a username and password are provided
    as secrets, this task will set their value to the environment variables
    GRADLE_REPO_USERNAME and GRADLE_REPO_PASSWORD.
        </td>
        </tr>
        <tr>
        <td>3</td>
        <td>mend-scan</td>
        <td>Yes</td>
        <td> The WhiteSource scan task executes a scan using Mend (formerly WhiteSource).</td>
        </tr>
        <tr>
        <td>4</td>
        <td>unit-test</td>
        <td>Yes</td>
        <td>    This task runs the gradle test command that is used to run the unit test
    defined on the project.</td>
        </tr>
        <tr>
        <td>5</td>
        <td>lint</td>
        <td>Yes</td>
        <td>This task runs the gradle lint command that verifies if the code matches
    the configured style rules</td>
        </tr>
        <tr>
        <td>6</td>
        <td>git-version</td>
        <td>Yes</td>
        <td>    This task will tag the current commit with a new version.
    First, the task will check github for any existing tags
    reachable by this commit. It will also get version information
    from the configuration file to determine what the next version
    should be. Once it has the next version, it will create a git
    tag and return the version as the result
</td>
        </tr>
        <tr>
        <td>7</td>
        <td>gradle-version</td>
        <td>Yes</td>
        <td>This task runs the gradle version command that is used to get the
    version of the project.</td>
        </tr>
        <tr>
        <td>8</td>
        <td>sonarqube-scan</td>
        <td>Yes</td>
        <td>    This task focuses on running sonarqube enterprise scan for continous inspection of code quality to detect bugs, code smells and security vulnerabilites.
        </td>
        </tr>
        <tr>
        <td>9</td>
        <td>package</td>
        <td>Yes</td>
        <td>This task runs the gradle bootJar command to create the .jar file that
    is used to execute the application.</td>
        </tr>
        <tr>
        <td>10</td>
        <td>build-app-image</td>
        <td>Yes</td>
        <td>    Buildah task builds source into a container image and then pushes it to a container registry.
        </td>
        </tr>
        <tr>
        <td>10a</td>
        <td>twistlock-scan-app-image</td>
        <td>Yes</td>
        <td>The Twistlock scan task executes a scan using twistlock.</td>
        </tr>
        <tr>
        <td>10b</td>
        <td>cosign-sign-app-image</td>
        <td>Yes</td>
        <td>Cosign task signs a container image on a container registry.</td>
        </tr>
        <tr>
        <td>11</td>
        <td>build-verification-image</td>
        <td>Yes</td>
        <td>Buildah task builds the integration test into a container image and then pushes it to a container registry.</td>
        </tr>
        <tr>
        <td>11a</td>
        <td>twistlock-scan-verification-image</td>
        <td>Yes</td>
        <td>The Twistlock scan the verification image executing a scan using twistlock</td>
        </tr>
        <tr>
        <td>11b</td>
        <td>cosign-sign-verification-image</td>
        <td>Yes</td>
        <td>Cosign task signs a container image on a container registry.</td>
        </tr>
        <tr>
        <td>12</td>
        <td>deploy-to-test-environment</td>
        <td>No</td>
        <td>This task focuses on updating the image for an OpenShift application.</td>
        </tr>
        <tr>
        <td>13</td>
        <td>deploy-verification-test-environment</td>
        <td>No</td>
        <td>This task focuses on updating the image for an OpenShift application.</td>
        </tr>
        <tr>
        <td>12</td>
        <td>deploy-to-preprod-environment</td>
        <td>Yes</td>
        <td>This task focuses on updating the image for an OpenShift application.</td>
        </tr>
        <tr>
        <td>13</td>
        <td>deploy-verification-preprod-environment</td>
        <td>Yes</td>
        <td>This task focuses on updating the image for an OpenShift application.</td>
        </tr>
        <tr>
        <td>14</td>
        <td>deploy-to-production-environment</td>
        <td>Yes</td>
        <td>This task focuses on updating the image for an OpenShift application.</td>
        </tr>
        <tr>
        <td>15</td>
        <td>deploy-verification-production-environment</td>
        <td>Yes</td>
        <td>This task focuses on updating the image for an OpenShift application.</td>
        </tr>
        </tbody>
        </table></div>
    </div>
</details>

{%
    include-markdown "./includes/deployment-verification-pods.md"
%}

{%
    include-markdown "./includes/contact_us.md"
%}

### Private Dependencies

You can use private dependencies on your proyect, for that encrypt your `gradle-repo-password` and `gradle-repo-username`, and then add the values to the `build.yml`.
Like in the following example:

```yaml
apiVersion: automation.cio/v1alpha1
kind: RepositoryConfig
ealImapNumber: BIL-000028
build:
  strategy: cirrus-deployment
  pipeline: java-v8-gradle-container-image
  version: v1
  config:
    cirrus-pipeline-name: ramcontroller
    service-port: '8080'
    cirrus-project-name: shopz-prod
    cirrus-api-username: 'encrypted:v1:AAABg4qQxowQTIPTad3ebvjkHow+t8h7hn36tpZWAghoyTzh68fVqUihTbCndzdXDgtsacxg4R/C7AdEDWb8ON1nYXFMAUnSZY0UKM+w+h+4ic1+f26gOmR0q7oYNx4Qc404z42sFPqRv/CwcFbEMViBuvjmvlR+bhbuC8KJbtM9lMmkHIV0xbdeuhbu5bwqIUljyA=='
    cirrus-api-password: 'encrypted:v1:AAABg4qQxoxeBk7ZWxpD2KGX2o6pXnupN58b7VCbAc5KYdaVSHLSFw3qKQby+kfzjgcMAeEKfIHP7FUJhoKrWDwJCtwj8T5b0CTI3zrxshoOjuzNAvocU5zxyrYLInnzB0iHBOhi8S4IR2eeG6pBQF/Rm8DO7E1FSu1+FKxC9KituSU2WTruRcy+Xdf9xmdqaYFcIvc9r0VDF5eCFJ2dRDPJIOjaUtGy/lCZcw5Fnw=='
    gradle-repo-password: 'encrypted:v1:AAABg4qQxowzibCSt1Sa1nfrj4dmtxI1WejiTmj4mJodLzzTGmFrJbIC1+lMCWYv8rlksAjRLHAT42pW2Csz+nAFLDhALQG0GWRq/PQTmJHwvo1hB2IkVOTifY+e3ZrCMBoRDX0QSSgAYttH5d/0kzxesfi1wUHQ5ay4XpPh8hRkk6BJkCeLR2KnjPlj3fWIa7jtB6Ytguu7m41rvrCCrwIsj3oEzM2jLmdgWIUBj1H1'
    gradle-repo-username: 'encrypted:v1:AAABg4qQxoxj6+WqeckkUlyCZsi+n5Ydul2NgTCx2fPMjdhJbgxDgoNAnZiNpKGHKITnqc6TKcxmCmj3fdQzHqSS7FKa4whJgGQaaBhbFLfhgQ3uVYlGcBUqGgUZQQ/2iJUQpwN/LDXwQNA19w/F3s6bhxy83wr/iBOXk5jo'
    cirrus-region: us-south2
    deploy-verification-memory-request: q1gb
```

In addition to this, in your gradle repository it is necessary to reference these environment variables with the name of `GRADLE_REPO_USERNAME` and `GRADLE_REPO_PASSWORD` as in the following example:

<img width="598" alt="image" src="https://media.github.ibm.com/user/210997/files/c6ab1e84-bece-43d9-981c-52c17a909594">

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

- The Gradle pipeline is a community-supported pipeline, you can join the community at [#cio-cicd-gradle-pipeline](https://ibm.enterprise.slack.com/archives/C04G0BY8212).
- On **v1** Java code is compiled with `gradle compileJava`.
- On **v2** Java code is compiled with `gradle assemble`.
- Unit tests are run with `gradle test`.
- On **v1** Linting is performed by [checkstyle](https://checkstyle.sourceforge.io). The pipeline runs `gradle checkstyleMain` followed by `gradle checkstyleTest`.
- On **v2** Linting is not longer executed, instead is executed `gradle check`, linting is only executed if it configured on check task.
- On **v2** gradle version is removed.
- The Dockerfiles used by this pipeline are available [here](https://github.ibm.com/cio-ci-cd/pipeline-catalog/tree/main/catalog/official/tasks/buildah/dockerfiles/gradle).
