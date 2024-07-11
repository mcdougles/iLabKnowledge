# Security Testing Performed

The CIO CI/CD Pipeline performs scans for every push to GitHub Enterprise. This includes test releases, feature branches, as well as production releases. These scans are performed after successful completion of prerequisite tasks.

## Static Code Scanning

The container and compliance pipelines uses SonarQube Enterprise to scan application code.

??? question "When is this Scan Performed?"

    This scan is included in container pipelines and compliance pipelines. In the container pipelines, this scan is performed before building the application image. In compliance pipelines, this scan is performed after dependencies are installed.

??? question "How Do These Results Affect Future Activities?"

    The results from the SonarQube scan can be reviewed on the SonarQube UI. There are plans to integrate the results of the scan with the Developer Portal project, so teams would be able to review their results there as well.

    If a scan was not able to be performed for any reason (for example, if SonarQube is down), the pipeline will emit a warning in the logs and continue execution.

    Currently, the pipeline does not fail execution due to the results of a SonarQube scan.

??? question "How do I access scan results and take action on them?"

    **Accessing scan results:**

    In [My Developer Portal](https://cirrus.ibm.com/developer/){target=\_blank}, Static Code Scan vulnerabilities are viewable under the **Security**, then **SAST** tab for your Application or Repository. The Portal will list SonarQube vulnerabilities and “Hot Spots”. Additional information about each issue can be viewed by following the link under each vulnerability, which will bring you to the [SonarQube Enterprise portal](https://sonarqube.us-south-k8s.intranet.ibm.com/projects){target=\_blank}.

    **Taking action:**

    - Scan results should be triaged directly in the [SonarQube Enterprise portal](https://sonarqube.us-south-k8s.intranet.ibm.com/projects){target=\_blank}. 
    
    - Triage updates are not reflected in My Developer Portal until the next scan.

    - See additional CIO guidance for ITSS Ch. 5.2.4: [Respond to Vulnerabilities](https://pages.github.ibm.com/ciso-psg/main/standards/itss.html#20-security-by-design-principles){target=\_blank}. 

    Note: Not applicable for Native z/OS applications at this time.

#### Other Details

- [SonarQube UI](https://sonarqube.us-south-k8s.intranet.ibm.com/projects)

## Open-Source Vulnerability and Dependency Code Scans

The container and compliance pipelines use Mend to scan for open-source vulnerabilities.

??? question "When is this Scan Performed?"

    A dependency scan is performed on every push, immediately following the clone or dependency install step, depending on the pipeline being used.

??? question "How Do These Results Affect Future Activities?"

    The results from the Mend scan are reviewable on the Developer Portal website (currently in beta). The results of the scan do not prevent the pipeline from continuing execution.

??? question "How do I access scan results and take action on them?"

    **Accessing scan results:** 

    In [My Developer Portal](https://cirrus.ibm.com/developer/){target=\_blank}, open-source vulnerabilities are viewable under the Security, then open-source tab for your Application o Repository.

    **Taking action:**

    - Vulnerable dependencies must be updated where possible

    - Teams must document vulnerable dependencies until they are remediated

    - Dependencies that are EOL should be replaced with equivalents.

    - See additional CIO guidance for ITSS Ch. 5.2.4: [Respond to Vulnerabilities](https://pages.github.ibm.com/ciso-psg/main/standards/itss.html#20-security-by-design-principles){target=\_blank}.  

    Note: Not applicable for Native z/OS pipeline.

#### Other Details

- [Mend UI](https://ibmets.whitesourcesoftware.com/Wss/WSS.html#!home)
- [Developer Portal](https://cirrus.ibm.com/developer)

## Scans for Secrets/Credentials Accidentally Included in Code 

All pipelines run Detect Secrets to scan for any secrets included in code. Teams should scan/audit any secrets prior to pushing.

??? question "When is this Scan Performed?"

    A scan is performed on every pipeline execution immediately following clone. If a team does not have a `.secrets.baseline` file included in their repository, the Detect Secrets pipeline task will fail. This will cause the pipeline to fail, preventing builds from proceeding until a secrets baseline file has been added.

??? question "How Do These Results Affect Future Activities?"

    If the scan results in any found secrets or any potential secrets that have not been audited yet, the scan will fail. This will cause the pipeline to also fail, preventing builds until the scan results are addressed in some way.

??? question "How do I access and action on scan results?"

    **Accessing scan results:** 

    In [My Developer Portal](https://cirrus.ibm.com/developer/){target=\_blank}, secrets detection results are viewable under the Pipeline Metrics tab for an Application or Repository, then View Details for a specific pipeline run, then viewing the logs for the detect-secrets task. Additionally, under the Deployments tab for and Application or Repository, a link is provided to the pipeline run details for each deployed version. 

    **Taking action:**

    - Immediately invalidate any secrets that have been inadvertently committed-

    - Audit probable secret strings and commit a `.secrets-baseline` file to the root of repository, following instructions in [Detect Secrets setup guide](https://w3.ibm.com/w3publisher/detect-secrets){target=\_blank}.

    - See additional CIO guidance for ITSS Ch. 5.2.4: [Respond to Vulnerabilities](https://pages.github.ibm.com/ciso-psg/main/standards/itss.html#20-security-by-design-principles){target=\_blank}. 

#### Other Details
- [Detect Secrets](https://w3.ibm.com/w3publisher/detect-secrets)

## System/image/container Vulnerability Scans 
All pipelines that produce an image use Twistlock to scan that image for vulnerabilities.

??? question "When is this Scan Performed?"

    A scan is performed for every image that is built by the pipeline. The results of the image are formatted into logs to be displayed as results from the pipeline. In addition, the results are transmitted to the Developer Data Lake, for future integration into the Developer Portal.

??? question "How Do These Results Affect Future Activities?"

    Teams can review the vulnerabilities reported by this task in their pipeline results. This scan does not stop pipeline execution due to any vulnerability discovered during the scan.

??? question "What Architectures are Supported?"

    According to [Container Security External Docs](https://pages.github.ibm.com/Supply-Chain-Security/ConSec-External-Docs/platformsupport/), only
    x86 architectures images can be scanned as of June 2023.

??? question "How do I access and action on scan results?"

    **Accessing scan results:** 

    In [My Developer Portal](https://cirrus.ibm.com/developer/){target=\_blank}, open-source vulnerabilities are viewable under the **Security**, then **Container** tab for your Application or Repository.

    **Taking action:**

    - Vulnerable dependencies must be updated where possible.

    - Teams must document vulnerable dependencies until they are remediated.

    - Dependencies that are EOL should be replaced with equivalents.

    - See additional CIO guidance for ITSS Ch. 5.2.4: [Respond to Vulnerabilities](https://pages.github.ibm.com/ciso-psg/main/standards/itss.html#20-security-by-design-principles){target=\_blank}. 

    Note: Not applicable for Native z/OS, Compliance & Security, and Library pipelines.

#### Other Details

- [Twistlock](https://w3.ibm.com/w3publisher/cio-cybersecurity-assurance/services/vulnerability-patch-management/twistlock)

## Security Test the Final End-to-end Application
Our pipelines do not currently include a task for testing the security of the end-to-end application. We have plans to add this in the future.

??? question "How do I access and action on scan results?"

    **Accessing scan results:** 

    [My Developer Portal](https://cirrus.ibm.com/developer/){target=\_blank}:  N/A

    Contrast IAST: Full detailed scan results are available in the [Contrast UI](https://app.contrastsecurity.com/). If you have not registered for Contrast, visit [Contrast page](./contrast.md) in this documentation.

    **Actioning on scan results:**

    - Scan results should be triaged directly in the Contrast UI.

    - See additional CIO guidance for ITSS Ch. 5.2.4: [Respond to Vulnerabilities](https://pages.github.ibm.com/ciso-psg/main/standards/itss.html#20-security-by-design-principles){target=\_blank}.  

- [Roadmap](https://pages.github.ibm.com/cio-ci-cd/documentation/features-roadmap/)

## Artifact/Container Signing

??? question "How do I access Cosign results?"

    In [My Developer Portal](https://cirrus.ibm.com/developer/){target=\_blank}, logs are viewable under the **Pipeline Metrics** tab for an Application or Repository, then View Details for a specific pipeline run, then viewing the logs for the **cosign-sign-app-image** task.

    Note: Not applicable for Cirrus CI/CD, Compliance & Security, and Library pipelines.
