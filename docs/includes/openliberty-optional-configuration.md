#### Open Liberty Version & Java Distribution

The Java Open Liberty pipelines are configured to build application images based on Open Liberty **23.0.0.9** and the **OpenJ9** Java distribution. If your application requires a different version of Open Liberty or the IBM Java distribution, you can specify alternate configurations in your build configuration.

To use a specific version of Open Liberty, include `open-liberty-version: <version-number>` in the `config:` section of your `build.yml`. To use IBM Java, include `open-liberty-java-type: ibmjava`.

!!! note

    **IBM Java is only available with Java 8**. Specifying `open-liberty-java-type: ibmjava` for other Java versions will result in build failures.

The example below shows how to configure a Java 8 pipeline with Open Liberty 23.0.0.6 and IBM Java:

```yaml
apiVersion: automation.cio/v1alpha1
kind: RepositoryConfig
build:
  strategy: cirrus-deployment
  pipeline: java-v8-mvn-ol-container-image
  version: v2
  config:
    open-liberty-version: 23.0.0.6
    open-liberty-java-type: ibmjava
    ...
```

See the [Open Liberty container images](https://openliberty.io/docs/latest/container-images.html) for further details on versioning and java distributions.

#### OpenJ9 Shared Class Cache

Open Liberty is configured to populate the OpenJ9 Shared Class Cache (SCC) with your application classes during image build. This feature is enabled by default in Open Liberty images, as it reduces application startup times. However, it may cause build failures if configuration files depend on run-time variables that are not present at build time.

To disable the OpenJ9 Shared Class Cache, include `enable-openj9-scc: "false"` in the `config:` section of your `build.yml`:

```yaml
apiVersion: automation.cio/v1alpha1
kind: RepositoryConfig
build:
  strategy: cirrus-deployment
  pipeline: java-v17-mvn-ol-container-image
  version: v1
  config:
    deploy-to-apps: "true"
    enable-openj9-scc: "false"
    ...
```

See the [WebSphere Application Liberty documentation](https://www.ibm.com/docs/en/was-liberty/core?topic=containers-implementing-openj9-shared-class) for further details.
