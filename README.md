# Infrastructure as Code - Continuous Integration

Reusable GitHub Actions workflows for application repositories.

This repository provides one importable pipeline per project type, with smaller
reusable workflows underneath for build and security concerns.

## Project Pipelines

Use one of these from an application repository:

```yaml
name: CI

on:
  pull_request:
  push:
    branches: [main]
  workflow_dispatch:

permissions:
  attestations: write
  contents: read
  id-token: write
  packages: write
  pull-requests: read
  security-events: write

jobs:
  ci:
    uses: meyersa/iac-ci/.github/workflows/javascript-pipeline.yml@main
    with:
      image_name: my-app
    secrets:
      dockerhub_username: ${{ secrets.DOCKERHUB_USERNAME }}
      dockerhub_token: ${{ secrets.DOCKERHUB_TOKEN }}
```

For Python projects, use:

```yaml
jobs:
  ci:
    uses: meyersa/iac-ci/.github/workflows/python-pipeline.yml@main
    with:
      image_name: my-python-app
    secrets:
      dockerhub_username: ${{ secrets.DOCKERHUB_USERNAME }}
      dockerhub_token: ${{ secrets.DOCKERHUB_TOKEN }}
```

## Included Checks

- Docker metadata with `latest` on the default branch and branch-name tags on other branches.
- Docker Buildx cache using the GitHub Actions cache backend.
- Multi-platform image builds for `linux/amd64` and `linux/arm64`.
- Docker Scout CVE scanning, recommendations, SARIF upload, and optional SBOM generation.
- GitHub artifact attestations for pushed container images.
- Dependency review for pull requests.
- CodeQL for JavaScript/TypeScript or Python.

## Internal Workflows

- `.github/workflows/build-docker-image.yml`
- `.github/workflows/security-dependency-review.yml`
- `.github/workflows/security-codeql-javascript.yml`
- `.github/workflows/security-codeql-python.yml`
- `.github/workflows/javascript-pipeline.yml`
- `.github/workflows/python-pipeline.yml`

When this repository is tagged, update the internal workflow references from
`@main` to the release tag, such as `@v1`, and consume the project pipelines
with the same tag.
