# Using Spring Boot Applications with Open Liberty

Learn how to configure an existing Java Spring Boot project using Maven for Open Liberty server. This guide assumes the user is already familiar with developing Spring Boot applications along with Java and Maven.

This guide explains how to configure your project to package a WAR artifact. The Maven WAR Plugin is "responsible for collecting all artifact dependencies, classes and resources of the web application and packaging them into a web application archive."

You should also configure Open Liberty to load the WAR artifact and set up a basic health check endpoint.

Finally, this guide explains how to customize the CD automation of the Cirrus Application to use this health endpoint.

{%
  include-markdown "./includes/java_warning.md"
%}

### Step 1: Configure the pom.xml

Configure your pom.xml appropriately with the following:

```xml
<artifactId>my-app-name</artifactId>
<packaging>war</packaging>
<build>
  <finalName>${project.artifactId}</finalName>
  <plugins>
    <plugin>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-maven-plugin</artifactId>
    </plugin>
    <plugin>
      <groupId>io.openliberty.tools</groupId>
      <artifactId>liberty-maven-plugin</artifactId>
      <version>3.3.4</version>
    </plugin>
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-war-plugin</artifactId>
      <version>3.3.1</version>
    </plugin>
  </plugins>
</build>
```

### Step 2: Configure the server.xml

Configure the `server.xml` file located at `src/main/liberty/config/server.xml` with the following:
Copy "**my-app-name**" mentioned in the previous pom.xml step and update the `<springBootApplication location=my-app-name.war />` to the file packaged by maven.

```xml
<server>

  <featureManager>
    <feature>mpHealth-3.0</feature>
    <feature>springBoot-2.0</feature>
    <feature>servlet-4.0</feature>
  </featureManager>

  <httpEndpoint host="*" httpPort="9080" httpsPort="-1" id="defaultHttpEndpoint" />

  <springBootApplication id="open-liberty-spring-boot"  name="open-liberty-spring-boot" location="open-liberty-spring-boot.war"/>

  <applicationManager autoExpand="true" />

  <webContainer deferServletLoad="false"/>

</server>
```

### Step 3: Configure CD automation for mpHealth

By enabling the `mpHealth` feature in the server.xml above, your application is ready to respond to HTTP GET `/heatlh` health probes.

**Note:** When mpHealth is enabled, Spring Boot won't be able to serve sub-routes under `/health`, i.e. `/health/ping`.

Use the bash script below to bootstrap the application's Cirrus CD automation for the mpHealth endpoint. Before running the script, set the value of the shell variable `APP_NAME` to the desired name of your application on Cirrus (e.g.: APP_NAME=orgname-reponame).

```bash
mkdir deploy deploy/test deploy/pre-production deploy/production

cat <<EOF >./deploy/test/application.yaml
apiVersion: cirrus.ibm.com/v1alpha1
kind: Application
metadata:
  name: ${APP_NAME}-test
spec:
  livenessProbe:
    httpGet:
      path: /health
    periodSeconds: 30
    timeoutSeconds: 30
  readinessProbe:
    httpGet:
      path: /health
    periodSeconds: 30
    timeoutSeconds: 30
  replicas: 1
EOF

cat <<EOF >./deploy/pre-production/application.yaml
apiVersion: cirrus.ibm.com/v1alpha1
kind: Application
metadata:
  name: ${APP_NAME}-preprod
spec:
  livenessProbe:
    httpGet:
      path: /health
    periodSeconds: 30
    timeoutSeconds: 30
  readinessProbe:
    httpGet:
      path: /health
    periodSeconds: 30
    timeoutSeconds: 30
  replicas: 2
EOF

cat <<EOF >./deploy/production/application.yaml
apiVersion: cirrus.ibm.com/v1alpha1
kind: Application
metadata:
  name: ${APP_NAME}-prod
spec:
  livenessProbe:
    httpGet:
      path: /health
    periodSeconds: 30
    timeoutSeconds: 30
  readinessProbe:
    httpGet:
      path: /health
    periodSeconds: 30
    timeoutSeconds: 30
  replicas: 3
EOF

```

### Step 4: Configure the build.yml

1. Follow [Guide: Building and Deploying a Java application to Cirrus](java-and-cirrus.md) on how to configure the `build.yml` for Java Maven pipelines deploying to Cirrus.

2. Add and set the `deploy-to-apps` parameter in the config section of the `build.yml` to `true` to ensure the war file is deployed in the apps folder instead of the dropins folder of the Open-Liberty server.

Example:

```yaml
apiVersion: automation.cio/v1alpha1
kind: RepositoryConfig
build:
  strategy: cirrus-deployment
  pipeline: java-v17-mvn-ol-container-image
  version: v1
  config:
    deploy-to-apps: "true"
    ...
```

## Notes

Please refer to the [Security Testing Page](./security-testing.md) for more on TwistLock, Sonarqube, and Mend.