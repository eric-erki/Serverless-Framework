<!--
title: Serverless Dashboard - CI/CD
menuText: CI/CD
menuOrder: 7
layout: Doc
-->

<!-- DOCS-SITE-LINK:START automatically generated  -->

### [Read this on the main serverless docs site](https://www.serverless.com/framework/docs/dashboard/cicd/)

<!-- DOCS-SITE-LINK:END -->

# CI/CD

Serverless CI/CD enables you to automatically test and deploy services from Github.

## Requirements

Before you setup your CI/CD workflow, make sure you meet the following requirements:

1. **Deployment Profile must include an AWS Access Role**. When Serverless automatically deploys your service, it must be granted permission to your AWS account. This permission is granted by deploying to a stage which has an AWS Access Role configured in it’s deployment profile. This enables Serverless to automatically generate short-lived AWS Access Keys used to authenticate during the deployment. [Learn how to setup the AWS Access Role](/framework/docs/dashboard/access-roles/).
2. **Must have your Serverless Framework project checked into Github**. Currently only Github is supported as a VCS provider. Your project, including the serverless.yml file, must be checked into the repo.
3. **Must be deployed on AWS**. The dashboard currently only supports AWS as a cloud service provider. Other cloud service providers are a work in progress.
4. **Must use the Node or Python runtime**. Currently only Serverless Framework projects using the Node or Python runtimes are supported. Other runtimes are coming soon.

## Connect to Github

1. Select your application from the main dashboard.
2. Select the service you want to deploy or create a new one.
3. Select the “ci/cd settings” tab from the application page.
4. Select the service from the left-hand menu.
5. Click the "connect github repository" button to grant the Serverless Dashboard access to the repository.
6. After authenticate with Github you'll be asked to install the Serverless application in your Github organizations. In the "Repository access" section, ensure you select "All repositories", or that the intended repository is included if you select "Only select repositories".

## Configure the build settings

1. Select the Github repository from the "repository" dropdown. This must be a repository that contains one or more `serverless.yml` files.
2. Select the "base directory" containing the `serverless.yml` file. The repository must contain at least one `serverless.yml` and `service` value must match the current service in the dashboard.
3. Select the “region” for the deployments. Only regions supported by the Serverless Dashboard are currently listed. If you plan to deploy to another region, please reach out sales or support to request support for a new region.

## Running Tests

The Serverless Framework will automatically run tests for each deployment by running `npm test`. The tests must pass, return `0`, before the service is deployed. If the tests fail, then the service will not be deployed.

The tests only run if a `test` script is present in the `package.json` file, like in the example below:

```json
{
  "name": "my-serverless-project",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC"
}
```

The tests will be skipped if the `npm test` command returns `Error: no test specified`. This is the response from `npm` if no `test` script is defined. It is also the default value of the `test` script when you initialize a new package.json via `npm init`.

### Running Node tests

If you are using Node for your runtime, then all the dependencies will automatically be installed using `npm install` before tests are run.

Update the `tests` script to run your node test suite (e.g. `mocha`).

### Running Python tests

If you are using Python we recommend using the [serverless-python-requirements](https://github.com/UnitedIncome/serverless-python-requirements) plugin to install the dependencies from `requirements.txt`.

If you are not using the serverless-python-requirements plugin, then you can install the requirements by adding the `postinstall` script to `package.json`.

```yaml
{
  'name': 'demo-python',
  'version': '1.0.0',
  'scripts': { 'postinstall': 'pip3 install -r requirements.txt', 'test': 'pytest' },
  'devDependencies': { 'serverless-python-requirements': '^5.0.1' },
}
```

You must update the `test` script in `package.json` to run your Python tests suite (e.g. `pytest`).

## Custom scripts

Custom scripts in the pipeline are supported using the standard `scripts` in the `package.json` file.

For example, you can run scripts before/after install, and before/after a test.

```yaml
{
  'name': 'demo-serverless',
  'version': '1.0.0',
  'scripts': { 'preinstall': '', 'postinstall': '', 'pretest': '', 'posttest': '' },
}
```

Additional lifecycle hooks can be found in the `npm` documentation:

[https://docs.npmjs.com/misc/scripts](https://docs.npmjs.com/misc/scripts)

## Deployment settings

If your services depends on settings which are different for each stage, we recommend using [deployment profiles](/framework/docs/dashboard/profiles/) to set different [parameters](https://serverless.com/framework/docs/dashboard/secrets/) for each stage.
