# Library - Go

{%
       include-markdown "./includes/community-supported.md"
%}

!!! info

    Dedicated Slack channel for Go pipeline support: [#cio-cicd-golang-pipeline](https://my.slack.com/archives/C04RWLDTVL3)

## Guide: Building and Publishing a Go Library or Binary

The library pipeline builds your Go module (library) or binary and publishes it as part of a [GitHub Release](https://docs.github.com/en/repositories/releasing-projects-on-github/about-releases).

- If publishing a module, Go applications can use it as a dependency.
- If publishing a binary, note that this pipeline can be used to publish multiple as part of the same release.

Note that for this pipeline, _**no code is deployed to Cirrus**_.

## Step 1: Install the GitHub Application

{%
    include-markdown "./includes/github-app-pipeline.md"
%}

{%
    include-markdown "./includes/github-app-installation.md"
%}

## Step 2: Make Code Changes

### Required Steps

1.  Make sure the `go.mod` and `go.sum` (generated with `go mod tidy`) files have been added to the repository. You can find examples [here](https://github.ibm.com/cio-ci-cd-test/go-library/blob/main/go.mod) and [here](https://github.ibm.com/cio-ci-cd-test/go-library/blob/main/go.sum).

2.  Add a `Makefile` file to the root of your repository.

    {%
        include-markdown "./includes/golang-makefile-example.md"
    %}

3.  Add/rename certain targets in the `Makefile`. The GoLang library pipeline executes the `build`, `unit-test`, `lint`, and `version` Makefile targets during the build. You should replace the scripts for these targets with the tools you're using for your project.

    !!! note

        You may write a placeholder, such as `echo 'todo'` if you do not currently write unit tests and lint.

    !!! note

        The `version` target is only used for versioning binaries (the `BUILD_VERSION` environment variable contains the version number). If publishing a Go module, you should write a placeholder such as `echo 'skip'`.

4. 
    
    {%
        include-markdown "./includes/golang-version-example.md"
    %}

    !!! warning

        It's important to include the leading `v` because GoLang only supports the `vX.Y.Z` versioning format, `X.Y.Z` on its own is unsupported. The Go module cannot be installed as a dependency if its versioning deviates from the standard.

5.  Create a `build.yml` file in project root with the following structure.

    ```yaml
    apiVersion: automation.cio/v1alpha1
    kind: RepositoryConfig
    ealImapNumber: xxxxxx
    build:
      strategy: cirrus-deployment
      pipeline: golang-v1-library
      version: v1
    ```

    {%
        include-markdown "./includes/optional_jira.md"
    %}

6.  Choose a pipeline and fill into the **pipeline** field.

    {%
        include-markdown "./includes/golang-library.md"
    %}

7.  {%
       include-markdown "./includes/detect_secrets.md"
    %}

## Step 3: Push Code and Wait for Build

{%
  include-markdown "./includes/push-code-wait.md"
%}

## Step 4: Release

Once the pipeline runs successfully, a GitHub release will be created:

![GitHub Screen Capture](images/golang-library.png)

If you are using the pipeline to publish binaries instead of a module, the binaries will be included as release assets.

## Using the Published Module in Another Go Project

### Locally

If you published a module, this is how you can use it in another Go project locally:

1.  Enable the Go module system:
    ```bash
    GO111MODULE=on
    ```
2.  Allow for fetching modules from IBM's GHE instance:
    ```bash
    go env -w GOPRIVATE=github.ibm.com
    ```
3.  `cd` into the Go project you want to import this module into
4.  Install the module, you can optionally specify a version tag. For example:
    ```bash
    go get github.ibm.com/cio-ci-cd-test/go_library@v0.2.0
    ```
5.  Import the module with:

    ```golang
    import (
        "fmt"
        "os"

        "github.ibm.com/cio-ci-cd-test/go_library/greetings"
    )
    ```

6.  Then you can use it in your code, for example:

    ```golang
    func main() {
        if len(os.Args) <= 1 {
            fmt.Println("Please pass in a name argument.")
            os.Exit(1)
        }

        name := os.Args[1]

        fmt.Println(greetings.Hello(name))
    }
    ```

### Private Dependencies

{%
    include-markdown "./includes/golang-private-dependencies.md"
%}

## Examples

- [This repo publishes a module using the GoLang library pipeline](https://github.ibm.com/cio-ci-cd-test/go_library).

- [This repo publishes a binary using the GoLang library pipeline](https://github.ibm.com/cio-ci-cd-test/go-binary).


## Optional - Set Up Slack Notifications

{%
    include-markdown "./includes/slack-notifications.md"
%}
