Add/Rename certain scripts in the `package.json`.

The Node.js pipelines have one optional and four required scripts that must be added to the `package.json`. Those scripts are:

- `compile` (optional): This script is designated for Node projects requiring some type of compile / build step such as with TypeScript, Next.js, WebPack, or minifier tools.
- `unit-test` (required): This script should begin your unit tests using this script. For example, your script might look like `nyc ava --no-color --tap`.
- `lint` (required): This script is intended to run the project's linter. For example, your script might look like `eslint . --ext .js`.
- `e2e` (required\*): This script confirms your running application is fully running, including it's dependencies. Sometimes called an integration test, this script is run in the pipelines during the "deploy-verification" steps.

    \*CronJob and Job deployments do not run verification tests and therefore this script does not apply.
    
- `start` (required): This script is used as the entrypoint to the application when it is deployed.

The following is an example of the scripts section of the `package.json`. You should replace the values of these scripts with the tools you're using for your project.

```json
"scripts": {
  "compile": "tsc", // not required
  "unit-test": "nyc ava --no-color --tap",
  "lint": "eslint . --ext .js",
  "e2e": "npx nightwatch node_modules/nightwatch/examples/tests/ecosia.js",
  "start": "node ./index.js",
}
```

If you do not currently write unit tests, lint or e2e, you may write a placeholder such as `echo 'todo test'`.
