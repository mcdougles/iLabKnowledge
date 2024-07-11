# Overview

This page describes the multi-region support for CIO CI/CD pipelines that deploy to Cirrus. By following this guide, you will learn how to configure your project to deploy to multiple Cirrus regions during CD.

## Deployment Method

The pipelines use the instant deployment method, where all regions are deployed to simultaneously. If a deployment fails to any region, then the deploy task of the pipeline also fails. The deploy task only succeeds when all regions succeed, and will be followed by the verification task which deploys and verifies the end-to-end test job on each region.  All regions must pass the verification test in order for the verification task to succeed.

### Load Balancers

Load balancer and Ingress automation are not currently supported.

### Rollback

Rollback are not currently supported by the pipelines and therefore users should have a well-defined rollback plan in place to revert undesired changes.

### Feature Branches

The pipelines do not deploy to multiple regions on feature branch deployments to the test environment. Only the first named region in the `cirrus-region` param will be used when deploying the the test environment. 

## Configuration

### The Repository Config

To configure the repository for multi-region deployment, update the `cirrus-region` config param in the build.yml with each desired region separated by a space. For example to deploy to both the regions us-south2 and us-east1, configure the param as shown in this partial example of a build.yml:

```yaml
apiVersion: automation.cio/v1alpha1
kind: RepositoryConfig
ealImapNumber: xxxxxx
build:
  config:
    cirrus-region: us-south2 us-east1
```

### Supported Regions

The following regions are valid values supported by the `cirrus-region` config param:

- `us-south2`
- `us-east1`
- `ash-cpc`

### Custom Deployment Files

See [Deployment Customization - The Deploy Resources Directory](./../cirrus-deployment-customization/#multi-region) for instructions on how to create region specific deployment resource files.