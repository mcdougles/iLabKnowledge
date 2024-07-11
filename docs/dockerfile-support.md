## How does an onboarding team use their Dockerfile with CIO CI/CD?

The short answer is: _you can't_.

Longer answer:

Docker files are designed to build image layers.

At IBM we are required to comply with ITSS. Asking each team to meet those requirements in every Dockerfile creates a lot of duplicate effort. We know most teams are not compliant with ITSS yet. By adopting a common pipeline it should make it easier to gain compliance. Various compliance checks can be built into each pipeline, making it easier for teams to run production services.

## How do I convert my Dockerfile to use the CIO CI/CD pipeline?

The Dockerfiles we use are [stored here](https://github.ibm.com/cio-ci-cd/pipeline-catalog/tree/main/catalog/official/tasks/buildah/dockerfiles).

Also review the Cirrus guides for each supported language. [Node.js](./nodejs-and-cirrus.md), [Python](./python-and-cirrus.md) and [Java](./java-and-cirrus.md).

## How do I install package dependencies into my application's container image?

All pipelines that build container images support the installation of custom software packages through parameters in the `build.yml`. All of our base images are RHEL 8 UBI and use the DNF software package manager to install packages from the pre-configured repositories. The easiest way to search for available packages is to pull the RHEL UBI 8 image on Docker and run the `dnf search` command. For example, to search for the package `bzip2` run the following Docker commands:

```bash
docker pull registry.access.redhat.com/ubi8/ubi:8.6
docker run registry.access.redhat.com/ubi8/ubi:8.6 dnf search bzip2
```

Read more on [Universal Base Images (UBI): Images, repositories, packages, and source code](https://access.redhat.com/articles/4238681)

You can also use packages from [CentOS Stream](https://www.centos.org/centos-stream/). This can be used for [Cypress](https://www.cypress.io/), [Playwright](https://playwright.dev/), and other tools or application dependencies. CentOS Stream is on a lower priority than the UBI packages, so packages will only be installed from CentOS Stream if they are unavailable on the UBI package repository.

Once you have decided which packages to have installed during the build step of container images, configure the following parameters in the build config section of the `build.yml`.

- `install-packages-app` - What additional packages should be installed in the application's container image?
- `install-packages-verification` - What additional packages should be installed in the verification test image? (Where applicable)

For example:

```yaml
apiVersion: automation.cio/v1alpha1
kind: RepositoryConfig
ealImapNumber: EAL-011824
build:
  strategy: cirrus-deployment
  pipeline: nodejs-v18-npm-container-image
  version: v2
  config:
    install-packages-app: bzip2
    install-packages-verification: nss libctk
    ...
```

When your pipeline is run, you should see that the package `bzip2` was installed in the logs of the `build-app-image` task and that the packages `nss` and `libctk` were both installed in the  `build-verification-image` task.

!!! note

    All software packages are installed prior to running `dnf update`.

    Also, users are responsible for updating CVEs in their applications.

## How can I add package dependencies to the CI image used for CI tasks?
You may need additional packages present during pipeline CI tasks like install, unit-test, and compile. In your build.yml, indicate the necessary packages for the image's OS using the `install-packages-ci` key.

```yaml
# ...truncated for brevity...
build:
  config:
    install-packages-ci: java-1.8.0-openjdk-headless bzip2
```
In the example above, we're installing `java-1.8.0-openjdk-headless` and `bzip2` from Red Hat UBI8 repositories. The packages will now be available during all CI tasks in the Pipeline.

In a case where a package also needs to be on the application image OS during runtime, you can refer to this [section](#how-do-i-install-package-dependencies-into-my-applications-container-image). If you need a package in all three images (CI image, application image, and application verification image), you will need to specify it in all three keys. You should only request what you need to improve speed and adhere to the ITSS requirement of limiting unused packages from images.

## I need to deploy a third-party tool. How can I do this using CIO CI/CD?

!!! note

    Please review the [Open Source@IBM guidance for consuming open source code](https://w3.ibm.com/developer/docs/open-source/consume-open-source/#open-source-code-and-data) before proceeding.

If you need to deploy an **open source** third-party tool (e.g. Airflow, Superset), CIO CI/CD offers a workflow for this.

You may opt to use an approved container image from the [CIO CI/CD image catalog](https://github.ibm.com/cio-ci-cd-image-catalog). These custom images can be deployed to Cirrus using the [Approved Container Image Pipeline](./deploy-approved-images.md), which allows for the reuse of approved images.

The base image for these container images will always be a [Red Hat UBI image](https://catalog.redhat.com/software/base-images). The image will include the third party tool(s) as package dependencies. For certain approved images, additional configuration may need to be provided via a ConfigMap.

To request support for a new container image, submit a request using [the issue template](https://github.ibm.com/cio-ci-cd-image-catalog/requests/issues/new?assignees=&labels=&template=container-image-request.md&title=). Be sure to fill out all the details.

Once the request has been submitted, the CIO CI/CD team will review it (whether the request is workable and the tool meets the [Open Source@IBM](https://w3.ibm.com/developer/docs/open-source/consume-open-source/) criteria for use) and decide to approve or reject it.

If the request is approved, the CIO CI/CD team will create a base repository and let requestors (and declared contributors) propose changes directly to the repo.

To learn more about building an approved image, refer to the [guide](./build-approved-images.md).
