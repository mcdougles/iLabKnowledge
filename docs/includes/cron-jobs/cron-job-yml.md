Each repository must specify a cron job schedule through the environment folders inside the `deploy` directory.

```
deploy
├── pre-production
│   └── cron-job.yaml
├── production
│   └── cron-job.yaml
└── test
    └── cron-job.yaml
```

An example of a cron-job.yaml resource specification should look like the following:

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: github-owner-github-repo
spec:
  schedule: "0 */12 * * *" # Every 12 hours
```

!!! note

    This pipeline supports single cron job repositories as well as cron job monorepos.

**Quotas**

Cron jobs have a default memory limit of 256 MB on Cirrus OpenShift, and users may see `OOMKilled` errors if the job exceeds that limit. To override the memory quota, set the `cirrus.ibm.com/quotas` label of the jobTemplate to a valid value as shown in the following snippet:

```yaml
spec:
  jobTemplate:
    metadata:
      labels:
        "cirrus.ibm.com/quotas": "q512mb"
```

To get a list of available quotas, login to your cluster on the terminal and run the following command:

```bash
oc get quotas
```

**Suspending Cron Jobs**

You can also suspend a cron job, as shown in the following snippet:

```yaml
spec:
  suspend: true
```

Upon setting `suspend` to `true`, no future pods are created for the cron job. The job can be un-suspended either by removing the above snippet from its resource definition, or by setting `suspend` to `false`.

**Cron Job Monorepos**

Cron job monorepos are supported by this pipeline. 

!!! note 
    It is important to note that this pipeline only deploys cron jobs; cron jobs and application deployments should not be mixed within the same repository.

Each repository must specify a YAML file for each cron job to be deployed through the environment folders inside the `deploy` directory. The name of the file is not important, however it must end in `.yaml` and contain a single CronJob resource definition.

```
deploy
├── pre-production
│   │   cron-job-1.yaml
│   └── cron-job-2.yaml
├── production
│   │   cron-job-1.yaml
│   └── cron-job-2.yaml
└── test
    │   cron-job-1.yaml
    └── cron-job-2.yaml
```

An example of a monorepo cron job YAML file's resource specification should look like the following:

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: github-owner-github-repo-job-name # If the name becomes too long, use github-owner-job-name
spec:
  schedule: "0 */12 * * *" # Every 12 hours
  jobTemplate:
    spec:
      template:
        spec:
          containers:
            - name: job
              env:
                - name: JOB_NAME
                  value: "job-name" # The name of the cron job
```

!!! note

    Each cron job within a monorepo must have its own unique name, which is referenced in the `metadata.name` property and the `JOB_NAME` environment variable above.

`JOB_NAME` is used to determine which cron job script should run when the container starts up. Make sure to define the `JOB_NAME` environment variable with the cron job's resource specification. Otherwise, the job will not run as expected. Each cron job should have its own unique job name. 

`JOB_NAME` should be injected into your cron job monorepo's start script. For the purpose of this example, a Node.js cron job monorepo is used. [This](https://github.ibm.com/cio-ci-cd-test/nodejs-cron-job-monorepo/blob/main/package.json#L7) npm start script's value is `npm run start:$JOB_NAME`. If `JOB_NAME` is set to `cron-job-1`, then when a new pod is created within Cirrus OpenShift, the `job` container runs the following script: `npm run start:cron-job-1` (defined [here](https://github.ibm.com/cio-ci-cd-test/nodejs-cron-job-monorepo/blob/1.0.1/package.json#L8)). Each cron job should have its own individual start script which follows this naming convention: `start:<insert_job_name>`.

**Secrets and Environment Variables**

If you are required to configure secrets or environment variables for your cron job, see the [Environment Variables](../../cirrus-deployment-customization.md#environment-variables) and [Secrets](../../cirrus-deployment-customization.md#secrets) documentation to learn more.
