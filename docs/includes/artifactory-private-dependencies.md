In order to publish to (or read from) your private registry, CI/CD requires authentication for that registry. You can provide that authentication by encrypting the registry credential and setting the `private-dependencies` config value. The `private-dependencies` config value should look similar to:

```bash
https://na.artifactory.swg-devops.com/artifactory/api/npm/my-artifactory-org-npm-local/;;@my-github-org;;username@us.ibm.com;;artifactory_token
```

The values represent the following:

- **registry**: The URL to set the registry to. This should include the protocol. This should match the `publishConfig`.
- **scope**: The name of the scope being published. This should match the scope used in the `package.json`.
- **username**: The username to use for authentication.
- **token**: The plain-text token used for authentication.

{%
include-markdown "./artifactory-token.md"
%}

Encrypt the above using the following command and add the encrypted response to the `private-dependencies` field of your `build.yml`.

{%
include-markdown "./credentials_encrypt.md"
%}
