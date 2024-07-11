Create a `build.yml` file in project root with the following structure. Some of the values will be filled out later.

```yaml
apiVersion: automation.cio/v1alpha1
kind: RepositoryConfig
ealImapNumber: xxxxxx
build:
  strategy: cirrus-deployment
  pipeline: xxx
  version: v2
  config:
    cirrus-pipeline-name: xxx
    cirrus-project-name: xxx
    cirrus-api-username: xxx
    cirrus-api-password: xxx
    cirrus-region: <us-south2 | us-east1 | ash-cpc>*
```
