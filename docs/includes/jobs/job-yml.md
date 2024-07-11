Each repository must specify a job schedule through the environment folders inside the `deploy` directory.

```
deploy
├── pre-production
│   └── job.yaml
├── production
│   └── job.yaml
└── test
    └── job.yaml
```

An example of a job.yaml resource specification should look like the following:

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: my-job
  labels:
    cirrus.ibm.com/quotas: q512mb
spec:
  suspend: true
  backoffLimit: 0
  template:
    spec:
      imagePullSecrets:
        - name: mtos-quay
      restartPolicy: Never
```
For more information regarding Cirrus jobs configuration, see the following [document](https://pages.github.ibm.com/CIOCloud/cio-blog/cli/#creating-jobs).

!!! note

    This pipeline only supports single job repositories.

**Suspending Jobs**

You can also suspend a job, as shown in the following snippet:

```yaml
spec:
  suspend: true
```

Upon setting `suspend` to `true`, no future pods are created for the job. The job can be un-suspended either by removing the above snippet from its resource definition, or by setting `suspend` to `false`.

You can find more details [here](https://kubernetes.io/docs/concepts/workloads/controllers/job/#suspending-a-job).

**Secrets and Environment Variables**

If you are required to configure secrets or environment variables for your job, see the [Environment Variables](../../cirrus-deployment-customization.md#environment-variables) and [Secrets](../../cirrus-deployment-customization.md#secrets) documentation to learn more.
