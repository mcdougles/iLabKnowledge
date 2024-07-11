# Guide: Building and Deploying a Ruby application to Cirrus

{%include-markdown "./includes/community-supported.md"%}

!!! info

    Dedicated Slack channel for Ruby pipeline support: [#cio-cicd-ruby-pipeline](https://my.slack.com/archives/C05UPFX3GG7)

By the end of this guide, you should be able to deploy your Ruby application to Cirrus. Your repository should be set up such that every push will trigger a build and deploy. {% include-markdown "./includes/contact_us.md" %}

## Step 1: Complete Checklist

{%
    include-markdown "./includes/cirrus-complete-checklist.md"
%}

## Step 2: Install the GitHub Application

{%
    include-markdown "./includes/github-app-pipeline.md"
%}

{%
    include-markdown "./includes/github-app-installation.md"
%}

## Step 3: Generate a GitHub Access Token

{%
    include-markdown "./includes/github_access_token.md"
%}

## Step 4: Create your Project and Pipeline on Cirrus

{%
    include-markdown "./includes/cirrus-project-pipeline.md"
%}

## Step 5: Make Code Changes

1.  Ensure you commit the `Gemfile` and `Gemfile.lock` files. CIO CI/CD uses [bundler](https://bundler.io/) to install the packages as listed in these files. Packages cannot be installed with only a `Gemfile`.

1.  {%
       include-markdown "./includes/build_yml.md"
    %}
    {%
    include-markdown "./includes/optional_jira.md"
    %}

1.  {%
          include-markdown "./includes/apm-configuration.md"
    %}

1.  Choose a pipeline and fill into the **pipeline** field.

    - `ruby-v31-container-image` - for Ruby on Rails web-applications using Ruby v3.1

1.  Fill in the **cirrus-pipeline-name** with the name of your Pipeline in Cirrus.

1.  {%
       include-markdown "./includes/openshift-cli-access.md"
    %}

1.  {%
       include-markdown "./includes/cirrus_api_key.md"
    %}

1.  Encrypt the Cirrus API Username and Password.

    {%
      include-markdown "./includes/encryption.md"
    %}

    {%
        include-markdown "./includes/encrypt-each-repo.md"
    %}

1.  {%
       include-markdown "./includes/detect_secrets.md"
    %}

1.  Review the `build.yml` file.

    See the following example of the build.yml file for a ruby pipeline:

    ```yaml
    apiVersion: automation.cio/v1alpha1
    kind: RepositoryConfig
    ealImapNumber: xxxxxx
    build:
      strategy: cirrus-deployment
      pipeline: ruby-v31-container-image
      version: v1
      config:
        cirrus-project-name: cirrus-project-demo
        cirrus-pipeline-name: cirrus-pipeline-demo
        service-port: '3000'
        cirrus-region: us-south2
        cirrus-api-username: 'encrypted:v1:AAABJiD8bVgJb64QpnlTH4+9kugiiKi4QHBbpGOzng0GfOxi3Zn5XLspBMNBNZqkzuaKWFHg3v3qXNkzECZ4FSAnpN/km73bB3C/jWh/Wf0TCVjnXgyWh2aFZKgaeXhRJ5ZTAQhQ9a6oS8/4gwpBeocrutkHOVklA0O1SwtvQcqO9iMGWUGzxGG76kZkK0wFhQ=='
        cirrus-api-password: 'encrypted:v1:AAABJiD8bVhCQXY13u8/xa4B4zbyxbKa6Q5E9eorK2hr0WR1cg7Chnup8eawiOzyIcJh/7IXhEwal25WkAINtPF9tFoeQQCvEaHKPRGyHl517M6aeNf86RWBtsQILO5/9Ih1F04zcn+A1d8H3ijucSPU5nIG7x2GP3TDriOPiHBwmLzk/jtDEeVSqgkop9H7iw916iTalzmRahx77s060xvj4SyBIBU4IYWn0N/Qcg=='
        deploy-verification-memory-request: q512mb
        ruby-precompile: 'true'
        ruby-run-yarn: 'false'
        sidekiq: 'true'
        ruby-cronjobs: 'true'
    ```

    The `ruby-precompile` param is used to include/exclude the `rake-precompile` task in the pipeline. This task will execute the `assets:precompile` rake task. If the `ruby-run-yarn` param is `true`, it will also install node packages and run the `build` script defined in your `package.json` file using `yarn` before executing the `assets:precompile` rake task.

    The prerequisites of this task is to have the `rake` Ruby gem installed in your project. If the value of the `ruby-run-yarn` param is `true`, you also have to make sure that you have the `yarn.lock` and `package.json` files committed and the `build` script declared in your `package.json` file.

    It's recommended to use `jsbundling` instead of `webpacker` for building your javascript code (if there is any) in your project. The reason for that is that during the `assets:precompile` rake task if you use `webpacker`, some `yarn` scripts will be executed. Since the `assets-precompile` step is running on a Ruby base image where neither `yarn` nor `node` is installed, if `yarn` will be called due to `webpacker`, the pipeline build process will eventually fail. Using `jsbundling` it's easier to prevent running javascript code by including the following script in your `Rakefile`:

    ```ruby
    if Rake::Task.task_defined?('assets:precompile')
        precompile_task = Rake::Task['assets:precompile']
        precompile_task.prerequisites.delete('javascript:build')
    end
    ```

    If you can configure `webpacker` to not execute javascript code during the `assets:precompile` rake task, you are all set.

    If you decide to migrate from `webpacker` to `jsbundling` please follow [this guide](https://github.com/rails/jsbundling-rails/blob/main/docs/switch_from_webpacker.md).

    The `sidekiq` param triggers the deployment of a Sidekiq MQ server beside the web-application. Sidekiq will be configured using `config/sidekiq.yml`. For Sidekiq deployment customization you can create a `deploy-sidekiq` folder in the root of your project. Beside the naming of the folder, it works exactly the same as a normal deployment customization described in the [Cirrus Deployment Customization](cirrus-deployment-customization.md) section.

    The `ruby-cronjobs` param triggers a new deployment task that will send all cronjob manifest files defined in the `deploy-cronjobs` folder respectively to the environment to the OpenShift API server. The `deploy-cronjobs` folder should be located in the root of your project. Beside the naming of the folder, it works exactly the same as a normal deployment customization described in the [Cirrus Deployment Customization](cirrus-deployment-customization.md) section.

    Cronjob manifest example:

    ```yaml
    ---
    apiVersion: batch/v1
    kind: CronJob
    metadata:
      name: my-cronjob
    spec:
      suspend: false
      schedule: '0 7 * * 1-5'
      jobTemplate:
        spec:
          template:
            spec:
              containers:
                # should be always job
                - name: job
                  args:
                    # default container entrypoint = ['bundle', 'exec']
                    - rake
                    # the name of the rake task you want to execute as cronjob
                    - 'my-job'
                  envFrom:
                    # optional
                    - secretRef:
                        name: my-secret
    ```

{%
   include-markdown "./includes/optional_jira.md"
%}

### Ruby project prerequisites

1.  Create a `rbproject.toml` file to store your project metadata. At a minimum, you should provide version in this file.

    See the following example of a minimal `rbproject.toml`:

    ```toml
    name = "my-project"
    version = "0.1.0"
    description = "A ruby example project used for testing"
    authors = ["John Doe <john.doe@ibm.com"]
    ```

1.  The application will be started by executing the `bundle exec rails server -b 0.0.0.0` command respectively to the current `RAILS_ENV` which should be added as a secret in Cirrus.

1.  {%
       include-markdown "./includes/deployment-testing.md"
    %}

1.  {%
       include-markdown "./includes/health-check.md"
    %}

1.  Follow the steps outlined in [Cirrus Deployment Customization](cirrus-deployment-customization.md).

## Step 6: Push Code and Wait for Build

{%
  include-markdown "./includes/push-code-wait.md"
%}

{%
  include-markdown "./includes/deployment-verification-pods.md"
%}

{%
  include-markdown "./includes/contact_us.md"
%}

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

- For the `bundle-install` task make sure that you have committed the `Gemfile` and `Gemfile.lock` files in the root folder of your project.
- If you want to include the `rake-precompile` task in your pipelinerun with `ruby-run-yarn` set to `true`, make sure you have committed the `package.json` and `yarn.lock` files in the root folder of your project.
- The `rake` Ruby gem is used during the `rake-precompile` task. If you want this task to be executed, make sure it's installed in your project.
- The `rspec` Ruby gem is used to run tests on your code base. Make sure it's installed in your project.
- The `rubocop` Ruby gem is used for linting your code base. Make sure it's installed in your project.
- The `rbproject.toml` file is used to store metadata of the project including the version. This file is also required for the `git-version` task.
- For launching a Sidekiq MQ server additionally, please commit `config/sidekiq.yml` and make sure you have the `sidekiq` gem installed in your project.
- The Dockerfiles we use are [stored here](https://github.ibm.com/cio-ci-cd/pipeline-catalog/tree/main/catalog/official/tasks/buildah/dockerfiles/ruby).
- Need to configure secrets or environment variables for your application? See the [Environment Variables](cirrus-deployment-customization.md#environment-variables) and [Secrets](cirrus-deployment-customization.md#secrets) documentation to learn more.
