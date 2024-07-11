# Controlled access to development and build environments

The build environment for CIO CI/CD has tightly controlled access. In short, the environment is provided by Cirrus, with the Cirrus team managing access and giving the CIO CI/CD Pipeline team limited access. Adopters do not have access to the build environment.

!!! question "How is access controlled to the code repository as well as to all the systems and tools used by both individuals and automation in the development and build process?"

    Access to the code repository is granted through the installation of a GitHub App. This app has a limited scope of permissions required to build code. Access must first be granted by installing the GitHub App. In order to revoke access, teams can uninstall the GitHub App.

    For access to the OpenShift cluster where builds are run, Cirrus manages access since they provide the cluster. Our team does not have the ability to grant additional access to this environment.

!!! question "Who approves the access?"

    Access to the code repository is approved by application teams when they install the GitHub App.

    Access to the build environment is granted by the Cirrus team via Bluegroup. That process is described on this runbook [page](https://github.ibm.com/cio-ci-cd/runbook/blob/main/docs/build-cluster.md#cluster-access){:target="_blank"}.

!!! question "How frequently is access revalidated?"

    Access to the code repository is revalidated on every push to the repository. A limited time token is generated for each build which lasts for one hour.

    The Cirrus team re-validates access to the build environment.

!!! question "How, and how soon, is access removed when it is no longer needed?"

    Access to the code repositories is removed when the GitHub app is uninstalled. Existing tokens will expire after one hour.

    Access to the build environment is managed by the Cirrus team.

!!! question "How are credentials secured?"
 
    Access to code repositories is managed by the GitHub app. Credentials for the code repository access are generated for each pipeline, which have a lifespan of one hour. The private keys to generate those credentials are encrypted, added to GitHub, then deployed to the build cluster as a secret. Access to this secret is limited to those with access to the build cluster.
