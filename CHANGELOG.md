# Changelog

## 3.1.0

### Features

* PDF generation is supported using [docs2pdf](https://github.com/diplodoc-platform/docs2pdf)

## 3.0.0

### Features

* the upload logic has been removed from this action and moved to [docs-upload-action](https://github.com/diplodoc-platform/docs-upload-action)

### Migration from v2 to v3 action

#### Workflow with docs-build-action@v2

File: `.github/workflow/build.yml`

```yaml
name: Build

on:
  pull_request:

jobs:
  build-docs:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3
      - name: Build
        uses: diplodoc-platform/docs-build-action@v2
        with:
          revision: "pr-${{ github.event.pull_request.number }}"
          project-name: ${{ secrets.DIPLODOC_PROJECT_NAME }}
          src-root: "./docs"
          storage-bucket: ${{ secrets.DIPLODOC_STORAGE_BUCKET }}
          storage-endpoint: ${{ vars.DIPLODOC_STORAGE_ENDPOINT }}
          storage-access-key-id: ${{ secrets.DIPLODOC_ACCESS_KEY_ID }}
          storage-secret-access-key: ${{ secrets.DIPLODOC_SECRET_ACCESS_KEY }}
          storage-region: ${{ vars.DIPLODOC_STORAGE_REGION }}
```

#### Workflow with docs-build-action@v3

File: `.github/workflows/build.yml`
**Note**: Be careful and approve the start of this workflow for PR from forks if there are no suspicious changes

```yaml
name: Build

on:
  pull_request:
    paths: 'docs/**'
    types: [opened, synchronize]

jobs:
  build-docs:
    runs-on: ubuntu-latest
    permissions: write-all
    steps:
      - name: Checkout
        uses: actions/checkout@v3

      - name: Build
        uses: diplodoc-platform/docs-build-action@v3
        with:
          revision: "pr-${{ github.event.pull_request.number }}"
          src-root: "./docs"
```

File: `.github/workflows/post-build.yml`
**Note**: The configuration with two workflow files allows to run a build for PR from forks. Because `.github/workflows/post-build.yml` workflow will have access to the repository secrets.

```yaml
name: Upload & Message

on:
  workflow_run:
    workflows: [Build]
    types:
      - completed

jobs:
  post-build:
    permissions: write-all
    runs-on: ubuntu-latest
    steps:
      - name: Upload
        uses: diplodoc-platform/docs-upload-action@v1
        if: github.event.workflow_run.conclusion == 'success'
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          storage-endpoint: ${{ vars.DIPLODOC_STORAGE_ENDPOINT }}
          storage-region: ${{ vars.DIPLODOC_STORAGE_REGION }}
          storage-bucket: ${{ vars.DIPLODOC_STORAGE_BUCKET }}
          storage-access-key-id: ${{ secrets.DIPLODOC_ACCESS_KEY_ID }}
          storage-secret-access-key: ${{ secrets.DIPLODOC_SECRET_ACCESS_KEY }}

      - name: Comment message
        uses: diplodoc-platform/docs-message-action@v1
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          project-link: ${{ vars.DIPLODOC_PROJECT_LINK }}
```


