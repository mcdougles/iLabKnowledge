Optional: If your team uses [Jira](https://jsw.ibm.com){:target="_blank"}, you can add the parameter `jiraProjectName: PROJECTKEY` to your build.yml. It facilitates DevSecOps metrics tracking using the IMAP number to link Jira to your application.  The DevSecOps Jira dashboard is based on a feed from existing Jira projects. The `PROJECTKEY` is the common prefix shared by every issue in the project. For example, `CIOCICD` is the `PROJECTKEY` for the issue `CIOCICD-100`.  For more information, see the CIO DX&HFRI [DevOps Industry Benchmarks page](https://w3.ibm.com/w3publisher/dxhrfi/skills-guilds/devops-agile/industry-benchmark).
```yaml
apiVersion: automation.cio/v1alpha1
kind: RepositoryConfig
ealImapNumber: xxxxxx
jiraProjectName: <PROJECT-KEY>
```
