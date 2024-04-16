# AdoreMe GitHub Actions reusable workflows

## ❓ What is it?
This repository contains reusable workflows for GitHub Actions. 

If you want to find more about this you can check GitHub Actions [documentation](https://docs.github.com/en/actions/learn-github-actions/reusing-workflows).

Its scope is to make it easier to create and share workflows by avoiding the need to write the same code over and over again in different app repositories.

## 🔧 How do I use it?
In your own GitHub Actions workflow, you can import the reusable workflows found in [.github/workflows](.github/workflows) by referencing it like in the example below:
```yaml
name: 'CD'
on:
  push:
    branches: [main, master]
    paths-ignore:
      - '...'
jobs:
  cd:
    uses: adore-me/gha-workflows/.github/workflows/reusable-cd.yaml@v0.0.22 # <-- Import the reusable workflow
    with:
      backup: true
      prod_gcp_project_id: SOME_PROJECT_ID
    secrets:
      QUAY_USER: ${{ QUAY_USER }}
      QUAY_PASSWORD: ${{ QUAY_PASSWORD }}
      GCR_CREDENTIALS: ${{ GCR_CREDENTIALS }}
```
## 📖 Table of contents

- [Reusable PHP CD Workflow](#reusable-php-cd-workflow)
- [Reusable PHP Lib Workflow - Help Comment](#reusable-php-lib-workflow---help-comment)
- [Reusable PHP Lib Workflow - Static Checks](#reusable-php-lib-workflow---static-checks)
- [Reusable PHP Lib Workflow - Tests](#reusable-php-lib-workflow---tests)
- [Reusable JS CI Workflow](#reusable-js-ci-workflow)
- [Release Workflow](#release-workflow)
- [Reusable JS CD Workflow](#reusable-js-cd-workflow)
- [Reusable JS Canary Build Workflow](#reusable-js-canary-build-workflow)
- [Reusable Cleanup Build Cache Workflow](#reusable-cleanup-build-cache-workflow)
- [Reusable Promote Workflow](#reusable-promote-workflow)
- [Reusable PHP Canary Build Workflow](#reusable-php-canary-build-workflow)
- [Sync Env Workflow](#sync-env-workflow)

## 📖 Contents

### [Reusable PHP CD workflow](.github/workflows/php-reusable-cd.yaml)

This workflow should be used to promote an `rc` image to a `prod` image. 

#### Inputs
- `prod-gcp-project-id`: The GCP project ID where the `prod` image backup will be pushed.
  - `type`: **string**
  - `default`: **none**
  - `required`: **true**
- `php-image-name`: The name of the PHP image to be used.
  - `type`: **string**
  - `default`: **php**
  - `required`: **false**
- `composer-self-update`: If true, composer will be updated before running the `composer install` command.
  - `type`: **boolean**
  - `default`: **false**
  - `required`: **false**
- `composer-self-update-version`: The version of composer to be used when `composer-self-update` is set to `true`.
  - `type`: **string**
  - `default`: **''**
  - `required`: **false**
- `create-jira-tickets`: If true, a Jira ticket will be created when a release is created.
  - `type`: **boolean**
  - `default`: **true**
  - `required`: **false**
- `slack-channel`: Slack channel for error notifications.
  - `type`: **string**
  - `default`: **''**
  - `required`: **true**
#### Secrets
- `APP_ID`: GitHub Actions App ID
  - `required`: **true**
- `GH_KEY`: GitHub Actions App Private Key
  - `required`: **true**
- `QUAY_USER`: The username to use when authenticating with the Quay registry.
  - `required`: **true**
- `QUAY_PASSWORD`: The password to use when authenticating with the Quay registry.
  - `required`: **true**
- `GCR_CREDENTIALS`: The GCR credentials to use when pushing the `prod` image to the backup registry.
  - `required`: **true**
- `JIRA_USER`: Jira username.
  - `required`: **true**
- `JIRA_PASSWORD`: Jira password.
  - `required`: **true**
- `JIRA_URI`: Jira URI.
  - `required`: **true**
- `SLACK_WEBHOOK`: Slack webhook URL.
  - `required`: **true**

#### Jobs
- `release`: Creates the GitHub release, builds the image and pushes it to Quay and GCR.
- `create-jira-ticket`: Creates a Jira ticket for the release.
  - depends on `release` and `${{ inputs.create-jira-ticket }}`

### [Reusable PHP Lib workflow - Help Comment](.github/workflows/php-lib-reusable-help-comment.yaml)

This workflow should be used to publish a comment regarding versioning

#### Secrets
- `APP_ID`: GitHub Actions App ID
  - `required`: **true**
- `GH_KEY`: GitHub Actions App Private Key
  - `required`: **true**


#### Jobs
- `comment`: Creates a comment on a GitHub PR regarding versioning practice for php libraries.

### [Reusable PHP Lib workflow - Static Checks](.github/workflows/php-lib-reusable-static-checks.yaml)

This workflow should be used to run static checks on a PHP library.

#### Inputs
- `php-image`: PHP image to be used.
  - `type`: **string**
  - `default`: **''**
  - `required`: **false**
- `run-phpcs`: Run PHP Checkstyle.
  - `type`: **string**
  - `default`: **'true'**
  - `required`: **false**
- `phpcs-standard`: PHP checkstyle standard to use, if run-phpcs is true.
  - `type`: **string**
  - `default`: **'PSR2'**
  - `required`: **false**
- `run-phpmd`: Run PHP MD.
  - `type`: **string**
  - `default`: **'true'**
  - `required`: **false**
- `phpmd-app-dir`: PHP MD application directory - add separate folders by using comma. When empty will try to guess if is `src` or `app`.
  - `type`: **string**
  - `default`: **''**
  - `required`: **false**
- `run-phpcpd`: Run PHP CPD.
  - `type`: **string**
  - `default`: **'true'**
  - `required`: **false**
- `run-phpcpd`: Run PHP Stan.
  - `type`: **string**
  - `default`: **'true'**
  - `required`: **false**
- `reports-dir`: Reports base directory.
  - `type`: **string**
  - `default`: **'build/reports'**
  - `required`: **false**
- `app-dir`: Application directory - use without trailing slash. When empty will try to guess if is `src` or `app`.
  - `type`: **string**
  - `default`: **'build/reports'**
  - `required`: **false**
- `run-rector:`: Run PHP Rector.
  - `type`: **string**
  - `default`: **'false'**
  - `required`: **false**
- `run-laravel-pint:`: Run Laravel Pint.
  - `type`: **string**
  - `default`: **'false'**
  - `required`: **false**
#### Secrets
- `APP_ID`: GitHub Actions App ID
  - `required`: **true**
- `GH_KEY`: GitHub Actions App Private Key
  - `required`: **true**
- `QUAY_USER`: The username to use when authenticating with the Quay registry.
  - `required`: **true**
- `QUAY_PASSWORD`: The password to use when authenticating with the Quay registry.
  - `required`: **true**

#### Jobs
- `static-checks`: Runs static checks on a PHP library.

### [Reusable PHP Lib workflow - Tests](.github/workflows/php-lib-reusable-tests.yaml)

This workflow should be used to run tests on a PHP library.

#### Inputs
- `reports-dir`: Reports base directory.
  - `type`: **string**
  - `default`: **'build/reports'**
  - `required`: **false**
- `phpunit-report-file-name`: PHPUnit report file name.
  - `type`: **string**
  - `default`: **'phpunit-junit.xml''**
  - `required`: **false**
- `enable-mysql`: Enable MySQL.
  - `type`: **string**
  - `default`: **'false'**
  - `required`: **true**
- `run-migrations`: Run migrations.
  - `type`: **string**
  - `default`: **'true'**
  - `required`: **true**
- `run-seeds`: Run seeds.
  - `type`: **string**
  - `default`: **'true'**
  - `required`: **true**
- `enable-redis`: Enable Redis.
  - `type`: **string**
  - `default`: **'false'**
  - `required`: **true**
- `enable-workers`: Enable Workers.
  - `type`: **string**
  - `default`: **'false'**
  - `required`: **true**
- `workers-conf-path`: Workers configuration file path.
  - `type`: **string**
  - `default`: **'ci/worker-confs/supervisor_dev_test_workers.conf'**
  - `required`: **false**
- `with-coverage`: Run coverage.
  - `type`: **string**
  - `default`: **'true'**
  - `required`: **true**
- `run-rector:`: Run PHP Rector.
  - `type`: **string**
  - `default`: **'false'**
  - `required`: **false**
- `coverage-report-file-name:`: Coverage report file name.
  - `type`: **string**
  - `default`: **'coverage-clover.xml'**
  - `required`: **false**
- `run-suites:`: Run specific suites. Pass suites as a comma separated list, no spaces (e.g. "UnitTests,IntegrationTests").
  - `type`: **string**
  - `default`: **''**
  - `required`: **false**
- `verbose:`: Run PHPUnit in verbose mode.
  - `type`: **string**
  - `default`: **'false'**
  - `required`: **true**
- `min-line-coverage:`: Minimum percentage acceptable for line coverage.
  - `type`: **number**
  - `default`: **0**
  - `required`: **false**
- `min-method-coverage:`: Minimum percentage acceptable for method coverage.
  - `type`: **number**
  - `default`: **0**
  - `required`: **false**
- `enable-rabbitmq:`: Enable RabbitMQ.
  - `type`: **string**
  - `default`: **'false'**
  - `required`: **false**
- `testing-env-file:`: .env.testing file is used
  - `type`: **string**
  - `default`: **'true'**
  - `required`: **false**
#### Secrets
- `APP_ID`: GitHub Actions App ID
  - `required`: **true**
- `GH_KEY`: GitHub Actions App Private Key
  - `required`: **true**
- `QUAY_USER`: The username to use when authenticating with the Quay registry.
  - `required`: **true**
- `QUAY_PASSWORD`: The password to use when authenticating with the Quay registry.
  - `required`: **true**

#### Jobs
- `tests`: Runs tests on a PHP library.

### [Reusable JS CI workflow](.github/workflows/js-reusable-ci.yaml)

This workflow should be used for front-end apps.

#### Secrets
- `NPM_TOKEN`: NPM token.
  - `required`: **true**
- `CYPRESS_CURRENTS_RECORD_KEY`: Cypress currents record key.
  - `required`: **true**

#### Jobs
- `read-config`: Read Config
- `build-application`: Build&Test Application
  - depends on `read-config`
- `cypress-run`: Cypress Tests
  - depends on `read-config` and `needs.read-config.outputs.cypressEnabled` to be true

### [Release workflow](.github/workflows/release.yaml)

This workflow should be used to create a GitHub release.

#### Jobs
- `release-on-push`: Creates a GitHub release on push.

### [Reusable JS CD workflow](.github/workflows/js-reusable-cd.yaml)

This workflow should be used to promote an `rc` image to a `prod` image.

#### Inputs
- `prod-gcp-project-id`: GCP project ID for production.
  - `type`: **string**
  - `default`: **none**
  - `required`: **true**
- `create-jira-tickets`: If true, a Jira ticket will be created when a release is created.
  - `type`: **boolean**
  - `default`: **true**
  - `required`: **false**
#### Secrets
- `APP_ID`: GitHub Actions App ID
  - `required`: **true**
- `GH_KEY`: GitHub Actions App Private Key
  - `required`: **true**
- `GH_OAUTH_TOKEN`: GitHub Token
  - `required`: **true**
- `QUAY_USER`: The username to use when authenticating with the Quay registry.
  - `required`: **true**
- `QUAY_PASSWORD`: The password to use when authenticating with the Quay registry.
  - `required`: **true**
- `GCR_CREDENTIALS`: The GCR credentials to use when pushing the `prod` image to the backup registry.
  - `required`: **true**
- `GCP_SA_CREDENTIALS`: The GCP credentials to use for GCP project.
  - `required`: **true**
- `JIRA_USER`: Jira username.
  - `required`: **true**
- `JIRA_PASSWORD`: Jira password.
  - `required`: **true**
- `JIRA_URI`: Jira URI.
  - `required`: **true**
- `SLACK_WEBHOOK`: Slack webhook URL.
  - `required`: **true**
- `NPM_TOKEN`: NPM token.
  - `required`: **true**

#### Jobs
- `release`: Creates the GitHub release, builds the image and pushes it to Quay and GCR.
- `create-jira-ticket`: Creates a Jira ticket for the release.
  - depends on `release` and `${{ inputs.create-jira-ticket }}`

### [Reusable JS Canary Build workflow](.github/workflows/js-reusable-canary-build.yaml)

This workflow should be used for js canary build

#### Inputs
- `app-version`: Application version to build (ex: v2.1.4-canary). Defaults to branch name.
  - `type`: **string**
  - `default`: **''**
  - `required`: **false**
#### Secrets
- `NPM_TOKEN`: NPM token.
  - `required`: **true**
- `QUAY_USER`: The username to use when authenticating with the Quay registry.
  - `required`: **true**
- `QUAY_PASSWORD`: The password to use when authenticating with the Quay registry.
  - `required`: **true**
- `GCR_CREDENTIALS`: The GCR credentials to use when pushing the `prod` image to the backup registry.
  - `required`: **true**
- `GCP_SA_CREDENTIALS`: The GCP credentials to use for GCP project.
  - `required`: **true**

#### Jobs
- `canary-build`: Builds image and pushes it to Quay and GCR.

### [Reusable Cleanup Build Cache workflow](.github/workflows/reusable-cleanup-build-cache.yaml)

This workflow should be used for cleanup cached build artifacts

#### Secrets
- `APP_ID`: GitHub Actions App ID
  - `required`: **true**
- `GH_KEY`: GitHub Actions App Private Key
  - `required`: **true**

#### Jobs
- `cleanup-cache`: Cleanup Cache

### [Reusable Promote workflow](.github/workflows/reusable-promote.yaml)

This workflow should be used to promote to PROD

#### Inputs
- `version`: Version to promote (ex: v1.0.3).
  - `type`: **string**
  - `default`: **''**
  - `required`: **false**
#### Secrets
- `QUAY_USER`: The username to use when authenticating with the Quay registry.
  - `required`: **true**
- `QUAY_PASSWORD`: The password to use when authenticating with the Quay registry.
  - `required`: **true**
- `GCR_CREDENTIALS`: The GCR credentials to use when pushing the `prod` image to the backup registry.
  - `required`: **true**

#### Jobs
- `promote`: Promote to PROD

### [Reusable PHP Canary Build workflow](.github/workflows/reusable-canary-build.yaml)

This workflow should be used for php canary build.

#### Inputs
- `app-version`: Application version to build (ex: v2.1.4-canary). Defaults to branch name.
  - `type`: **string**
  - `default`: **''**
  - `required`: **false**
- `php-image-tag`: PHP image version tag (ex: php-7.4.3-v1.0.0). Default to version in `ci/config.yaml`.
  - `type`: **string**
  - `default`: **''**
  - `required`: **false**
- `cache-routes`: Cache routes (laravel). Default to false.
  - `type`: **string**
  - `default`: **'false'**
  - `required`: **false**
#### Secrets
- `QUAY_USER`: The username to use when authenticating with the Quay registry.
  - `required`: **true**
- `QUAY_PASSWORD`: The password to use when authenticating with the Quay registry.
  - `required`: **true**
- `GCR_CREDENTIALS`: The GCR credentials to use when pushing the `prod` image to the backup registry.
  - `required`: **true**
- `GH_PRIVATE_ACTIONS_TOKEN`: Github token
  - `required`: **true**

#### Jobs
- `canary-build`: Builds image and pushes it to Quay and GCR.

### [Sync env workflow](.github/workflows/sync-env.yaml)

This workflow should be used to sync env files to kube-dev.

#### Inputs
- `enable`: Enable the workflow
  - `type`: **boolean**
  - `default`: **true**
  - `required`: **false**
#### Secrets
- `APP_ID`: GitHub Actions App ID
  - `required`: **true**
- `GH_KEY`: GitHub Actions App Private Key
  - `required`: **true**

#### Jobs
- `sync-env`: Sync env files to kube-dev.

