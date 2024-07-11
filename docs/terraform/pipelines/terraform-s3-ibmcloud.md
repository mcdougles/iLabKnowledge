## Guide: Configuring Terraform s3 backend and provider to provision resources in IBM Cloud

{%
    include-markdown "../../includes/community-supported.md"
%}

By the end of this guide, you should be able to provision IBM Cloud resources via Terraform. Your repository should be set up such that every commit triggers the following Terraform stages: init and plan, and that on merge to main, the Terraform plan is applied in your IBM Cloud infrastructure.

You can find the source pipeline in the catalog [here](https://github.ibm.com/cio-ci-cd/pipeline-catalog/tree/main/catalog/community/pipelines/terraform-s3-ibmcloud/v1).

## Step 1: Complete Checklist

Make sure the following are true before proceeding:

- You have an IBM Cloud account: <a href="https://cloud.ibm.com/docs/account?topic=account-account-getting-started" target="_blank">register for an IBM Cloud account</a>.
- You have enough access to provision resources under your IBM Cloud account: <a href="https://cloud.ibm.com/docs/account?topic=account-userroles" target="_blank">review IBM Cloud account roles</a>.

## Step 2: Terraform backend setup

The terraform implementation for this pipeline uses the s3 backend (see more details in the [official documentation](https://developer.hashicorp.com/terraform/language/settings/backends/s3)).

In order to setup the s3 backend, you need to have an IBM Cloud Object Storage instance created in your account, Please see [this page](https://cloud.ibm.com/docs/cloud-object-storage?topic=cloud-object-storage-provision&locale=es) for more details.

Following, you will need to provision a bucket within the instance to store your terraform state, learn how to provision using this [wiki](https://cloud.ibm.com/docs/cloud-object-storage?topic=cloud-object-storage-getting-started-cloud-object-storage&locale=es#gs-create-buckets).

Provision a new set of `HMAC credentials` with write permissions, using this [wiki](https://cloud.ibm.com/docs/cloud-object-storage?topic=cloud-object-storage-uhc-hmac-credentials-main&locale=es).

Finally, you need to provide these two variables in your `build.yaml` configuration:

- `aws-access-key-id`. the id of the Cloud Object Storage access key id.
- `aws-secret-access-key`. The Cloud Object Storage access key.

## Step 3: Terraform IBM Cloud provider authentication

Since this pipeline is intended to be used to provision resources in the IBM Cloud, you need to create/obtain these values from your account: `ic-api-key`, `iaas-classic-api-key` and `iaas-classic-username` as follows:


- Create or find your `ic-api-key` and `iaas-classic-api-key` [here](https://cloud.ibm.com/iam/apikeys).
  - Select **My IBM Cloud API Keys** option from view dropdown for `ic-api-key`.
  - Select **Classic Infrastructure API Keys** option from view dropdown for `iaas-classic-api-key`.
- For `iaas-classic-username`:
  - Go to **Users**.
  - Search your user and click on it.
  - Find user name in the **VPN password** section under **User Details** tab.

For more details about the IBM Cloud provider authentication, please refer to the [official provider documentation](https://registry.terraform.io/providers/IBM-Cloud/ibm/latest/docs#authentication).

## Step 4: Generate a GitHub Access Token

{%
    include-markdown "../../includes/github_access_token.md"
%}

## Step 5: Make Code Changes

1.  Create a `build.yml` file in project root with the following structure. Some of the values will be filled out later.

    ```yaml
    apiVersion: automation.cio/v1alpha1
    kind: RepositoryConfig
    ealImapNumber: xxxxxx
    build:
      strategy: simple-pipeline
      pipeline: terraform-s3-ibmcloud
      version: v1
      config:
        aws-access-key-id: xxx
        aws-secret-access-key: xxx
        ic-api-key: xxx
        iaas-classic-username: xxx
        iaas-classic-api-key: xxx
    ```

1.  Encrypt the **aws-access-key-id**, **aws-secret-access-key**, **ic-api-key**, **iaas-classic-username** and **iaas-classic-api-key** values obtained from Steps 2 and 3 above.

    Follow <a href="https://pages.github.ibm.com/cio-ci-cd/documentation/includes/credentials_encrypt/" target="_blank">this link</a> to encrypt the values and copy and paste the result into your `build.yml`.

1.  Review the `build.yml` file.

    See the following example of the build.yml file for a Terraform pipeline:

    ```yaml
    apiVersion: automation.cio/v1alpha1
    kind: RepositoryConfig
    ealImapNumber: xxxxxx
    build:
      strategy: cirrus-deployment
      pipeline: terraform-s3-ibmcloud
      version: v1
      config:
        aws-access-key-id: 'encrypted:v1:xxx'
        aws-secret-access-key: 'encrypted:v1:xxx'
        ic-api-key: 'encrypted:v1:xxx'
        iaas-classic-username: 'encrypted:v1:xxx'
        iaas-classic-api-key: 'encrypted:v1:xxx'
    ```

### Terraform minimal configuration

> **About Terraform version**:
Currently, the terraform tasks are executed using `terraform 1.5.5` (latest available), see [Dockerfile](https://github.ibm.com/cio-ci-cd/terraform-image/blob/main/Dockerfile). If you plan to adopt this pipeline, make sure your current Terraform / IBM Cloud provider versions are compatible.

Following, you can find a list of files that are required to have a minimal configuration of terraform with s3 backend setup with an
IBM Cloud Object Storage bucket, all these file must exist in the root of your Github repository.

- `backend.tf`. Provides the details about your Terraform backend such as the name, location and connection endpoint of the Cloud Object Storage bucket where the plan is going to be stored, as well as some specific values required for s3 backend to work with the IBM Cloud Object Storage offering. These values (`skip_region_validation`, `skip_credentials_validation`, `skip_metadata_api_check`) must not be changed.

  ```
  terraform {
    backend "s3" {
      bucket                      = "my-terraform-bucket"
      key                         = "my-arbitrary-terraform-state-location-path/terraform.tfstate"
      region                      = "us-south" # set the location of your bucket
      skip_region_validation      = true
      skip_credentials_validation = true
      skip_metadata_api_check     = true
      endpoint                    = "s3.us.cloud-object-storage.appdomain.cloud" # set the corresponding endpoint for your bucket's region
    }
  }
  ```

- `provider.tf`. The Terraform provider plugin to be able to provision resources in the IBM Cloud.

  ```
  terraform {
    required_providers {
      ibm = {
        source  = "IBM-Cloud/ibm"
        version = "1.54.0"
      }
      ...
    }
  }
  provider "ibm" {
    region = "us-south"
  }
  ...
  ```

- `resources.tf`. A file containing your terraform resource definitions.

  ```
  resource "ibm_resource_group" "cio-ci-cd-test-resource-group" {
    name = "CIO-CI-CD-TEST-RESOURCE-GROUP"
  }
  ```

  **NOTE:** for sake of simplicity, we have only provided a single file as example, however you are not limited to a single one and can organize your resources as needed. Please bear in mind that as for the current pipeline version, ALL `*.tf` files MUST remain in the root of your repository, which will become the Terraform working directory once `terraform init` is executed, otherwise they will not be considered for the subsequent `terraform plan / apply` execution, this because terraform does not recursively look for files outside the current working directory.

You can find an example repository [here](https://github.ibm.com/cio-ci-cd-test/terraform-test).

## Step 6: Push Code and Wait for Build

{%
  include-markdown "../../includes/push-code-wait.md"
%}

{%
  include-markdown "../../includes/contact_us.md"
%}