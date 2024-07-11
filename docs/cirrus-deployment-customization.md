# Cirrus Deployment Customization

This page explains how to provide Cirrus resources in your repository so that the CI/CD pipelines can deploy your application to Cirrus with your specific customizations.

{%
  include-markdown "./includes/deployment-testing.md"
%}

## The Deploy Resources Directory

Create a directory named `deploy` at the top-level of your repository. Inside of the `deploy` directory you can create a sub-directory named for the environments you wish to target (test / pre-production / production). Add resource definition yaml files inside the respective sub-directory. CD automation will scan all yaml files in target environment's directory and add them to the deployment.

See the following example of the directory structure from a Node.js repository that has customized deployment files for the test environment. It contains Application and Route resource definition files for the test environment. The empty `pre-production` and `production` directories are shown for the example and can be omitted if unused.

```
.
├── README.md
├── build.yml
├── deploy
│   ├── pre-production
│   ├── production
│   └── test
│       └── application.yaml
│       └── route.yaml
├── package-lock.json
└── package.json
```

!!! note

    The suggested naming convention of yaml files is the resource kind name in lower-kebab case. For example, `application.yaml` is for a file containing a resource definition for an Application. Automation adds all files ending in `.yaml` found in the target environment directory to the deployment. A `kustomization.yaml` file found in an environment's directory is acknowledged but is not required.

### Multi-Region

If separate configurations are desired for a particular region, create another sub-directory within the environmemnt's directory with the region name as it appears in the build.yml. For example to define deployment resources for the region `us-south2` when deploying to production, use the following structure:

```
.
├── README.md
├── build.yml
└── deploy
    ├── pre-production
    │   ├── application.yaml
    │   └── route.yaml
    ├── production
    │   ├── application.yaml
    │   ├── route.yaml
    │   └── us-south2
    │       ├── application.yaml
    │       └── route.yaml
    └── test
        ├── application.yaml
        └── route.yaml
```

In this example, when the build.yml specifies deplyoment to `us-south2` during a production pipeline, the yaml resource files defined under the path `deploy/production/us-south2` will be used. If the build.yml is also configured to deploy to the region `us-east1` in this example, then the yaml resource files defined under the path `deploy/production` will be used as there is no sub-directory for that region.

!!! note

    Currently, when requesting `us-south2` in the build.yml, the `us-south1` cluster is being used for production deployments. In this situation, the sub-folder must still be named `us-south2` as it is defined in the build.yml.

## Default CD Configuration

Deployment customization is optional and there is no need to create the directory structure or any yaml files. The default CD configuration will be used to deploy the build.

Some examples of when customization is needed are:

- A route must be exposed to the Application.
- Setting the number of replicas.
- Configuring the health check endpoints.
- Setting the memory quota for the Application.
- Injecting environmental variables.
- Overriding the service-port.

Keep reading this guide to learn how to configure these examples.

## Quick Start

Select and edit from the following bash-like command examples to help get started with the basic files required to customize a deployment.

```yaml
################################################################################
#
# Create the directory structure in your repository:
#
################################################################################

mkdir deploy deploy/test deploy/pre-production deploy/production


################################################################################
#
# Start with a basic test Application:
#
################################################################################

cat <<EOF >./deploy/test/application.yaml
apiVersion: cirrus.ibm.com/v1alpha1
kind: Application
metadata:
  name: orgname-reponame-test
spec:
  livenessProbe:
    httpGet:
      path: /health/ping
    periodSeconds: 30
    timeoutSeconds: 30
  readinessProbe:
    httpGet:
      path: /health/ping
    periodSeconds: 30
    timeoutSeconds: 30
  replicas: 1
  quota: q512mb
EOF


################################################################################
#
# Start with a basic pre-production Application:
#
################################################################################

cat <<EOF >./deploy/pre-production/application.yaml
apiVersion: cirrus.ibm.com/v1alpha1
kind: Application
metadata:
  name: orgname-reponame-preprod
spec:
  livenessProbe:
    httpGet:
      path: /health/ping
    periodSeconds: 30
    timeoutSeconds: 30
  readinessProbe:
    httpGet:
      path: /health/ping
    periodSeconds: 30
    timeoutSeconds: 30
  replicas: 2
  quota: q512mb
EOF


################################################################################
#
# Start with a basic production Application:
#
################################################################################

cat <<EOF >./deploy/production/application.yaml
apiVersion: cirrus.ibm.com/v1alpha1
kind: Application
metadata:
  name: orgname-reponame-prod
spec:
  livenessProbe:
    httpGet:
      path: /health/ping
    periodSeconds: 30
    timeoutSeconds: 30
  readinessProbe:
    httpGet:
      path: /health/ping
    periodSeconds: 30
    timeoutSeconds: 30
  replicas: 3
  quota: q512mb
EOF


################################################################################
#
# Opt-In to exposing a Route for the production environment:
#
################################################################################

cat <<EOF >./deploy/production/route.yaml
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  name: orgname-reponame-prod
EOF
```

## Application Customization

For full reference to the object model of a Cirrus Application and other related resources, refer to the Cirrus docs [here](https://pages.github.ibm.com/CIOCloud/cio-blog/cli/#creating-an-application).

### Naming

We suggest the naming convention `<orgname>-<reponame>-<environment>` for your Applications and other resources. The name of the Application is set in the `.metadata.name` field. The Application name is also used as part of the sub-domain when routes are being created. For example if the `.metadata.name` of a production Application hosted in `us-south` is `ciocicd-demo-production` then the Route would be `https://ciocicd-demo-production.dal1a.ciocloud.prod.intranet.ibm.com`.

Resource names must be all lower-case, alpha-numeric and can include the dash character `-`.

When deploying to the test environment (feature branches), automation suffixes the provided `.metadata.name` in the repository with the branch name for all resources in the deployment. For example if the `.metadata.name` on a `test` Application is set to `ciocicd-demo-test` on a branch named `feature-1`, the deployed Application's `.metadata.name` will be `ciocicd-demo-test-feature-1`. Thus, the generated route for the `test` Application hosted in `us-south` will be `https://ciocicd-demo-test-feature-1.dal1a.ciocloud.nonprod.intranet.ibm.com`.

### Service Port

The `.spec.port` field of an Application resource definition defaults to the value of the `service-port` parameter from the `build.yml` and should therefore be omitted for simplicity. The `service-port` parameter is used to expose the port in the application's container image and has a platform specific default value.

### Health Checks

Pipelines deploying applications to Cirrus **require health check support**. By default, an Application's health checks are configured as HTTP requests to `GET /health/ping` over the Application's `service-port`.

There are two types of health checks - the **liveness probe** and the **readiness probe**. Both are required and can be set to the same endpoint. Refer to the Kubernetes documentation [Configure Liveness, Readiness and Startup Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/) for explanation on types of health checks and how they can be configured.

Consider the snippet below showing the default health check configuration of an Application:

```yaml
---
spec:
  livenessProbe:
    httpGet:
      path: /health/ping
    periodSeconds: 30
    timeoutSeconds: 30
  readinessProbe:
    httpGet:
      path: /health/ping
    periodSeconds: 30
    timeoutSeconds: 30
```

### Routes

External routes are not automatically exposed for Applications. The quick start example above contains a snippet on how to expose a route for the production Application.
A yaml file with the following fields are all that is needed inside of a target environment's directory.

```yaml
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  name: orgname-reponame-environment
```

As mentioned in the Naming section above, the generated route's sub-domain will be suffixed with the branch name for the `test` environment only.

The `.metadata.name` of the Route is for identification only and is not used as part of the sub-domain. The sub-domain of the Route is based on the Application's name. If the Application name exceeds 63 characters, it will be truncated to the first 57 characters and appended with a single dash `-` character followed by the first 5 characters of the original name's MD5 hash to make it unique. For example an application named:

```
abcdefghijklmnopqrstuvwxyz-ABCDEFGHIJKLMNOPQRSTUVWXYZ-0123456789
```

will be shortened to:

```
abcdefghijklmnopqrstuvwxyz-abcdefghijklmnopqrstuvwxyz-012-64fdc
```

### Environment Variables

!!! note

    Ensure that sensitive data is stored in [Secrets](#secrets); non-sensitive configurations can be stored in Kubernetes `ConfigMaps` or environment variables.

There are a couple ways to inject environment variables into an `Application` or `CronJob` hosted on Cirrus.

The Cirrus `Application` object itself supports the following example:

```yaml
apiVersion: cirrus.ibm.com/v1alpha1
kind: Application
metadata:
  name: orgname-reponame-test
spec:
  env:
    - name: FOO
      value: BAR
  envFrom:
    # (optional) injecting environment variables from Kubernetes ConfigMaps
    - configMapRef:
        name: name-of-your-configmap
  # ...
```

`CronJob` object example:

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: orgname-reponame-test
spec:
  schedule: '0 24 * * *' # Every 24 hours
  jobTemplate:
    spec:
      template:
        spec:
          containers:
            - name: job
              envFrom:
                # (optional) injecting environment variables from Kubernetes ConfigMaps
                - configMapRef:
                    name: name-of-your-configmap
              env:
                - name: FOO
                  value: BAR
  # ...
```

### Secrets

Kubernetes `Secret` resource definitions found in a deploy directory get deployed. The service used to encrypt strings for the `build.yml` is **NOT** supported anywhere in the `deploy` directory.

**DO NOT** put unencrypted sensitive data in deploy files like `Secret` resource definitions. Create `Secrets` for sensitive data directly on the Cirrus Portal and simply reference them in your `Application` or `CronJob`.

Cirrus `Application` object example:

```yaml
apiVersion: cirrus.ibm.com/v1alpha1
kind: Application
metadata:
  name: orgname-reponame-test
spec:
  envFrom:
    - secretRef:
        name: name-of-your-secret
  # ...
```

`CronJob` object example:

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: orgname-reponame-test
spec:
  schedule: '0 24 * * *' # Every 24 hours
  jobTemplate:
    spec:
      template:
        spec:
          containers:
            - name: job
              envFrom:
                - secretRef:
                    name: name-of-your-secret
  # ...
```

### Memory Quota

When you deploy an application as a container, you need to select an initial quota for your application. This is the maximum amount of memory that 1 pod of your application can consume.

To set the memory of the application, use the `.spec.quota` field.

Examples of valid values are q64mb, q128mb, q256mb, q512mb, q1gb. You can see the valid values in the pull down list when managing the memory tier on Cirrus. You can also run the command `oc get quotas` on the cluster to see the available quotas.

To troubleshoot issues with memory quota on Cirrus, review their [documentation](https://pages.github.ibm.com/CIOCloud/cio-blog/troubleshooting/troubleshooting-tips/#issues-with-memory-quota).
