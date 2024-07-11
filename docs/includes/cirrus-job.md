A job executes the container image as a task in your Cirrus OpenShift Container Platform cluster.

To learn how to configure the project to be deployed to Cirrus as a Job, expand the section below:

??? "Configuration for Jobs"

    1. To deploy the project as a Job, first update the build config section of the **build.yml** with the `deploy-kind` param:

        ```yaml
        apiVersion: automation.cio/v1alpha1
        kind: RepositoryConfig
        ealImapNumber: xxxxxx
        build:
          strategy: ...
          pipeline: ...
          version: ...
          config:
            ...
            deploy-kind: Job
        ```
    1.  {%
           include-markdown "./jobs/job-yml.md"
        %}