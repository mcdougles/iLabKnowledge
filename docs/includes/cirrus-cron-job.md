A cron job builds on a regular job by allowing you to specifically schedule how the job should be run.

To learn how to configure the project to be deployed to Cirrus as a CronJob, expand the section below:

??? "Configuration for CronJobs"
    
    1. To deploy the project as a CronJob, first update the build config section of the **build.yml** with the `deploy-kind` param:

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
            deploy-kind: CronJob
        ```
    1.  {%
           include-markdown "./cron-jobs/cron-job-yml.md"
        %}