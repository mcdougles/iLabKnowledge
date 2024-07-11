In order to use private dependencies (aka modules) from `github.ibm.com`, CI/CD requires authentication. You can provide that authentication by encrypting a [GitHub Personal Access Token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens) that has the `repo` scope and setting `private-dependency-token` in the `build.yml` config equal to the encrypted value.

```yaml
apiVersion: automation.cio/v1alpha1
kind: RepositoryConfig
ealImapNumber: xxxxxx
build:
    strategy: cirrus-deployment
    pipeline: xxxxxx
    version: v1
    config:
        private-dependency-token: encrypted:v1:...
```

??? "How to Encrypt Values"
    {%
    include-markdown "./encryption.md"
    %}

    {%
    include-markdown "./encrypt-each-repo.md"
    %}
