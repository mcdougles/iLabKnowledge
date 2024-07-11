If your project uses private dependencies and TaaS Jfrog Artifactory is not in option, you can use local maven repository. Setup Local maven Repository in your root directory of project. 

Use below command to create Local Maven Repository for required dependencies. 
```shell
mvn install:install-file -DgroupId=YOUR_GROUP -DartifactId=YOUR_ARTIFACT -Dversion=YOUR_VERSION -Dfile=YOUR_JAR_FILE -Dpackaging=jar -DgeneratePom=true -DlocalRepositoryPath=./LocalMavenRepo -DcreateChecksum=true
```

Replace **YOUR_GROUP, YOUR_ARTIFACT, YOUR_VERSION and YOUR_JAR_FILE** as required. 
YOUR_JAR_FILE should point to an existent jar file in accessible directory in system's local path. 
Once all jars are added using above command you can move the LocalMavenRepo directory to project root path if created in different directory.


Create checksum for all files in mentioned local repository path using `shasum -a 256`, `md5sum` and `sha1sum`.

**Update build.yml**

Add following in `build.yml`. The below config will run mvn install goal during compile and package phase which is required when using Local Maven Dependencies

```yaml
    config:
        mvn-goal: 'install'
        # other configurations
```

**Update pom.xml**

Add following in pom.xml. Following configuration in pom.xml specifies path of local repository (e.g. LocalMavenRepo directory).
```xml
  <repositories>
    <repository>
      <id>maven-repo</id>
      <url>file://${project.basedir}/LocalMavenRepo</url>
    </repository>
  </repositories>
```
