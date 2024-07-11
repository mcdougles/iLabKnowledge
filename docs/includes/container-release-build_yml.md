Please Update your `build.yml` to look similar to one below:

```yaml
apiVersion: automation.cio/v1alpha1
kind: RepositoryConfig
ealImapNumber: xxx
build:
  strategy: container-release
  pipeline: <PIPELINE-NAME>
  version: <PIPELINE-VERSION>
  config:
    cirrus-pipeline-name: <CIRRUS-PIPELINE-NAME>
    cirrus-project-name: <PROJECT-NAME>
    cirrus-region: <CIRRUS-REGION>
    cirrus-api-username: xxx
    cirrus-api-password: xxx
release:
  environments:
    - name: test
      deploy-verification-memory-request: "q1gb"
    - name: pre-production
      deploy-verification-memory-request: "q1gb"
    - name: production
      deploy-verification-memory-request: "q1gb"
```

Additionally for us to be able to delete your Cirrus Application and Secrets automatically on delete of a branch, you must have a `build.yml` on your `default` branch (main/master).