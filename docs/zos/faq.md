This page contains common questions and issues reported using CIO CI/CD for z/OS apps

## Frequently Asked Questions

??? question "Timeline and Deadlines"
    <a id='timelines' href='#timelines'>Link to this section</a>

    **Q: Is there an official deadline for migrating all z/OS repositories to the CI/CD pipeline? What will happen if I can't migrate my application by the deadline set by ITSS?**
    
    According to ITSS Chapter 5, the official deadline for the migration is the end of June. However, it's important to note that GitHub has been a requirement for some time, with a temporary exception granted due to the Lift and Shift requirements. If your application cannot be migrated by the end of June, you will need to follow the process for a Risk as documented by the BISO organization. This process will include submitting a plan for migration and addressing the risk associated with not meeting the deadline.     
    
    
    **Q: When will the CI/CD pipeline be production-ready for z/OS?**  
    
    The CI/CD pipeline is already operational in production for some applications.  

??? question "Sunset"
    <a id='sunset' href='#sunset'>Link to this section</a>

    **Q: Considering legacy applications on a sunset path that undergo minimal changes, is there a significant benefit to migrating the code to CIO CI/CD given the amount of work involved?**

    If the application is scheduled for sunset this year, the only requirement is to archive the final code in GitHub. It's not necessary to focus on organizing the code extensively in GitHub; simply placing the contents in folders based on type is sufficient, without worrying about the number of files in each folder.   
    
    However, if the application is not sunset-bound this year, it must meet ITSS requirements. ITSS mandates that source code be stored in GitHub and comply with secure build processes, build signing, and other auditing requirements. Moving to the CI/CD pipeline satisfies ITSS requirements. While migrating does involve work, it brings benefits such as onboarding into ADDI, which provides improved visibility into the application. Additionally, all applications must be moved into Cirrus 2.0 within the next two years. Managing custom-built LPARs becomes impractical, and onboarding into the pipeline is necessary to transition to Cirrus 2.0.

??? question "Tools and Infrastructure"
    <a id='tools' href='#tools'>Link to this section</a>

    **Q: Who is responsible for installing and maintaining the required tools, such as Python, zoau, wheelhouse, Git, DBB, on the z/OS server? Is it the responsibility of the z/OS team or the Applications teams?**

    We are working with the infrastructure team to establish a process where they will be responsible for installing and maintaining the required base tools. If these tools are not present on your z/OS system, you will need to create a ticket with the support team to have them installed. 
    The objective is to have infrastructure team keeping these tools up to date as part of their standard maintenance process. We do not want the application teams to be burdened with this task. Additionally, we aim to have a single version of each tool on the systems to avoid multiple versions and ensure that the right tools are present on the appropriate systems.
    The documentation will be updated to include the instructions.
    

    **Q: Is it true that installing tools like Python, zoau, wheelhouse, Git, DBB by the Applications teams is out of compliance?**

    No, that information is not true. If the application teams install these tools as part of the pipeline, it is within compliance. However, it is important to ensure that the installed tools are kept up to date with patches. To reduce the workload on the application teams, we encourage the infrastructure team to handle the installation and maintenance of these tools. The applications are not considered out of compliance.
        

    **Q: Is the infrastructure available on the z/OS Legacy systems?**

    Yes, the infrastructure is available on the z/OS Legacy systems. However, if you are already using the pipeline, it is necessary to migrate to a Cirrus 2.0 system.

    

    **Q: In an environment with multiple applications, would each application need to perform its own installation on the z/OS side, or can they share the same structure?**

    Once the infrastructure team handles the installation, it will be done in one location that all teams can utilize. Therefore, there is no need for each application to perform separate installations.
    

    **Q: When using IDz or any other IDE, will the same set of tools installed for the development CI/CD pipeline be used? Are there any special setup requirements, considering that we will be using our personal user ID which may not have the tools installed under it?**

    Both IDz and [VS Code](https://code.visualstudio.com/){target=\_blank} with the Z Open Editor utilize the existing tools. You will need to ensure that your configuration points to the correct location. Since the tools will be installed and accessible to all users, there is no need to be concerned about your personal user ID using them.

    
    **Q: We have multiple applications in our libraries and use SCLM. Will we need to create a separate repository for each application?**

    Yes, you will need to separate the applications into individual repositories. If you have shared includes, it would be beneficial to separate them into their own repositories, owned by the appropriate team, while still being usable by other application teams as part of the build process.
    

    **Q: Is local compilation on IDz (e.g., PL/I code) supported?**

    Local actual compilation has not been supported in IDz for some time. Instead, IDz provides a user build function that copies the source to the host and compiles it using the true z/OS compiler.


    **Q: In our SCLM setup, we use multiple streams to manage different priority projects and utilize multiple test environments. What is the equivalent of this in Git?**

    In Git, branches are used to handle multiple changes. Git provides greater flexibility in managing differences and improved merge capabilities compared to SCLM. However, it's important to note that if multiple users change the same line of code, a merge conflict will still occur and need to be resolved.



    **Q: Can we have two test environments between the development and production environments (e.g., DEV -> Test -> Business Test -> Prod)?**

    Yes, you can have as many environments as needed. In Cirrus 2.0, the system is designed to accommodate up to four levels of environments easily. If additional environments are required, automation can be implemented to create them.
    


    **Q: How can we use IDz in the CI/CD Cirrus 2.0 for our IMS-based project?**

    You should be able to use IDz in your current environment. If you encounter any issues with a specific LPAR, please let us know in the Slack Channel, and we will coordinate with the infrastructure team to address it. While there may be some additional time required for the lift and shift process, our goal is to ensure that teams can be as productive as possible.



    **Q: How do we update the IDz License on a M1 or M2 Mac?**

    You currenly can't apply a new license on IDz installed using P2 on an M1 or M2 Mac.  
    First navigate to \~/var/ibm/p2/license and delete the LUM directory. Then clean unzip IDz again (don't use the prior unzipped application), launch, and you can use the same workspace as before - this will get you into a clean state and restart the 90-day trial. This is **not** meant to be a true workaround, and more of a debugging procedure for licensing and IDz. *But* will get you into a working state again.


    **Q: How can we check the free space on the file system in Unix System Services.**

    To check the free space on the current directory issue `df -mPv .`.  
    To check the free space on a specific directory issue `df -mPv ~/data`.

??? question "Code Page Concerns"
    <a id='codepage' href='#codepage'>Link to this section</a>

    **Q: What needs to be in the .profile to avoid EBCDIC to ASCII to EBCDIC issues?**

    The `.profile` needs to set `\_BPXK_AUTOCVT=ON` for the tagging to work.  If it does not have that env var exported, it won't tell z/OS to read the file based on its tag.the `.profile` there is a chicken and egg situation.  You need to have `\_BPXK_AUTOCVT=ON` set in order to read it, but you can't set `\_BPXK_AUTOCVT=ON` in the `.profile` if it is not readable.  The way around this is you need the file to be in EBCDIC not ASCII.   
    Now this is where things gets a bit tricky.  On a lot of systems we interact with the code page for EBCDIC is `IBM-1047`, but for others it can be very different.  In Japan for example, they would have a VERY different code page than `IBM-1047`.  
    The .profile should NOT be transferred over sftp.  I would recommend you used scp instead.

    **Q: Can text files be transferred via FTP to z/OS from a laptop?**

    Use `scp` to transfer files and the conversion will be handled correctly.

    **Q: How do we handle non-roundtrippable characters in PL/1?**

    There are several recordings on how to handle non-roundtrippable characters in PL/1.  Here are links to them: [Video 1](https://secure.video.ibm.com/channel/25235794/video/133306740), [Video 2](https://secure.video.ibm.com/channel/25235794/video/133305660), [Video 3](https://secure.video.ibm.com/channel/25235794/video/133121304) .

??? question "Git and GitHub "
    <a id='git' href='#git'>Link to this section</a>

    **Q: What does the term "branch" mean in Git?**

    In Git, a branch refers to a logical version of a set of files. When you clone or pull a repository, you retrieve the entire repository, including all branches. (There are ways to limit this, but in the simplest and most common case, you get all branches.) All branches will be available in your local copy. However, it's important to note that Git does not recommend creating branches to represent traditional levels or environments. Branching models can vary, and for more information, we suggest referring to resources like this:  [https://www.atlassian.com/git/tutorials/using-branches](https://www.atlassian.com/git/tutorials/using-branches){target=\_blank} 

    
    **Q: We have multiple projects supporting one application with different delivery dates and multiple stages (e.g., dev, test). How do we go about with this kind of setup?**

    Working with Git makes it easier to track multiple projects simultaneously with different changes, but the process is different when using branches. We recommend reading more about branching models and their implications for managing multiple projects: [https://www.atlassian.com/git/tutorials/using-branches](https://www.atlassian.com/git/tutorials/using-branches){target=\_blank} 

    

    **Q: Why is an encrypted key needed when we can connect z/OS and GitHub using an SSH public key?**

    The encrypted private key used for the SSH connection needs to be stored in the GitHub repository. If an unencrypted version were used, anyone with read access to the GitHub repo would be able to make changes to your environments. Encryption adds an extra layer of security to protect sensitive information.

    

    **Q: Can GitHub be integrated with change management tools like Jira?**

    Yes, GitHub offers integrations with various tools, including Jira. You can explore the available GitHub Enterprise integrations specific to IBM here:  [https://w3.ibm.com/#/support/article/github_ent_ibm/github_migrate?_lang=en](https://w3.ibm.com/#/support/article/github_ent_ibm/github_migrate?_lang=en){target=\_blank} 

    For information on Jira integrations, you can refer to this resource:  [https://w3.ibm.com/#/support/article/jira_at_ibm](https://w3.ibm.com/#/support/article/jira_at_ibm){target=\_blank} 

    Additionally, the GitHub documentation provides guidance on connecting a GitHub Enterprise Server account to Jira Software:  [https://support.atlassian.com/jira-cloud-administration/docs/connect-a-github-enterprise-server-account-to-jira-software/](https://support.atlassian.com/jira-cloud-administration/docs/connect-a-github-enterprise-server-account-to-jira-software/){target=\_blank} 


    **Q: My home directory is not the default when doing `git clone`.**

    Add `export HOME=/D0@@/u/cuscucd/` at the top of your `.profile` for the CI/CD userid.



    **Q:  My SSL certificate is not found when doing a `git clone`.**

    Set this variable in your `.profile` - `GIT_SSL_AINFO=/u/gend001/cert.pem`.

    **Q:  Is there a video that explains the git commands?**

    This Lightning Talk has a good explaination of the [most commonly used git commands](https://secure.video.ibm.com/channel/25235794/video/133305664).

    **Q:  What is Trunk-Based Development?**

    This Lightning Talk has a good explaination of [Trunk-Based Development](https://secure.video.ibm.com/channel/25235794/video/133121220).
    
??? question "Supported Languages and Middleware"
    <a id='supportedlanguages' href='#supportedlanguages'>Link to this section</a>

    **Q: Is the Rexx language included in the CI/CD pipeline? How will it be handled?**

    Yes,  Rexx is supported in the CI/CD pipeline. Both Interpreted Rexx and compiled Rexx can be handled in the pipeline. Basic configuration is required for all languages in the pipeline, including Rexx. However, it is not complicated and is part of the initial setup process.
    

    **Q: Is CI/CD ready to work with IMS applications?**

    There are no restrictions on the old LPARs for what is supported in the pipeline. For legacy LPARs or redeploy LPARs, the full deployment process is not being replaced or automated due to their variations. The existing deployment process will be utilized, and the pipeline will handle the movement of load modules and bindings. If there are additional REXX or other automation for deployment, they can be integrated into the pipeline. Work is being done to support new world IMS deployment in Cirrus 2.0.
    

    **Q: How will the pipeline support middleware upgrades?**

    In Cirrus 2.0, there are multiple levels of pipelines. From an application perspective, the application pipeline will be responsible for making the appropriate middleware updates specific to the application. However, it will not upgrade the middleware itself. The application pipeline can target an updated version of the middleware, but the actual upgrades of middleware and operating system changes will be handled by the infrastructure pipeline.


??? question "Developer Tasks and Processes"
    <a id='developertasks' href='#developertasks'>Link to this section</a>

    **Q: Can we re-run a specific failed step of the pipeline and resume from there?**

    The pipeline is designed for users to compile and test their code using User Build first. Failures in the pipeline are typically related to failed tests, which need to be fixed before rerunning the pipeline. Currently, the pipeline is relatively quick due to limited automated testing. As testing increases, it will be beneficial to address all failed tests together to improve efficiency.

    
    **Q: Do developers need to individually clone the repository and install applications in z/OS?**

    There are two aspects to consider: the migration into the pipeline and the day-to-day activities. When it comes to the migration into the pipeline, tools on z/OS need to be installed, and this task can be performed by one of the developers. However, for day-to-day activities, each developer simply needs to clone the Git repository locally and set up their preferred IDE.
    

    **Q: How can we back out a change? Do we need to rebuild and redeploy to override the last change?**

    Backing out a change may be necessary in some cases, and the backout process needs to be defined in collaboration with the teams. Wazi Deploy can create a backout package that allows rolling back to the state before the change. However, it is generally better to roll forward by making a new change and pushing it through the pipeline. This approach becomes preferable as teams adopt more automated testing. In the pipeline, building is not necessary beyond the development environment. The release pipeline is used to deploy an already built package to the respective environments.
    

    **Q: My application has the development environment on one LPAR and Production on another LPAR. In that case, do I need to configure CI/CD twice? In both DEV and PROD servers? How does deployment from DEV to PROD work?**

    Configuration of the process is done within the repository. You will have build.yml and multiple deploy.yml files to define the expected runtime environments. The build-related tools should be set up in the development environment. The pipeline will build the required artifacts, store the package in the artifact repository, and deploy them to the defined development environment.
    There is a secondary pipeline used for deploying to later stages, which utilizes the artifacts created by the initial build. It deploys the artifacts to subsequent test and production environments. If any issues arise, you can go back to the initial pipeline to create a new package.

    Please refer to the documentation for more details:   [https://pages.github.ibm.com/cio-ci-cd/documentation/zos/concepts/workflow/](https://pages.github.ibm.com/cio-ci-cd/documentation/zos/concepts/workflow/){target=\_blank}

    Deployment-related tools will indeed need to be present in the later stage environments. Our collaboration with the infrastructure team ensures that the appropriate tools are available in the respective environments.


??? question "Testing and Automation"
    <a id='testing' href='#testing'>Link to this section</a>

    **Q: Are there any automated testing plans included in the z/OS CI/CD?**

    A: Yes, development teams should aim to create appropriate automated testing as part of the pipeline. The testing process includes unit testing using ZUnit in IDz (currently supporting COBOL, not PL/I), Virtual Test Platform (VTP) for stubbed testing, and functional testing through System and Integration testing using Galasa. You can learn more about it at [galasa.dev](https://galasa.dev){target=\_blank}


    **Q: If we onboard an application onto CI/CD, do testing activities need to be conducted on the Mainframe Kernel itself? Can development and deployment be handled through CI/CD?**

    The CI/CD pipeline performs all the necessary tasks on z/OS directly. It uses SSH to connect to the z/OS environment, pulls the source code from Git, and executes the build process. When testing is incorporated, it also runs on the z/OS environment.

    **Q: Are there any presentations on what tools are available for testing on z?**

    There have been 3 presentations: 
    
    - [Overview of z Testing Strategy](https://ec.yourlearning.ibm.com/w3/playback/10404918)
    - [Deep Dive on IBM DevOps Test and Virtualization](https://ec.yourlearning.ibm.com/w3/playback/10420114)
    - [Deep Dive on Galasa](https://ec.yourlearning.ibm.com/w3/playback/10424116)


??? question "Security Scanning"
    <a id='security' href='#security'>Link to this section</a>

    **Q: How can I check the inventory of open-source libraries used by an application?**

    In traditional z/OS languages like PL/I, there are no open-source libraries used, so this does not apply. The detection of secrets process is run on traditional languages.  

    For languages such as Java, where open-source packages are used, the CIO CI/CD pipeline handles the scanning requirements. It will continue to adapt and meet the requirements from ITSS to ensure proper handling of open-source libraries.


??? question "New Ways of Working and Productivity"
    <a id='newways' href='#newways'>Link to this section</a>

    **Q: How does the productivity of the CI/CD pipeline compare to traditional z/OS code deployment methods like SCLM and TRANSF?**

    It's challenging to provide a direct comparison in terms of productivity as the ways of working are completely changing. While developers may feel that creating automated tests adds more work initially, the pipeline eliminates manual steps and the need for someone else to handle the deployment process.

    Transitioning to IDz or [VS Code](https://code.visualstudio.com/){target=\_blank} alone has already shown a 25% increase in productivity externally. Clients have experienced significant reductions in the time required to make changes, going from months to days. Some organizations even deploy functional changes to production multiple times a day.


??? question "Successful Migration and Early Adopters"
    <a id='migration' href='#migration'>Link to this section</a>

    **Q: How many z/OS repositories have been migrated from SCLM to the CI/CD pipeline?**

    Currently, within the CIO, we have three applications in Cirrus 2.0, indicating successful onboarding and transition to the new environment. Additionally, there are other teams actively working on implementing the pipeline in their existing environments.

    In customer environments, we have observed numerous clients who have migrated their entire application sets to the CI/CD pipeline. These clients have recognized significant value in the migration, even for applications with minimal or no ongoing changes.


??? question "Updates and Notifications"
    <a id='updates' href='#updates'>Link to this section</a>

    **Q: How are changes to Plum, DBB, templates, and documentation communicated to everyone?**

    The standard versions of Wazi Deploy (Plum) and DBB will be maintained, and the application configurations will be stored in the associated Git repository. If there are compatibility issues that require multiple versions, we will address them as they arise.

    The infrastructure team is responsible for managing software updates, ensuring they are non-impacting. If an update is expected to have an impact, we will inform teams and provide options to run parallel versions, allowing for a smooth migration.

    To stay informed about updates, please monitor the  [#cio-zos-ci-cd-support](https://ibm-cio.slack.com/archives/C034Y7URH97){target=\_blank} and subscribe to the [CIO DX Advocacy for z/OS Blog](https://w3.ibm.com/w3publisher/cio_developer_advocacy/cio-dx-advocacy-for-zos){target=\_blank}. We will provide updates to documentation, processes, and any significant software changes through these channels.
    

??? question "Feature Requests and Contributions"
    <a id='requests' href='#requests'>Link to this section</a>

    **Q: How can a team request features for the CI/CD pipeline?**

    To request new features, teams can use the dedicated Slack channel: [#cio-zos-ci-cd-support](https://ibm-cio.slack.com/archives/C034Y7URH97){target=\_blank}. This channel serves as a platform for communicating feature requests and discussing enhancements.

    
    **Q: If I'm interested in understanding the inner workings of the pipeline, is there a way to access that information?**

    We welcome InnerSource contributions for the pipeline. To gain access to more in-depth information about the pipeline's functionality, please reach out to the team via the ([#cio-zos-ci-cd-support](https://ibm-cio.slack.com/archives/C034Y7URH97){target=\_blank}) Slack channel.


??? question "Future Implementations"
    <a id='future' href='#future'>Link to this section</a>

    **Q: Is there a possibility that a CIO Z pipeline will be developed for z/VM?**

    Developing a pipeline for z/VM poses some challenges as there is no inherent Unix environment to port Git to, unlike in z/OS. However, we are actively exploring options and working with teams to determine the most suitable approach.

??? question "Upgrading the pipeline"
    <a id='upgrade' href='#upgrade'>Link to this section</a>

    **Q: How do I upgrade from v2 of the zos-application pipeline to v3?**

    Ensure $HOME is set in your .profile file per the instructions in the [Repository Setup](./migration/repository-setup.md#configure-home-in-profile) page.

    Change the `version` parameter (under `build`) in the build.yml file to `v3` for all of your repositories.  

??? question "Education"
    <a id='education' href='#education'>Link to this section</a>

    **Q: Where can we find education on the steps needed to implement the pipeline?**

    We have recordings from the [CIO DX Workshop for z/OS](https://w3.ibm.com/w3publisher/cio-dx-workshop-for-z-os/schedule-and-syllabus) you can watch.

    **Q: Where can we find education on the PL/1 Pipeline setup and troubleshooting?**

    We have a recording of a Lightning Talk about [PL/1 Pipeline Setup](https://secure.video.ibm.com/channel/25235794/video/133306739) and a [copy of the steps](https://ibm.ent.box.com/s/c1y6rp2we6l0w194k2wom5dbkdl4amj3).

    **Q:  Is there a video that explains the git commands?**

    This Lightning Talk from Cohort 3 has a good explaination of the [most commonly used git commands](https://secure.video.ibm.com/channel/25235794/video/133305664).

    **Q: How do we handle non-roundtrippable characters in PL/1?**

    There are several recordings on how to handle non-roundtrippable characters in PL/1.  Here are links to them: [Video 1](https://secure.video.ibm.com/channel/25235794/video/133306740), [Video 2](https://secure.video.ibm.com/channel/25235794/video/133305660), [Video 3](https://secure.video.ibm.com/channel/25235794/video/133121304) .
