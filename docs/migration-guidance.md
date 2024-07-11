# Migration guidance

!!! note
    z/OS users proceed to the [z/OS overview page](./zos/overview.md)

## Overview
This page contains migration guidance for users coming from **Travis** and deprecated **Cirrus** pipelines, and also applies generally to any new user. If you are new to CIO CI/CD, see our [W3Publisher page](https://w3.ibm.com/w3publisher/cio-ci-cd){target=\_blank} for a short and comprehensive overview plus ways to get engaged with the product.  We added more [Office Hours](https://ec.yourlearning.ibm.com/w3/series/10272011?layout=grid){target=\_blank} sessions to field the increase in questions and concerns.

CIO CI/CD facilitates the corporation mission of ITSS compliance and consistency, and there is over a year’s worth of development to realize this goal.

!!! note
    CIO CI/CD implements image signing and **verification** at scale with Cirrus.  Verification is proof required by ITSS.

## General Guidance

!!! note
    To get an idea of how a migration works, check out this [10 minute video](https://ibm.box.com/s/e1kgzzpiszemj8ds6e2ldmscja5q9759){target=\_blank} showing tips for a migration from Travis to CIO CI/CD (Spring Boot application).

The primary suite of pipelines that exist today are Java, Node and Python based.  **Read our [Decision page](./decision.md) which lists all of them and is kept up-to-date.**

Broadly speaking, pipelines are divided into two categories: official and community.  Community pipelines have been built by InnerSource and some official pipelines have been built by InnerSource as well.  This distinction is important because the CIO CI/CD team is small and relies  on InnerSource to be successful especially for the migration.

Another distinction are compliance vs container pipelines.  Compliance pipelines do not perform deployment.  They meet only a subset of ITSS chapter 5 requirements. Using them on their own can be helpful, but they are a stepping stone to the full CI/CD pipelines.

!!! note
    Community pipelines may indicate that they do not meet your level of support.  If so, please list your [BCV level](https://w3.ibm.com/w3publisher/application-management-from-dev2prod/documentation/business-continuity-management){target=\_blank} in [this issue](https://github.ibm.com/cio-ci-cd/pipeline-catalog/issues/1062){target=\_blank}.  CIO CI/CD is not requiring teams to change their BCV level.

Following these steps to get engaged with CIO CI/CD:

1. Bookmark our [W3Publisher page](https://w3.ibm.com/w3publisher/cio-ci-cd){target=\_blank}, note the links on the bottom left to our technical documentation and register for the Office Hours, Feature Presention and Blog.

1. Post a message in our support channel, [#dx-platform-support](https://my.slack.com/archives/C02AM16DR19){target=\_blank} with technical detail describing your application listing and any gaps in CIO CI/CD.

2. Our team will try to solve your needs with our current offering or direct you to our [InnerSource process](https://pages.github.ibm.com/cio-ci-cd/pipeline-catalog-docs/#getting-started){target=\_blank}, which explains how to submit a new request.

Many teams are requesting [multi-project](https://github.ibm.com/cio-ci-cd/pipeline-catalog/issues/1029){target=\_blank} and [multi-region](https://github.ibm.com/cio-ci-cd/ciocicd-project-board/issues/1236){target=\_blank} support.  Those are enhancements to our [Container Release Management](./container-release.md) feature, and are top priorities. Feel free to add your comments to the above Github issues.

!!! note
    Please see our [Features and Roadmap](./features-roadmap.md#innersource) page for a list of InnerSource contributions in development.  If you don't see the pipeline you need there or on our [Pipeline Decision](./decision.md) page, consider opening a new contribution via our [InnerSource process](https://pages.github.ibm.com/cio-ci-cd/pipeline-catalog-docs/#getting-started).  Also, consider offering help on current InnerSource contributions to accelerate development.

## Specific Guidance

### S2i
Follow the above [General Guidance](#general-guidance) documentation.  Ruby users follow and comment on [this Github issue](https://github.ibm.com/cio-ci-cd/pipeline-catalog/issues/830){target=\_blank} in our team's backlog.  GoLang users do the same for [this Github issue](https://github.ibm.com/cio-ci-cd/pipeline-catalog/issues/263){target=\_blank}.

### BYOI
Review the [list of available pipelines](https://pages.github.ibm.com/cio-ci-cd/documentation/decision/){target=\_blank} to verify if CIO CI/CD fits your needs or if you are able to board by changing your current processes.  If your application is not a fit, submit your automation as an [InnerSource request](https://pages.github.ibm.com/cio-ci-cd/pipeline-catalog-docs#getting-started){target=\_blank} and a CIO CI/CD team member will contact you for evaluation. Continue to check this page for updates and monitor our support channel: [#dx-platform-support](https://my.slack.com/archives/C02AM16DR19){target=\_blank}.  Additional guidance and process will be available soon.  Bring your inquiries to our [Office Hours](https://ec.yourlearning.ibm.com/w3/series/10272011?layout=grid){target=\_blank} as well.

!!! Note
    You may notice in our documentation that CIO CI/CD deployes images using BYOI.  This will be going away as CIO CI/CD will become a trusted integration with Cirrus.

### DockerFile
One design goal for CIO CI/CD is consistency and therefore it does not support DockerFile.  DockerFile allows a high level of customization which is not possible to implement or support in a single system.  In addition, ITSS compliance is challenging and most often not possible with Dockerfile.  We have received input from a number of adopters and as a result created the following [DockerFile guidance](https://w3.ibm.com/w3publisher/cio-ci-cd/dockerfile-guidance){target=\_blank} page.  Also, we encourage you to bring inquiries to our [Office Hours](https://ec.yourlearning.ibm.com/w3/series/10272011?layout=grid){target=\_blank}.

### Deployment Targets
CIO CI/CD supports Cirrus and z/OS deployments.  If this does not fit your needs, we welcome InnerSource requests.  See our [contribution guidance](https://pages.github.ibm.com/cio-ci-cd/pipeline-catalog-docs/){target=\_blank}.

### Build-time environment variables
For security purposes, CIO CI/CD has a prescriptive approach to the usage of build-time environment variables.  Read our [Guidance on Build-time Environment Variables](./guidance-env-vars.md) page for more information.

## Community Channels
IBM-wide Slack channels exist for each community pipeline to facilitate collaboration between maintainers and users.  If you need to make an enhancement to board the pipeline or otherwise, an [InnerSource contribution](https://pages.github.ibm.com/cio-ci-cd/pipeline-catalog-docs/){target=\_blank} is required.  If you are going to make the contribution, post a message in the channel and make sure you receive acknowledgement from a community maintainer.

- [#cio-cicd-golang-pipeline](https://my.slack.com/archives/C04RWLDTVL3){target=\_blank}
- [#cio-cicd-gradle-pipeline](https://my.slack.com/archives/C04G0BY8212){target=\_blank}
- [#cio-cicd-mvn-jar-pipeline](https://my.slack.com/archives/C04Q81E7SF2){target=\_blank}
- [#cio-cicd-mvn-springboot-layertools-pipeline](https://my.slack.com/archives/C05JLNJGQHF){target=\_blank}
- [#cio-cicd-openresty-pipeline](https://my.slack.com/archives/C04L54FDZK9){target=\_blank}
- [#cio-cicd-pnpm-pipeline](https://my.slack.com/archives/C05KWM6C80H){target=\_blank}
- [#cio-cicd-pnpm-ui-pipeline](https://my.slack.com/archives/C05EKHV2QP6){target=\_blank}
- [#cio-cicd-yarn-pipeline](https://my.slack.com/archives/C04PLQK8STW){target=\_blank}
- [#cio-cicd-ruby-pipeline](https://my.slack.com/archives/C05UPFX3GG7){target=\_blank}

## Frequently Asked Questions

### General questions

??? question "'Is there a way to request an exception or extension (Risk, etc), and what is the process and criteria?'"

    <a id='exception-extension' href='#exception-extension'>Link to this section</a>

    The CIO CI/CD team is not involved in timelines or exceptions.  The sole capacity of the team is to facilitate onboarding onto the pipeline by building core functionality and facilitating InnerSource contributions.  We have, however, increased the frequency of our office hours and invited leadership to address questions unrelated to onboarding.


??? question "'When the Cirrus pipelines are removed, will the applications stop or be removed as well?'"

    <a id='pipego-appgo' href='#pipego-appgo'>Link to this section</a>

    No. There is no effect to running applications.


### Feature questions

??? question "'Can we get a one-click approval button that would facilitate product owner driven deployments?'"

    <a id='one-click' href='#one-click'>Link to this section</a>

    The Developer Portal team is working on a feature to invoke pipelines via a button.  Also note our [Container Release Management](./container-release.md) feature which has a pause feature to enable stakeholders to review before production deployment.

??? question "'How can we get a library pipeline?'"

    <a id='library-pipe' href='#library-pipe'>Link to this section</a>

    We have a Library pipeline for [Node.js](https://pages.github.ibm.com/cio-ci-cd/documentation/decision/#nodejs){target=\_blank}.  If you are not using Node.js, submit an [InnerSource](https://pages.github.ibm.com/cio-ci-cd/pipeline-catalog-docs/#getting-started){target=\_blank} contribution.


??? question "'When will the pipeline support C++?'"

    <a id='c-plus' href='#c-plus'>Link to this section</a>

    C++ support is not on our roadmap but you can contribute a pipeline via [InnerSource](https://pages.github.ibm.com/cio-ci-cd/pipeline-catalog-docs/){target=\_blank}.


??? question "'When will the new BYOI support be available ([Issue 1292](https://github.ibm.com/cio-ci-cd/ciocicd-project-board/issues/1292){target=\_blank})?'"

    <a id='new-byoi' href='#new-byoi'>Link to this section</a>

    We have work planned to support deployment of approved existing and vendor images.  If you have an additional need, please [create an issue](https://github.ibm.com/cio-ci-cd/pipeline-catalog/issues/new?assignees=&labels=enhancement&template=proposal.md&title=%5BFeature%5D){target=\_blank} and our team will review.


??? question "'Will there be a GoLang pipeline based on a multi-service repository?'"

    <a id='go-multi-serve' href='#go-multi-serve'>Link to this section</a>

    There is a [GoLang container pipeline](./golang-and-cirrus.md){target=\_blank}. It does not include multi-service, but if you are interested contribute via [InnerSource](https://pages.github.ibm.com/cio-ci-cd/pipeline-catalog-docs/){target=\_blank}.


??? question "'What is the guidance for C/C++ apps that include DB2 client?'"

    <a id='c-db2' href='#c-db2'>Link to this section</a>

    A C/C++ pipeline itself or one combined  with Java to support DB2 Client requires an [InnerSource](https://pages.github.ibm.com/cio-ci-cd/pipeline-catalog-docs/){target=\_blank} contribution.

### Technical Questions

??? question "'What is the process if we have multiple environments (DEV / UAT / STG / PROD)?'"

    <a id='multi-targets' href='#multi-targets'>Link to this section</a>

    CIO CI/CD uses the [Trunk-based](./cirrus-branch-strategy.md) development methodology.  The same image that is deployed to pre-Prod is also used for Prod.  Images are not re-built.

??? question "'What is the process for UI apps that have build-time environment variables?'"

    <a id='env-vars' href='#env-vars'>Link to this section</a>

    An InnerSource contribution for [envsubst](https://github.ibm.com/cio-ci-cd/pipeline-catalog/issues/1103){target=\_blank} is in progress.


??? question "'How do I build multiple servers from a single image with different configurations?'"

    <a id='multi-servers' href='#multi-servers'>Link to this section</a>

    A core offering is in design.  If you are using Maven multi-module deployment, consult our [Cirrus Deployment Customization](./cirrus-deployment-customization.md) page.


??? question "'Is it possible to create an image from multi-language source code?'"

    <a id='multi-lang' href='#multi-lang'>Link to this section</a>

    Not at this time, but we welcome an [InnerSource](https://pages.github.ibm.com/cio-ci-cd/pipeline-catalog-docs/){target=\_blank} contribution.


??? question "'How can we have two static environments?  We need one more in addition to preprod and production.'"

    <a id='two-env' href='#two-env'>Link to this section</a>

    You can have more than one environment with our [Container Release Management](./container-release.md) feature.  If you are not using Container Release Management, [create an issue](https://github.ibm.com/cio-ci-cd/pipeline-catalog/issues/new?assignees=&labels=enhancement&template=proposal.md&title=%5BFeature%5D){target=\_blank} describing you need and we will consult with you.


??? question "'How do I use the pipeline for a Golang webserver with React and Angular apps?'"

    <a id='go-re-an' href='#go-re-an'>Link to this section</a>

    There is a [GoLang container pipeline](./golang-and-cirrus.md){target=\_blank}. We do not have a GoLang pipeline for React and Angular but you may contribute one via [InnerSource](https://pages.github.ibm.com/cio-ci-cd/pipeline-catalog-docs/){target=\_blank}.  React and Angular are supported for [NodeJS](./decision.md#nodejs) today.


??? question "'Is it possible to disable auto-create of application for each branch?'"

    <a id='disable-autocreate' href='#disable-autocreate'>Link to this section</a>

    No, however, applications are deleted when branches are deleted.


??? question "'Can DevEx Mend be more in sync with Github Mend?'"

    <a id='diff-mend' href='#diff-mend'>Link to this section</a>

    We do not have control over GitHub Mend, but are interested in learning about the differences.  When you see them, please feel free let us know in our support channel [#dx-platform-support](https://my.slack.com/archives/C02AM16DR19){target=\_blank}.


??? question "'How do we manage CIO CI/CD creating different internal paths for each micro service?'"

    <a id='internal-paths' href='#internal-paths'>Link to this section</a>

    Consult our [Cirrus Deployment Customization](./cirrus-deployment-customization.md) page.  If that does not solve your requirement, file an issue in our [backlog](https://github.ibm.com/cio-ci-cd/pipeline-catalog/issues/new?assignees=&labels=enhancement&template=proposal.md&title=%5BFeature%5D){target=\_blank}.


??? question "'Do you support the ability to deploy a branch (not main) to production?'"

    <a id='deploy-non-main' href='#deploy-non-main'>Link to this section</a>

    No. The default branch must match production.


??? question "'What is the recommendation for systems that are currently organized with monorepo?'"

    <a id='mono-rec' href='#mono-rec'>Link to this section</a>

    Our guidance is to break them up into separate repositories, but you may also contribute a solution via [InnerSource](https://pages.github.ibm.com/cio-ci-cd/pipeline-catalog-docs/){target=\_blank}.  Solutions that use a common tool to manage mono repos are preferred.


??? question "'How to run _project specific_ **Dockerfiles**?'"

    <a id='proj-specific-dockerfile' href='#proj-specific-dockerfile'>Link to this section</a>

    CIO CI/CD only works with common Dockerfiles.


??? question "'How to make **Dockerfile** to use given _parameters_?'"

    <a id='make-dockerfile' href='#make-dockerfile'>Link to this section</a>

    CIO CI/CD only works with common Dockerfiles.  Submit an [issue](https://github.ibm.com/cio-ci-cd/pipeline-catalog/issues/new?assignees=&labels=enhancement&template=proposal.md&title=%5BFeature%5D){target=\_blank} if this parameters requirement is not met and one of our team members will look at your implementation and provide consultation.

??? question "'How to run **integration tests** if the language of test differs from the application (Java applications with JS/TS integration tests)?'"

    <a id='int-test-diff-lang' href='#int-test-diff-lang'>Link to this section</a>

    Today, it is not possible.  There is InnerSource contribution ([#961](https://github.ibm.com/cio-ci-cd/pipeline-catalog/issues/961)) in progress.


??? question "'How to add missing/required **Gradle tasks** (like `JacocoTestCoverageVerification`?)'"

    <a id='missing-gradle-tasks' href='#missing-gradle-tasks'>Link to this section</a>

    Consult the Gradle community via the [#cio-cicd-gradle-pipeline](https://my.slack.com/archives/C04G0BY8212) Slack channel.


??? question "'How to increment **MAJOR and MINOR version** automatically – without manually writing them?'"

    <a id='auto-major-minor' href='#auto-major-minor'>Link to this section</a>

    If you have an idea for a workflow, please submit it via [InnerSource](https://pages.github.ibm.com/cio-ci-cd/pipeline-catalog-docs/){target=\_blank}.


??? question "'How could we check the **application version** after deployment?'"

    <a id='app-version' href='#app-versioni'>Link to this section</a>

     The [Cirrus Portal](https://cirrus.ibm.com/){target=\_blank} shows the version that is deployed.


??? question "'How could we **rollback production** applications?'"

    <a id='rollback' href='#rollback'>Link to this section</a>

    Pipelines do not support rollback and follow a fix-forward methodology. An alternative is to manually edit the image on Cirrus.

### Contribution questions


??? question "'How does the contribution development process work?'"

    <a id='contrib-process' href='#contrib-process'>Link to this section</a>

    Read the main page of our [Pipeline Catalog InnerSource Contribution Guidelines](https://pages.github.ibm.com/cio-ci-cd/pipeline-catalog-docs/){target=\_blank}.  Once you support a contribution idea, our team will consult with you.  Read each page per the tabs at the top of the Pipeline Catalog to get started.


??? question "'How does the review-release process work?'"

    <a id='review-release' href='#review-release'>Link to this section</a>

    Read the content in the tabs of our [Pipeline Catalog InnerSource Contribution Guidelines](https://pages.github.ibm.com/cio-ci-cd/pipeline-catalog-docs/){target=\_blank}.


??? question "'Do we need special access (if yes, where can we request them)?'"

    <a id='#special-access' href='#special-access'>Link to this section</a>

    The access required is `admin` level of your Github Org and a team member with production deployment rights in Cirrus to create the API key.


??? question "'What kind of tools do we need to be able to run them in local environment.?'"

    <a id='tools-for-local' href='#tools-for-local'>Link to this section</a>

    See the Local Development tab of our Pipeline Catalog [InnerSource Contribution Guidelines](https://pages.github.ibm.com/cio-ci-cd/pipeline-catalog-docs/local-development/).
