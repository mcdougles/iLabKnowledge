# Build Versioning Strategy

The CI/CD pipeline currently only supports [Semantic Versioning specification](https://semver.org/). 

The semantic version format is 'MAJOR.MINOR.PATCH' (e.g. `1.0.0`).

The following process describes how the pipeline obtains the **current version**:

- The project version is obtained from the project configuration file (e.g. pom.xml, package.json) and must be in the semantic version format.
- The latest Git Tag version is obtained by taking the greatest semantic version value between all Git Tags on the current branch, if any.
- The greater semantic version value between the project version and the latest Git Tag version is used as the current version.

The following process describes how the pipeline calculates the new **build version** from the current version:

- If the pipeline is triggered from a default(main) branch, the build version is calculated by incrementing the current PATCH version by 1.
  MAJOR.MINOR.PATCH+1
  For example: 1.0.26
- If the pipeline is triggered from a non-default(feature) branch, then the git commit hash is suffixed to the current PATCH version.
  MAJOR.MINOR.PATCH-rGIT_COMMIT_SHA
  For example: 1.0.25-r8facc21

Once the build version is calculated, the CI/CD pipeline does the following:

- The current Git Commit is tagged with the build version and pushed to GitHub.
- The project configuration file is updated with the build version, but this change is not committed or pushed to GitHub. It is done for the remaining tasks of the pipeline only.
- The build version is used to tag the container image built by the pipeline.

### Why are there 3 images on push?!

There is one image, but multiple tags point to the same image. At the core, semantic versioning allows you to automatically manage the risk of breaking your software by baking information about relative risk into the version number.

To provide flexibility in how users can reference and pull the image, multiple tags are pushed that reference the same image. For example, these all refer to the same image:

- 1.0.25
- 1.0
- 1

This makes it easier for users to reference different levels of specificity in the versioning scheme. This can also be useful for compatibility with other tools or systems that may only accept certain formats of version tags. For more about semver, check out npm's [blog post](https://blog.npmjs.org/post/162134793605/why-use-semver.html).
