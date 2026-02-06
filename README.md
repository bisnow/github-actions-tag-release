# Tag Release Action

A GitHub Action that finds the most recent commit with a built Docker image in AWS ECR and tags it with a semantic version tag.
This is used in the k8s deploy process

## What it does

This action searches through your recent commits (up to the last 20) to find the most recent one that has a Docker image built in ECR, then tags that image with your specified release version (e.g., `v1.0.0`). The action will also preserve any associated dev or RC tags when creating the release manifest.

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
      - uses: bisnow/github-actions-tag-release@main
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
| `ECR_REGISTRY` | AWS ECR registry URL | Yes | - |
| `aws-account` | AWS account to assume | No | `bisnow` |
| `aws-region` | AWS region where ECR repo is located | No | `us-east-1` |

## Outputs

| Output | Description |
|--------|-------------|
| `git_sha` | Git SHA that was tagged |
| `dev_tag` | Dev or RC tag found for the commit |

## Versioning

This action uses rolling major version tags. You can pin to:

- A specific version: `@v3.1.0` (exact, never changes)
- A major version: `@v3` (recommended, gets bug fixes and new features)

When a new semantic version tag (e.g., `v3.2.0`) is pushed, a GitHub Actions workflow automatically updates the corresponding major version tag (`v3`) to point to the new release.