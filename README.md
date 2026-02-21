# Helm GitHub Pages Action

Publish Helm charts to GitHub Pages.

This will publish Helm charts and the Helm repository index to GitHub Pages.

## Example workflows

Your GitHub Pages branch (default `gh-pages`) must already exist in the repository.

```yaml
name: Publish
on:
  push:
    branches:
      - main

env:
  COMMIT_USER: helm-chart-bot

jobs:
  helm-chart:
    name: Publish chart
    runs-on: ubuntu-24.04
    permissions:
      # Requires write permission to push to the GitHub Pages branch
      contents: write

    steps:
      - name: Checkout
        uses: actions/checkout@v6

      - name: Publish to gh-pages
        uses: manics/helm-gh-pages-action@v0.1.1
        with:
          # Directory in this repository containing the Helm chart
          chart-dir: chart
```

Only publish tags, and automatically update the chart version

```yaml
      - name: Update Chart.yaml version with tag
        run: |
          export VERSION=${{ github.ref_name }}
          yq --inplace '.version = env(VERSION)' chart/Chart.yaml
          git diff

      - name: Publish to gh-pages
        uses: manics/helm-gh-pages-action@v0.1.1
        if: startsWith(github.ref, 'refs/tags/')
        with:
          chart-dir: chart
```

## Installing a published chart

```sh
helm repo add my-chart-repository https://USERNAME.github.io/REPOSITORY_NAME/
helm install RELEASE_NAME my-chart-repository/CHART_NAME ...
```
