# Guide: Building and Deploying to GitHub Pages using Python tools

By the end of this guide, you should be able to deploy documentation to GitHub Pages using python tools. Your repository will be configured so that builds happen on every feature branch, and deploys happen once code has been merged to the default branch. {% include-markdown "./includes/contact_us.md" %}

## Step 1: Complete Checklist

Make sure the following are true before proceeding:

- Your static assets are built using python based tools.
- [Register for an Enterprise Application Library (EAL) account](https://w3.ibm.com/enterprise-application-library). You will need the created IMAP number later (EAL-XXXXXX).

## Step 2: Install the GitHub Application

{%
    include-markdown "./includes/github-app-pipeline.md"
%}

{%
    include-markdown "./includes/github-app-installation.md"
%}

## Step 3: Make Code Changes

1.  Ensure you commit either a **Pipfile.lock**, **poetry.lock**, or **requirements.txt** file. CIO CI/CD uses micropipenv to install the packages as listed in these files. Packages cannot be installed with only a Pipfile.

1.  Create a `build.yml` file in the root of the project with the following structure:
    ```yaml
    apiVersion: automation.cio/v1alpha1
    kind: RepositoryConfig
    ealImapNumber: xxx
    build:
        strategy: cirrus-deployment
        pipeline: python-v39-micropipenv-github-pages
        version: v1
        config: 
            distribution-directory: # Optional value. Set this to the directory of your static assets once built.
            github-pages-branch: # Optional value. Set this to the branch you're using for GitHub pages. Defaults to gh-pages.
    ```

1.  {%
          include-markdown "./includes/apm-configuration.md"
    %}

1.  Choose a pipeline and fill into the **pipeline** field.

    - `python-v39-micropipenv-github-pages` - static assets built on Python 3.9

1.  {%
       include-markdown "./includes/detect_secrets.md"
    %}

1.  {%
       include-markdown "./includes/optional_jira.md"
    %}

1.  Create a `pyproject.toml` file to store your project metadata. At a minimum, you should provide the name and version in this file. If your project uses poetry, then poetry creates this file automatically. More information about the `pyproject.toml` file can be found at: [PEP 621 -- Storing project metadata in pyproject.toml](https://www.python.org/dev/peps/pep-0621/)

    See the following example of a minimal `pyproject.toml`:

    ```toml
    [tool.poetry]
    name = "poetry-example"
    version = "0.1.0"
    description = "A poetry example project used for testing"
    authors = ["Michael Ramos <ramosm@us.ibm.com>"]
    ```
    
1.  Create a `package.sh` file to generate the HTML for the Github pages site. The generated HTML should end up in the directory specified as `distribution-directory` in the `build.yml` (step 2 above). The script **must be executable** (i.e, `chmod +x package.sh`).
    See the following example of a minimal `package.sh` for Mkdocs users; the example will output generated HTML to the `site` directory. Accordingly, `site` should be configured as the `distribution-directory` in the pipeline `build.yml`. 

    ```sh
    set -e
    
    mkdocs build
    ```

Optional:

1. CIO CI/CD uses `pytest` to run unit-tests, you can configure pytest by adding configuration options to a `pytest.ini` file or to the `pyproject.toml`.
1. CIO CI/CD uses `flake8` for linting, you can configure `flake8` by adding a `.flake8` file to your repository.

## Step 4: Push Code and Wait for Build

{%
  include-markdown "./includes/push-code-wait.md"
%}

{%
  include-markdown "./includes/contact_us.md"
%}

## Step 5: Enable GitHub Pages on your Repository

An admin of your repository should enable GitHub Pages under the settings for your documentation repository. To enable GitHub Pages, follow this guide:

1. Under the Settings tab of a repository, scroll down to the GitHub Pages section.
1. Under the Source section, select the `gh-pages` branch. <TODO this needs to move to after the pipeline runs>
1. Click the Save button.

GitHub will display a message containing the link you can use to access the GitHub Pages site.

## Sonarqube Integration

{%
  include-markdown "./includes/sonarqube.md"
%}

## Optional - Set Up Slack Notifications

{%
  include-markdown "./includes/slack-notifications.md"
%}

## Optional - SonarQube Access and Boarding

See the [Sonarqube](./sonarqube.md) page for detailed information.

## Notes

- For installation, make sure you have either a `requirements.txt`, `Pipfile.lock`, or `poetry.lock`. Other dependency managers are not supported at this time, but that could be a potential future improvement.
- The `pytest` is used to run unit-tests.
- Currently `flake8` is used to run linting, but the plan is to move to `Black` soon.
- The `pyproject.toml` file is used to manage metadata about the project including version.
- Need to configure secrets or environment variables for your application? See the [Environment Variables](cirrus-deployment-customization.md#environment-variables) and [Secrets](cirrus-deployment-customization.md#secrets) documentation to learn more.
