This page explains the requirements for using the CI/CD pipelines. Make sure that you have fulfilled all of the requirements on this page before you are ready to get started. Please review that these requirements are met before you reach out to the CI/CD team for troubleshooting support.

!!! note
    **z/OS users**: please proceed directly to the [z/OS section](zos/overview.md) of the documentation.

## Requirements

- You must be able to install the CIO CI/CD GitHub app in your GitHub organization.
- A ***build.yml*** file is required including a valid EAL IMAP Number. See the [section below for the minimum required build.yml](#minimum-buildyml-required).
- Code must be working - Applications must build and run locally.
- Make sure you have access to the Regions Clusters specificied for the build.yml. The CD steps may fail if you haven't set the right permissions/access.
- CI/CD enforces good practices regarding Healthchecks which should be implemented and working locally.
- CI/CD supports [Trunk-based deployment](https://cloud.google.com/architecture/devops/devops-tech-trunk-based-development) only as best practice to follow. Feature branches in this deployment model are intended to be short lived. Commits to feature branches deploy to a test environment and commits to the default branch automatically deploy first to pre-production, then to production. When a feature branch is merged to trunk, the running Cirrus application it created is automatically removed.
- A single GitHub repository should represent a single Cirrus application. Currently, monorepo setup is not supported. Also it is not supported to have applications where the backend and frontend both exist in a single GitHub repository.
- For the z/OS pipelines, see the [z/OS Pipeline Template](https://github.ibm.com/cio-ci-cd/zos-application-template) page for general requirements information.

## Recommendations

- The CI/CD pipelines are designed to build and deploy applications that use standard platform tools like npm and mvn. Pipelines do not support custom Dockerfiles.
- Building application images from custom Dockerfiles are not supported. Pipelines use standard platform tools like npm and mvn to install dependencies, build, and start applications.

## Node.js

To use our platform, your node.js application must run with `npm start`. Other specific pipeline technical requirements are documented on the Node.js pipeline section.

## Python

To use our platform, your python application must contain either an `app.py` python script or `app.sh` shell script that runs the application. Other specific pipeline technical requirements are documented on the Python pipeline section.

## Java

To use our platform, your Java applications must use Maven and be able to run the following commands with success:

```bash
# unit tests
mvn test

# e2e tests
mvn verify
```

## Minimum **build.yml** required

{% include-markdown "./includes/minimum_build_yaml.md" %}
