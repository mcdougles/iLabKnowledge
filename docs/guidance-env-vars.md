# Guidance on Build-time Environment Variables

## Overview

This page contains guidance for teams migrating from CI systems where environment variables can be customized during build-time. To address ITSS requirements, custom build-time environment variables are not supported by CIO CI/CD. The platform provides patterns to support the key use cases they address, so please review the guidance below. By adhering to this guidance and best practices, teams using build-time environment variables can successfully migrate applications to the CIO CI/CD platform.

## Guidance / Patterns

CIO CI/CD is IBM CIO's common platform for continuous integration and deployment. In a nutshell, its intent is to facilitate best practices and take the burden of ITSS requirements off of developers to that they can focus to building customer value.

Environment variables are often used at build-time to provide credentials or other secrets to the build processes. The primary ITSS requirement concerning build-time environment variables is [ITSS Chapter 5 - 2.3 Produce Secure Software](https://pages.github.ibm.com/ciso-psg/main/standards/itss.html#23-produce-secure-software), which requires that each build "scans for secrets and/or credentials accidentally included in code." CIO CI/CD addresses this ITSS requirement in two ways.

1. Pipelines run [IBM Detect Secrets](https://w3.ibm.com/w3publisher/detect-secrets) early to detect secrets before they can be built or deployed into an application.
2. The platform limits the ways secrets can be introduced into the build environment and how the pipeline can use them. This design ensures that such secrets are not disclosed through build logs, container metadata, the deployed application, or other ways highlighted in [Security and Privacy by Design (Technical) 2023-2024](https://yourlearning.ibm.com/activity/PLAN-736CB942B2C6).

By encouraging best practices and focusing on enabling key use cases, teams can be confident that the platform is supporting their requirement to produce secure software. CIO CI/CD has gathered feedback from teams and identified the following patterns around build-time environment variables to address:

1. Supplying authentication credentials needed to install dependencies, e.g. from an Artifactory repository.
2. Supplying credentials used to connect to external databases or other services during unit testing.
3. Providing configuration options dynamically based on the build environment to produce different artifacts.
4. Providing configuration options statically for unit testing or other tasks.

Below, we discuss how these use cases align with best practices and ITSS requirements, and how the pipelines support the underlying needs.

### Supplying credentials needed to install dependencies

CIO CI/CD promotes best practices around code reuse. The recommended practice is to move shared functionality to a library, version/publish that code to a package repository, and install it into applications as a build-time dependency.

CIO CI/CD pipelines that build container images support this practice as a standard feature. It is referenced in the documentation as **Private Dependencies**. The documentation describes how developers can securely provide the authentication credentials, repository URL, and other details needed to install the packages. These values are encrypted into the repository configuration `build.yml`, and securely accessed by the pipeline at build time. The pipeline ensures they are only used to install dependencies and are not available to other build processes. See [our Node.js on Cirrus Guide](../nodejs-and-cirrus/#private-dependencies) for a documentation example.

The platform supports code reuse while providing a secure, compliant mechanism for the pipeline to use installation credentials only when necessary.

### Supplying credentials to connect to external database

Using environment variables to provide database or other service credentials is a common practice, particularly in containerized applications. The CIO Cirrus platform and CI/CD pipeline support this pattern at **run-time** (see [Cirrus - Deployment Customization](../cirrus-deployment-customization/)) and when running the pipeline's integrated verification tests (see [Cirrus Deployment Testing](../cirrus-deployment-testing/)). At build-time, it is not supported.

Some teams have requested this feature at build-time for their unit tests. The best practice supported by the CIO CI/CD pipeline is for unit tests to test the code in isolation, and not depend on external services. External services needed at build-time should be mocked to preserve this isolation. Tests that depend on a live, external service can be run as part of the [post-deployment verification tests](../cirrus-deployment-testing).

### Providing configuration options dynamically

The CIO Cirrus platform and CI/CD pipeline support environment variables that can be set per environment at **run-time**. See [Cirrus - Deployment Customization](../cirrus-deployment-customization/) for details on configuring the `env` or `envFrom` fields for an application deployed to Cirrus.

At build-time, dynamically setting environment variables is not supported. Some frontend framework tooling (such as React) supports configuring applications differently at build-time, based on environment variables. Under this pattern, changing the target environment is designed to produce a different build. Since this would produce a different build of the application based on the target runtime, it violates the best practice of [separating config from code](https://12factor.net/config). The best practice is to build the code the same way and apply environment configurations at run-time. The CIO CI/CD pipelines support this best practice, which is necessary to meet the [ITSS requirement](https://pages.github.ibm.com/ciso-psg/main/standards/itss.html#62-separation-of-operational-environments) of testing applications in a non-production environment before applying changes to production. The pipelines that deploy container images enforce this by deploying to production, only after the image has been deployed and tested in the pre-production environment.

While platforms like Java, Node.js, and Python can easily access environment variables at run-time, static sites deployed into Nginx are generally not able to interpret environment variables at run-time. Nginx configuration files can be customized at runtime using this [feature](../static-site-deployments/#nginx-customization) using `envsubst`. For other use cases, teams may [make an InnerSource Contribution](https://pages.github.ibm.com/cio-ci-cd/pipeline-catalog-docs/#getting-started).

### Providing configuration options statically

While custom environment variables cannot be defined, platform tools that read environment variables can also be controlled by other mechanisms. Some examples below:

- `npm` will [read configuration from an `.npmrc` file in the project](https://docs.npmjs.com/cli/v10/configuring-npm/npmrc).
- `package.json` [scripts run by `npm`](https://docs.npmjs.com/cli/v10/using-npm/scripts) can set environment variables.
- `pytest` (Python) will [read configuration from `pytest.ini` or `pyproject.toml`](https://docs.pytest.org/en/stable/reference/customize.html).
-  Maven (Java) provides [documentation on several configuration options](https://maven.apache.org/configure.html).

Please remember that these configurations must be committed in version control with the rest of the source code, so be careful not to include any credentials or sensitive data.
