SonarQube Scan is a task for continuous inspection of code quality to perform static analysis of code to detect bugs and code smells. Sonarqube scan is enabled by default on pipelines  mentioned below with read-only access on Sonarqube UI Dashboard. Please proceed with the below documentation if you plan to assign the permission in Sonarqube UI to allow you to perform any operations such as manage issues, manage the project and manage the portfolio.

**Note:** The SonarQube scan task must first be run in a pipeline on the default branch prior to running on feature branches (the `build.yml` must be configured on the default branch).

## Available Pipelines

### Cirrus Deployments

#### Node.JS

- [Node.js Site Deployments](./nodejs-site-deployments.md)
- [Static Site Deployments](./static-site-deployments.md)

#### Python

- [Python](./python-and-cirrus.md)

#### Java

- [Java](./java-and-cirrus.md)

### Library Pipelines

- [Node.js Library](./npm-library-pipelines.md)

### Compliance Pipelines

- [Node.js compliance](./compliance-nodejs.md)
- [Java compliance](./compliance-java.md)
- [Python compliance](./compliance-python.md)

## SonarQube Version

Currently the version used is SonarQube Enterprise Edition Version 9.9.4 (LTE).

## How to Customize the Configurization for your Project in SonarQube

Create a configuration file in your project's root directory called sonar-project.properties.

See the following example:

```
# --- optional properties ---

# Comma-delimited list of file path patterns to be excluded from analysis.
# The default value for Node.js projects is `node_modules/**`
#sonar.exclusions=

# Path is relative to the sonar-project.properties file. Defaults to .
#sonar.sources=.

# Encoding of the source code. Default is default system encoding
#sonar.sourceEncoding=UTF-8

# Location of compiled java class files. Defaults to target/classes for java projects.
# If your Maven project compiles to a location other than target/classes, provide a
# comma-separated list of directories here.
#sonar.java.binaries=target/classes
```

The following property file values are controlled through automation and are ignored/overridden:

- sonar.login
- sonar.projectVersion
- sonar.projectKey
- sonar.projectName
- sonar.working.directory

Read more on configuring your project at [SonarQube](https://docs.sonarsource.com/sonarqube/9.9/analyzing-source-code/scanners/sonarscanner/).

## Pull Request Analysis

The pipelines perform a SonarQube Pull Request Analysis on a push event when it detects an open, non-draft pull request for that branch. Creating a pull request on GHE does not trigger a new pipeline. Push events remain the only trigger for new pipelines.

See [SonarQube Pull Request Analysis](https://docs.sonarsource.com/sonarqube/9.9/analyzing-source-code/pull-request-analysis/) for more information.

{%
    include-markdown "./includes/github-app-sonarqube-installation.md"
%}

## SonarQube Results

Please visit the [SonarQube Dashboard](https://sonarqube.apps.wdc-sonar-prod.core.cirrus.ibm.com/) in order to access the generated results.