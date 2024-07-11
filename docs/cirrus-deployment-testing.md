# Cirrus Deployment Testing

After running the CI phase, CD pipelines deploy and test applications on Cirrus. This reference guide describes how your deployment tests (for example: e2e, functional, integration) are run in Cirrus and provides details on how your tests should connect to the applications.

Tests are run in every environment immediately following the application deployment. Tests are a critical phase of the CD pipeline and must pass for the pipeline to be successful. Your tests should ensure that your application is up and running in the current environment. As a developer, this should give you confidence that the deployment was successful. The CIO CI/CD team recommends that you write tests that ensure a server accepts requests on important routes or performs other checks that verify your application is running as expected.

## Deployment Testing

As described in the _Getting started_ guides for each platform, the CD pipeline builds a test image that runs tests that you provide. These tests are run in a dedicated container, separate from your application containers. On startup, the test images are configured to run platform-specific commands:

| Platform | Test command                                          |
| :------- | :---------------------------------------------------- |
| Node.js  | `npm run e2e`                                         |
| Java     | `mvn verify`                                          |
| Python   | `deploy-test.sh` or `deploy-test.py` (from your repo) |

It's up to you to implement the deployment tests that are appropriate for your application. Run the relevant command above to execute the test on the CD pipeline.

### Environment Variables

Deployment tests connect to an application running in Cirrus. The same tests are run in multiple environments/phases (`test`, `pre-production`, `production`), so the CD pipeline provides deployment-specific information in environment variables. Your tests should read these environment variables to connect to the target application.

| Name           | Description                                                  |
| -------------- | ------------------------------------------------------------ |
| `APP_ENV`      | Environment type (`test`, `pre-production`, or `production`) |
| `APP_HOSTNAME` | The application hostname                                     |
| `APP_PORT`     | The application http port on `APP_HOSTNAME`                  |

Note: The `APP_HOSTNAME` and `APP_PORT` are internal to your Cirrus project. Since the tests are also run inside your Cirrus project, they can reach your application using these internal names.

For example, the following `deploy-test.sh` script would display the environment and make a request to `/test` on the deployed application:

```bash
#!/usr/bin/env bash

echo "Calling /test on the ${APP_ENV} environment"
curl "http://${APP_HOSTNAME}:${APP_PORT}/test"
```

### Secrets

To mount environment variables into the verification image using secrets, use the following parameters in the config section of the `build.yml`:

| Name                                 | Description                                         |
| ------------------------------------ | --------------------------------------------------- |
| `deploy-verification-secret-test`    | The secret name for the test environment.           |
| `deploy-verification-secret-preprod` | The secret name for the pre-production environment. |
| `deploy-verification-secret-prod`    | The secret name for the production environment.     |

### Limits

Deployment tests are run in containers within your Cirrus project separate from your application containers. Like all containers in Cirrus, tests are subject to the quota of your Cirrus project and count against the quota while running. They are also subject to time and logging limits enforced by the CD pipeline. These limits are detailed below.

{% include-markdown "./includes/contact_us.md" %}

#### Memory

The tests run in Cirrus include [quota selection](https://pages.github.ibm.com/CIOCloud/cio-blog/cli/#creating-jobs) for memory. Your project must have these memory resources available for the tests to run.


The following are the default memory quota selections requested for each platform:

| Platform | Memory Quota |
| :------- | :----------- |
| Node.js  | 256 MB       |
| Java     | 1 GB         |
| Python   | 256 MB       |

To select a different memory quota for the verification job, use the `deploy-verification-memory-request` config param in the `build.yml`.

#### Time

Deployment tests are limited to 20 minutes. If the tests do not complete within 20 minutes, the test phase is considered failed. The logs displayed on the GitHub Checks page should indicate that the tests did not complete in time.

#### Log Output

The logs from your deployment tests are recorded and displayed on the GitHub Checks page. The logs show up to 1000 lines of test output.
