# AdoreMe GitHub Actions reusable workflows

## ❓ What is it?
This repository contains reusable workflows for GitHub Actions. 

If you want to find more about this you can check GitHub Actions [documentation](https://docs.github.com/en/actions/learn-github-actions/reusing-workflows).

It's scope is to make it easier to create and share workflows by avoiding the need to write the same code over and over again in different app repositories.

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

## 📖 Contents

### [Reusable CD workflow](.github/workflows/reusable-cd.yaml)

This workflow should be used to promote an `rc` image to a `prod` image. 

#### Inputs
- `backup`: If true, the `prod` image will be also pushed to the backup registry (GCR for now).
  - `type`: **boolean**
  - `default`: **true**
  - `required`: **false**
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
- `create-jira-ticket`: If true, a Jira ticket will be created when a release is created.
  - `type`: **boolean**
  - `default`: **true**
  - `required`: **false**
#### Secrets
- `GH_OAUTH_TOKEN`: GitHub Token
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
- `DEPLOYSBOT_HEADER`: Deploysbot header.
  - `required`: **true**
- `DEPLOYSBOT_HEADER_VALUE`: Deploysbot header value.
  - `required`: **true**

#### Jobs
- `release`: Creates the GitHub release, builds the image and pushes it to Quay and GCR.
- `create-jira-ticket`: Creates a Jira ticket for the release.
  - depends on `release` and `${{ inputs.create-jira-ticket }}`

### Maybe more to come...
