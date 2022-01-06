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

### [Reusable CD workflow](reusable_cd.yaml)

This workflow should be used to promote an `rc` image to a `prod` image. 

#### Inputs
- `backup`: If true, the `prod` image will be also pushed to the backup registry (GCR for now).
  - `type`: **boolean**
  - `default`: **true**
  - `required`: **false**
- `prod_gcp_project_id`: The GCP project ID where the `prod` image backup will be pushed.
  - `type`: **string**
  - `default`: **none**
  - `required`: **true**
#### Secrets
- `QUAY_USER`: The username to use when authenticating with the Quay registry.
  - `type`: **string**
  - `default`: **none**
  - `required`: **true**
- `QUAY_PASSWORD`: The password to use when authenticating with the Quay registry.
  - `type`: **string**
  - `default`: **none**
  - `required`: **true**
- `GCR_CREDENTIALS`: The GCR credentials to use when pushing the `prod` image to the backup registry.
  - `type`: **string**
  - `default`: **none**
  - `required`: **true**
#### Jobs
- `setup-tags`: Based on the last tag found on repo will construct and output a list of tags to be used in the next jobs.
  - `outputs`:
    - `tag`
    - `rc-tag`
    - `prod-tag`
    - `prod-tag-backup`
- `push-main-image`: Tag and push the `prod` image to the Quay registry.
  - depends on `setup-tags`
- `push-backup-image`: Tag and push the `prod` image to the backup registry (GCR for now).
  - depends on `setup-tags`
- `create-release`: Create a release in GitHub.
  - depends on `setup-tags` and `push-main-image`

### Maybe more to come...
