## [3.1.10](https://github.com/diplodoc-platform/docs-build-action/compare/v3.1.9...v3.1.10) (2025-05-21)


### Bug Fixes

* add --inject-platform-agnostic-fonts flag ([659df3f](https://github.com/diplodoc-platform/docs-build-action/commit/659df3ffaa273f485d6bee3bf271129b5bb436bb))

## [3.1.9](https://github.com/diplodoc-platform/docs-build-action/compare/v3.1.8...v3.1.9) (2025-05-19)


### Bug Fixes

* merge sterr with stdout ([74efbdf](https://github.com/diplodoc-platform/docs-build-action/commit/74efbdfc2b66d8f0d9f4d41df80d648a7cf2033f))

## [3.1.8](https://github.com/diplodoc-platform/docs-build-action/compare/v3.1.7...v3.1.8) (2025-05-14)

## [3.1.7](https://github.com/diplodoc-platform/docs-build-action/compare/v3.1.6...v3.1.7) (2025-05-14)

## [3.1.6](https://github.com/diplodoc-platform/docs-build-action/compare/v3.1.5...v3.1.6) (2025-05-09)


### Bug Fixes

* for inputs include-hidden-files: true ([ec8ec57](https://github.com/diplodoc-platform/docs-build-action/commit/ec8ec57f1272a574beca2117114a3c39b0b33a15))

## [3.1.5](https://github.com/diplodoc-platform/docs-build-action/compare/v3.1.4...v3.1.5) (2025-04-28)


### Bug Fixes

* remove the usage of now obsolete `docs2pdf` package, replace it with `pdf-generator` ([fcb0fea](https://github.com/diplodoc-platform/docs-build-action/commit/fcb0fea7efab2e5c54d3b69218a8cd0fedf26c71))

## [3.1.4](https://github.com/diplodoc-platform/docs-build-action/compare/v3.1.3...v3.1.4) (2025-01-24)

## [3.1.3](https://github.com/diplodoc-platform/docs-build-action/compare/v3.1.2...v3.1.3) (2024-09-04)


### Bug Fixes

* upload-artifact include-hidden-files: true ([0e28509](https://github.com/diplodoc-platform/docs-build-action/commit/0e28509b037de756bce8d9247d32cbfa5f162cff))

## [3.1.2](https://github.com/diplodoc-platform/docs-build-action/compare/v3.1.1...v3.1.2) (2024-04-19)


### Bug Fixes

* handle non-existent .yfm file ([6cb4985](https://github.com/diplodoc-platform/docs-build-action/commit/6cb49857ad0d876bc969fc29ac5fef6e2be4a75d))
* make revision parameter optional ([0451230](https://github.com/diplodoc-platform/docs-build-action/commit/04512308ed175119ff98c04a95f017bc44244efd))

## [3.1.1](https://github.com/diplodoc-platform/docs-build-action/compare/v4.0.0...v3.1.1) (2024-01-30)


### Bug Fixes

* return changelog and change release workflow ([8921c37](https://github.com/diplodoc-platform/docs-build-action/commit/8921c37ac52b81133999b00d8a7fdac99e289a4e))

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