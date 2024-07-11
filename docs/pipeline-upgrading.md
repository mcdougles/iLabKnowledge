# How to Upgrade your Pipeline

## Recommended Version

For important information about end-of-life support for Node.js versions, see the [Node.js release schedule](https://nodejs.org/en/about/releases/).

## Process

1. Upgrade from the deprecating version. All CIO CI/CD pipelines follow a naming convention with the version in the pipeline name. For most cases, simply change the **16** to **18**. In node18 for ts, we removed the `ts` in node pipelines. You can just use `nodejs-v18-npm-container-image` and it'll run `compile` if you have a `compile` script

See the following articles about [node.js v18](https://nodejs.org/en/blog/announcements/v18-release-announce)

1. You will most likely need to run `npm install` to update your lock file, after running the latest node / npm required for the version.
1. Confirm that the app builds and continues to run successfully after the change. If necessary, make any updates/changes to resolve build/runtime issues.
1. Apply the changes to your default branch.
1. Do one more test to confirm everything went smoothly.

{% include-markdown "./includes/contact_us.md" %}
