Add/Rename certain scripts in the `package.json`.

The npm static site pipelines have one optional and four required scripts that must be defined in `package.json`. Those scripts are:

- `compile` (optional): This script is designated for Node projects requiring some type of compile / build step such as with TypeScript, Next.js, WebPack, or minifier tools.
- `unit-test` (required): This script must run your unit tests. For example, your script might look like `nyc ava --no-color --tap`.
- `lint` (required): This script is intended to run the project's linter. For example, your script might look like `eslint . --ext .js`.
- `package` (required): This script must generate the static site into a **distribution directory** such as `build`, `public`, or `dist` at the root of your repo. The directory used should match the `distribution-directory` specified in your repository configuration (`build.yml`) file.
- `e2e` (required): This script confirms your running application is fully running, including it's dependencies. Sometimes called an integration test, this script is run in the pipelines during the "deploy-verification" steps.

The following is an example of the scripts section of the `package.json`. You should replace the values of these scripts with the tools you're using for your project.

```json
"scripts": {
  "compile": "tsc", // not required
  "unit-test": "nyc ava --no-color --tap",
  "lint": "eslint . --ext .js",
  "package": "npm run build",
  "e2e": "sh e2e.sh",
}
```

If you do not currently write unit tests, lint or e2e, you may write a placeholder such as `echo 'todo test'`.

Simple `e2e.sh` script that verifies the deployed site responds with expected content:

```bash
#!/usr/bin/env bash

set -o pipefail

echo "Checking that site is running on the ${APP_ENV} environment"
curl -f "http://${APP_HOSTNAME}:${APP_PORT}/" | grep "My Static Site Content"
```
