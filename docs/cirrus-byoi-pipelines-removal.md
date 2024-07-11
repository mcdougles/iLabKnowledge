## Pipeline Configuration for Project and Pipeline.

In the Cirrus Portal, you create a CIO CI/CD pipeline for your repository. In the onboarding steps for your pipeline, you will create a build.yml configuration file.  This snippet shows where you put your `CIRRUS_PIPELINE_NAME` and `CIRRUS_PROJECT_NAME`.

```yaml
apiVersion: automation.cio/v1alpha1
kind: RepositoryConfig
ealImapNumber: xxx
build:
  strategy: xxx
  pipeline: xxx
  version: xxx
  config:
    cirrus-pipeline-name: <CIRRUS_PIPELINE_NAME GOES HERE>
    cirrus-project-name: <CIRRUS_PROJECT_NAME GOES HERE>
    cirrus-api-username: xxx
    cirrus-api-password: xxx
    cirrus-region: xxx
```

The setup instructions for each pipeline contain a step dedicated to the build.yml.  Keep the above placement in mind when you apply the values for your `CIRRUS_PIPELINE_NAME` and `CIRRUS_PROJECT_NAME`.

## Navigating the documentation.

Be sure to visit our [w3Publisher page](https://w3.ibm.com/w3publisher/cio-ci-cd) to obtain a high-level understanding of the product and register for our Office Hours, Feature Presentation and Blog.

To onboard, start at our [Overview page](./index.md), then proceed to *Getting started* in the left navigation.

You will encounter the build.yml configuration in the specific configuration steps for your pipeline (e.g. under Node.js, Python, Java, Ruby, etc. in the list).

The Features and Roadmap page is updated on an ongoing basis with the new functionality our team is implementing as well as the status of InnerSource contributions.