The primary purpose of the CIO CI/CD project is to provide a way to achieve [ITSS Chapter 5 compliance](https://w3.ibm.com/#/support/article/itss/sd) regarding software security. A [Pipeline](https://tekton.dev/docs/pipelines/pipelines/#overview), if applicable, must run the following `official` [Tasks](https://tekton.dev/docs/pipelines/tasks/#overview):

- [`twistlock`](https://github.ibm.com/cio-ci-cd/pipeline-catalog/tree/main/catalog/official/tasks/twistlock-scan) - To scan an image for vulnerabilities
- [`detect secrets`](https://github.ibm.com/cio-ci-cd/pipeline-catalog/tree/main/catalog/official/tasks/detect-secrets) - To ensure no secrets are committed to GitHub
- [`sonarqube`](https://github.ibm.com/cio-ci-cd/pipeline-catalog/tree/main/catalog/official/tasks/sonarqube-scan) - To improve code quality by finding vulnerabilities, bugs, and code smells
- [`mend`](https://github.ibm.com/cio-ci-cd/pipeline-catalog/tree/main/catalog/official/tasks/mend-scan) - To ensure open-source license compliance and detect vulnerabilities
- [`cosign`](https://github.ibm.com/cio-ci-cd/pipeline-catalog/tree/main/catalog/official/tasks/cosign) - To digitally sign each image you create
- `contrast` - To detect software vulnerabilities at runtime

By having a `consistent` standard of metadata collection on Applications and how they are deployed and managed in the organization, we enhance the `productivity` of IBMers regarding the maintenance and protection of the Enterprise.

## ITSS Tools

| Pipeline Category     | Static Application Security Testing | Open Source Scanning | Secrets Scanning | Container Image Scanning | Artifact/Container Signing | Interaction Application Security Testing / Dynamic Application Security Testing |
| --------------------- | ----------------------------------- | -------------------- | ---------------- | ------------------------ | -------------------------- | ------------------------------------------------------------------------------- |
| Cirrus CI/CD          | SonarQube                           | Mend                 | Detect Secrets   | Twistlock                | Cosign                     | Contrast                                                                        |
| Native z/OS           | N/A                                 | N/A                  | Detect Secrets   | N/A                      | Custom OpenSSL             | N/A                                                                             |
| Compliance & Security | SonarQube                           | Mend                 | Detect Secrets   | Twistlock                | N/A                        | Contrast                                                                        |
| Library               | SonarQube                           | Mend                 | Detect Secrets   | N/A                      | N/A                        | N/A                                                                             |

## Available Pipelines

To see what language/tools we support, check out our [Decision page](./decision.md).

## Core Design

- Read this to learn about our [standard release strategy and container release strategy for Cirrus](../cirrus-branch-strategy/#overview)
- Visit the [developer portal](https://cirrus.ibm.com/developer/) for metrics and logs

## What we're working on

In order of importance. Priority; not definitive commitment. Updated 3/27/2024

### Q2 2024 Epics

| Name                                                                                                       | Business Value                                                                                                                                                  | Phase       |
| ---------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------- |
| [Run PipelineRuns on ROKs build clusters](https://jsw.ibm.com/browse/CICDCTLG-183)                         | Users will have performant pipeline run time during peak hours by eliminating infra limitation                                                                  | In Progress |
| [Deploying Approved OpenSource Images](https://github.ibm.com/cio-ci-cd/ciocicd-project-board/issues/1292) | Cirrus Sunset Users need a solution for deploying approved open source images directly to Cirrus                                                                | In Progress |
| [UBI minimal images and UBI 9 ](https://github.ibm.com/cio-ci-cd/ciocicd-project-board/issues/1496)        | Users will see reduced vulnerabilities from Twistlock in the images the CI/CD pipelines produce outside of their control                                        | In Progress |
| [Pipeline Gating](https://jsw.ibm.com/browse/CICDCTLG-118)                                                 | CIO developers will have the capability to proactively mitigate critical or high vulnerabilities before they reach production, in alignment with ITSS policies. | In Planning |

### Q1 2024 Issues

| Name                                                                                    | Business Value                                                                                                                                                       | Phase                                       |
| --------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------- |
| [OCP Upgrade 4.14](https://jsw.ibm.com/browse/CICDCTLG-104)                             | Our current build cluster will run on the latest version and gives our users the best performance and reliability with what we have                                  | In Planning                                 |
| [Node 20 Pipelines](https://github.ibm.com/cio-ci-cd/ciocicd-project-board/issues/1521) | Users can use Node 20                                                                                                                                                | Looking for Contributors for UI and Library |
| Critical zOS Pipeline Enhancements                                                      | Our partners for App Modernization would provide us with feature requests on the existing zos-application pipelines that ensures business continuity and reliability | In Planning                                 |
| Audit Findings                                                                          | During our Audit in Q1, we uncovered several areas of improvements such as tracking access to privileged secrets and to avoid using 1Password                        | In Progress                                 |

### Q1 2024 Completed

Epics

| Name                                                                                                                               | Business Value                                                                                     |
| ---------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| [Improve resiliency for tasks relying on third-party services](https://github.ibm.com/cio-ci-cd/ciocicd-project-board/issues/1292) | Users no longer need to manually retrigger Pipeline Runs when 3rd party APIs fail sporadically     |
| [Python Application Pipeline on zOS](https://github.ibm.com/cio-ci-cd/ciocicd-project-board/issues/1428)                           | Users can deploy their Python Applications to a zOS LPAR                                           |
| [Pipeline for Pipelines](https://github.ibm.com/cio-ci-cd/ciocicd-project-board/issues/1103)                                       | Automation for InnerSource review process will speed up review and support Cirrus Pipelines Sunset |

Tasks

| Name                                                                                                              | Business Value                                                                                  |
| ----------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| [Python 3.11 Container Pipelines](https://github.ibm.com/cio-ci-cd/ciocicd-project-board/issues/1440)             | New strategic pipelines generated with a new code gen tool                                      |
| [OpenShift 1.10 Operator Upgrade](https://github.ibm.com/cio-ci-cd/ciocicd-project-board/issues/1538)             | CIO CI/CD will run on newer versions of OpenShift Pipelines                                     |
| [Discard in-progress Check Run Task Messages](https://github.ibm.com/cio-ci-cd/ciocicd-project-board/issues/1567) | Improve reliability and stability of Check Runs by reducing reporting granularity               |
| [CentOS Stream for Install Packages Feature](https://github.ibm.com/cio-ci-cd/ciocicd-project-board/issues/1404)  | Cirrus Sunset Users can install additional packages not found in UBI                            |
| [Monitor Kafka topics/consumers](https://github.ibm.com/cio-ci-cd/ciocicd-project-board/issues/1564)              | Help CICD team identify patterns in kafka related service hiccups                               |
| [New SonarQube Instance](https://github.ibm.com/cio-ci-cd/ciocicd-project-board/issues/1427)                      | Reduce cost of multiple SQ instances at IBM; users will use a newer version of SonarQube server |


## InnerSource

To begin the contribution process, start with our docs [here](https://pages.github.ibm.com/cio-ci-cd/pipeline-catalog-docs/).

To see all InnerSource issues, view this [JIRA board](https://jsw.ibm.com/secure/RapidBoard.jspa?rapidView=57676&projectKey=CICDCTLG).

## z/OS

### Native z/OS Types

| Language  | Type                | Description                                                                                         | File Extension                       |
| --------- | ------------------- | --------------------------------------------------------------------------------------------------- | ------------------------------------ |
| ASSEMBLER | asm                 | Assember program source members                                                                     | .asm                                 |
| ASSEMBLER | asmincl             | Assembler Includes                                                                                  | .asmi                                |
| ASSEMBLER | asmmac              | Assembler Macros                                                                                    | .asmm                                |
| CICS      | bms                 | CICS Basic Mapping Services screen format files                                                     | .bms                                 |
| CICS      | config              | Configuration files (CSD - CICS System Definition files, etc.) (3)                                  | varies, normally .txt                |
| COBOL     | cobol               | COBOL program source members                                                                        | .cbl                                 |
| COBOL     | copybook            | COBOL copybooks - shared source statements and data definitions (includes COBOL DCLGEN's)           | .cpy                                 |
| Db2       | ddl                 | DB2 DDL statements                                                                                  | .ddl                                 |
| Db2       | sql                 | DB2 SQL statements executed by special processes                                                    | .sql                                 |
| IMS       | imsdbd              | IMS Database Definitions                                                                            | .asm                                 |
| IMS       | imsmap              | IMS Map definitions                                                                                 | .map                                 |
| IMS       | imspsb              | IMS Program Specification Blocks                                                                    | .asm                                 |
| ISPF      | clist               | ISPF Command List                                                                                   | .clist                               |
| ISPF      | ispm                | ISPF Message files                                                                                  | .ispm                                |
| ISPF      | ispp                | ISPF Panel definitions                                                                              | .ispp                                |
| ISPF      | isps                | ISPF Skeletons                                                                                      | .isps                                |
| ISPF      | ispt                | ISPF Tables                                                                                         | .ispt                                |
| JCL       | control             | JCL Control parms, input to programs and utilities executed in a job, such as a SYSIN DD statement. | .ctl                                 |
| JCL       | jcl                 | Executable batch JCL jobs (i.e., has job card)                                                      | .jcl                                 |
| JCL       | proc                | JCL procedure (executed by Jobs)                                                                    | .prc                                 |
| PLI       | include             | PL/I Includes - shared source and data definitions (includes PL/I DCLGEN's)                         | .inc                                 |
| PLI       | pli                 | PL/I program source members                                                                         | .pli                                 |
| REXX      | rexx                | REXX programs                                                                                       | .rexx                                |
| other     | development objects | Dev and test versions of JCL, etc. Prefix folder name with "d" or "t" (djcl, tjcl, tproc, etc).     | Varies, probably same as folder name |

## Access to Scan Results

See the following table for information about the ITSS tool capabilities and the level of information that is provided for each pipeline category.

| Pipeline Category     | SAST  | OSS Scanning | Secrets Scanning | Container Image Scanning | Artifact/Container Signing | IAST | DAST |
| --------------------- | ----- | ------------ | ---------------- | ------------------------ | -------------------------- | ---- | ---- |
| Cirrus CI/CD          | RU/AP | RG/ANP       | RG/APF           | RG/ANP                   | N/A                        | N/A  | N/A  |
| Native z/OS           | N/A   | N/A          | RG/APF           | N/A                      | TBC                        | N/A  | N/A  |
| Compliance & Security | RU/AP | RG/ANP       | RG/APF           | N/A                      | N/A                        | N/A  | N/A  |
| Library               | RU/AP | RG/ANP       | RG/APF           | N/A                      | N/A                        | N/A  | N/A  |

### Scan Result Acronyms

Results:

- RG - Results directly in GitHub application
- RU - Results through URL in GitHub application

Actions:

- AP - Action possible
- APF - Action possible by committing a file
- ANP - Action not possible

!!! note

    Valid through 2023/05/15, Cross CIO risk [RSK100023847](https://w3.ibm.com/finance/controls/wwbcit/riskEdit.wss?riskId=RSK100023847&riskTypeId=1&riskStatus=3&quarter=2022%20Q4&mode=2) acknowledges the unknown number and severity of vulnerabilities that should be remediated, regardless of your implementation approach. After 2023/05/15, teams will need to shift to their domain, sub-domain, or application-level risks for vulnerability remediation come 2Q MSAC. For false positives, where action is not possible application teams should reference the cross-CIO risk and consult their [domain risk focal](https://w3.ibm.com/w3publisher/cio-cybersecurity-assurance/matrixed-teams/contact-list) about further risk documentation.
