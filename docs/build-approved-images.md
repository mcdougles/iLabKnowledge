{%
    include-markdown "./includes/build-approved-images-warning.md"
%}

{%
    include-markdown "./includes/build-approved-images-context.md"
%}

# Guide: Building a Custom Image to Deploy to Cirrus
By the end of this guide, you should be able to build and push your custom image to Cirrus. Your repository in [cio-ci-cd-image-catalog](https://github.ibm.com/cio-ci-cd-image-catalog) should be set up such that every commit may trigger a build and push. {% include-markdown "./includes/contact_us.md" %}

## Step 1: Complete Checklist

Make sure the following are true before proceeding:

- If a request for the custom image does not already exist, you must submit one using the [template provided](https://github.ibm.com/cio-ci-cd-image-catalog/requests/issues/new?assignees=&labels=&template=container-image-request.md&title=). Ensure all sections of the template have been filled out in detail.
- The image request has been approved by the CIO CI/CD Team.
- The CIO CI/CD Team has created a new repository in [cio-ci-cd-image-catalog](https://github.ibm.com/cio-ci-cd-image-catalog) based on your request

    The **CIO CI/CD Team will** configure the `build.yml` in your repository with the following:

    - `cirrus-project-name`: The Project name that will be used to push your image to Cirrus.

    - `cirrus-pipeline-name`: The Pipeline name that will be used to push your image to Cirrus.

    - `cirrus-pipeline-username`: The encrypted Pipeline username that will be used to push your image to Cirrus.

    - `cirrus-pipeline-password`: The encrypted Pipeline password that will be used to push your image to Cirrus.

    To promote re-usability, custom images are stored in shared image registry pipelines within a dedicated Cirrus project. The images are accessible by all prospective users.

## Step 2: Make Code Changes

1. A member of the CI/CD team will create a new repository for the custom image. The repository will be based off the [image template](https://github.ibm.com/cio-ci-cd-image-catalog/image-template), which contains the necessary files for successfully deploying your custom image.


1. Make necessary changes to your [Dockerfile](https://github.ibm.com/cio-ci-cd-image-catalog/image-template/blob/main/Dockerfile). All base images are required to be [RedHat UBI Base Images](https://catalog.redhat.com/software/base-images).

## Step 3: Push Code and Wait for Build

{%
    include-markdown "./includes/push-code-wait.md"
%}

## Step 4: Custom Image on Cirrus

Once the build has successfully completed, your custom image will be pushed to a Cirrus image registry pipeline. You can then pull the image using your preferred container runtime tool, following the format: `registry.cirrus.ibm.com/<cirrus-project-name>/<cirrus-pipeline-name>:<version-tag>`. The `version-tag` is the tag result from the logs in `git-version` task from the [Pipeline](https://github.ibm.com/cio-ci-cd/python-build-image/runs/40528209)

Once you can successfully pull the image, you are ready to move on to [Deploying the Approved Image](./deploy-approved-images.md).
