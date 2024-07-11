
!!! info

    As of March 1, 2023 [TaaS Artifactory](https://taas.cloud.ibm.com/tools/artifactory) is deprecating API keys. New API keys cannot be generated after this date and all existing API keys will be revoked by April 4, 2023. Going forward, follow the [Artifactory Authentication Access Tokens Guide at taas.cloud.ibm.com](https://taas.cloud.ibm.com/guides/artifactory-authentication-access-tokens.md) to generate an **Access Token** for use with CIO CI/CD. Access Tokens have an expiration date (up to 90 days), so you will need to re-generate the Access Tokens periodically.

    Access Tokens used for CIO CI/CD should be generated using the REST API with a scope of `audience=jfrt@*` so that they can only be used for Artifactory.

    For additional information and support on TaaS Artifactory, visit [https://taas.cloud.ibm.com](https://taas.cloud.ibm.com).
