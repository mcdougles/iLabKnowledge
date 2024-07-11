If your project uses private dependencies, first create a maven configuration file in your repository (`settings.xml`) using this template:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<settings xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.1.0 http://maven.apache.org/xsd/settings-1.1.0.xsd" xmlns="http://maven.apache.org/SETTINGS/1.1.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <servers>
    <server>
      <id>default</id>
      <username>${env.MAVEN_REPO_USERNAME}</username>
      <password>${env.MAVEN_REPO_PASSWORD}</password>
    </server>
  </servers>
  <profiles>
    <profile>
      <id>default</id>
    </profile>
  </profiles>
  <activeProfiles>
    <activeProfile>default</activeProfile>
  </activeProfiles>
</settings>
```

Next configure the following parameters under the `config` section of the `build.yml`:

`mvn-config` - The path to the maven configuration file (settings.xml) in the repo.

`mvn-repo-username` - The encrypted username used to resolve maven dependencies.

`mvn-repo-password` - The encrypted password used to resolve maven dependencies.

??? "How to Encrypt Values"
    {%
    include-markdown "./encryption.md"
    %}

    {%
    include-markdown "./encrypt-each-repo.md"
    %}