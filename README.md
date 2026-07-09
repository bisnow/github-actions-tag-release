# Tag Release Action

A GitHub Action that finds the most recent commit with a built Docker image and tags it with a semantic version tag.
Works with both **AWS ECR** and **Harbor** (`harbor.bisnow.cloud`). This is used in the k8s deploy process.

## What it does

This action searches through your recent commits (up to the last 20) to find the most recent one that has a Docker image built in the registry (ECR or Harbor), then tags that image with your specified release version (e.g., `v1.0.0`). The action will also preserve any associated dev or RC tags when creating the release manifest.

Provide **either** `ECR_REGISTRY` **or** `HARBOR_REGISTRY`. Set `only-harbor: true` to skip ECR/AWS entirely — the Harbor path authenticates via the `arc-cicd` robot on the self-hosted `arc-runners-bisnow*` runners, so it must run there.

## Usage

```yaml
name: Release

on:
  push:
    tags:
      - 'v*'

jobs:
  tag-release:
    runs-on: ubuntu-latest
    steps:
      - uses: bisnow/github-actions-tag-release@v1
        with:
          tag: ${{ github.ref_name }}
          ECR_REGISTRY: 123456789012.dkr.ecr.us-east-1.amazonaws.com/my-app
          aws-account: bisnow
          aws-region: us-east-1
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `tag` | Semantic version tag (e.g., v1.0.0) | Yes | - |
| `ECR_REGISTRY` | AWS ECR registry URL. Provide this **or** `HARBOR_REGISTRY`. | No | `''` |
| `HARBOR_REGISTRY` | Harbor registry URL (e.g. `harbor.bisnow.cloud/bisnow/my-app`). Provide this **or** `ECR_REGISTRY`. | No | `''` |
| `only-harbor` | Tag the release only in Harbor, skipping ECR/AWS entirely. Requires `HARBOR_REGISTRY`. | No | `false` |
| `aws-account` | AWS account to assume (ECR only) | No | `bisnow` |
| `aws-region` | AWS region where ECR repo is located | No | `us-east-1` |

### Harbor-only usage

Runs on the self-hosted `arc-runners-bisnow*` runners, which are pre-authenticated to Harbor via the `arc-cicd` robot. No AWS role is assumed.

```yaml
      - uses: bisnow/github-actions-tag-release@v1
        with:
          tag: ${{ github.ref_name }}
          HARBOR_REGISTRY: harbor.bisnow.cloud/bisnow/my-app
          only-harbor: true
```

## Outputs

| Output | Description |
|--------|-------------|
| `git_sha` | Git SHA that was tagged |
| `dev_tag` | Dev or RC tag found for the commit |

## Versioning

This action uses rolling major version tags. You can pin to:

- A specific version: `@v1.4.0` (exact, never changes)
- A major version: `@v1` (recommended, gets bug fixes and new features — includes Harbor support as of `v1.4.0`)

When a new semantic version tag (e.g., `v1.5.0`) is pushed, a GitHub Actions workflow automatically updates the corresponding major version tag (`v1`) to point to the new release.