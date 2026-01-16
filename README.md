# Tag Release Action

A GitHub Action that finds the most recent commit with a built Docker image in AWS ECR and tags it with a semantic version tag.
This is used in the k8s deploy process

## What it does

This action searches through your recent commits to find the most recent one that has a Docker image built in ECR, then tags that image with your specified release version (e.g., `v1.0.0`).

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
| `dev_tag` | Dev tag found for the commit |
